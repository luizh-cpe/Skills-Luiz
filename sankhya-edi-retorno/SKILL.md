---
name: sankhya-edi-retorno
description: >
  Gera os dois entregáveis necessários para configurar o EDI bancário de RETORNO no Sankhya ERP a partir do
  PDF do layout CNAB do banco: (1) arquivo XML de retorno pronto para importar no módulo de EDI do Sankhya
  e (2) script SQL Server (INSERT puro) para popular a tabela TSIOBC com o catálogo de ocorrências bancárias.
  Use esta skill SEMPRE que o usuário mencionar: "retorno bancário", "EDI retorno", "ocorrências bancárias",
  "TSIOBC", "popular ocorrências", "configurar retorno no Sankhya", "layout retorno", "CNAB retorno",
  "novo banco retorno", "OcorrenciaBancariaRetorno", ou variações. O usuário SEMPRE recebe os 2 arquivos
  juntos em uma única execução. Esta skill é a contraparte de RETORNO da skill sankhya-edi-remessa.
---

# Gerador de XML + SQL de Retorno Bancário — Sankhya EDI

## Objetivo

A partir do PDF do layout CNAB de retorno do banco, gerar **simultaneamente** os 2 entregáveis que o
Sankhya exige para processar arquivos de retorno bancário:

1. **`Retorno_[NomeBanco].xml`** — layout de retorno para importar no módulo de EDI do Sankhya
2. **`Popular_TSIOBC_[NomeBanco].sql`** — script SQL Server com INSERTs na tabela `TSIOBC` para
   registrar as ocorrências bancárias e como o Sankhya deve tratá-las (baixar, conciliar, rejeitar, etc).

> Os 2 arquivos são complementares e **sempre entregues juntos**. O XML define ONDE estão os campos no
> arquivo .RET; o registro na TSIOBC define O QUE FAZER quando cada código de ocorrência aparecer.

---

## Pré-requisitos antes de gerar

Antes de produzir os arquivos, Claude deve coletar do usuário (ou inferir do PDF):

| Item | Como obter | Exemplo |
|---|---|---|
| **Nome do banco** | Capa/cabeçalho do PDF, ou perguntar | `MULTIPLIKE`, `DAYCOVAL`, `YAALEH` |
| **CODIGO da TSIOBC** | Perguntar ao usuário — é o ID da instância pai de OcorrenciaBancariaRetorno no Sankhya | `331100` (Multiplike), `341100` (Daycoval), `321100` (Yaaleh) |
| **SEQUENCIA da coluna Ocorrencia** | Determinada pela estrutura do XML (geralmente 4 para CNAB400 padrão; Multiplike usa 15) | `4` ou `15` |

Se o usuário não fornecer o `CODIGO` da TSIOBC pai, **pergunte explicitamente** — não invente. Diga
algo como: *"Qual o CODIGO desta configuração de retorno na TSIOBC do seu Sankhya? Você encontra
em Modelagem → Tabela TSIOBC → instância OcorrenciaBancariaRetorno."*

---

## Parte 1 — Estrutura do XML de Retorno

### Diferenças críticas vs. XML de remessa

O XML de retorno é **estruturalmente diferente** do de remessa. Não tente reaproveitar padrões da
skill `sankhya-edi-remessa`. Diferenças principais:

| Aspecto | Remessa | Retorno |
|---|---|---|
| Atributo `MODULO` no `<LAYOUTS>` | `"B"` | **Ausente** |
| Atributos do `<LAYOUT>` | `TAMANHO`, `ANALITICO`, `ORDENAR`, `FICHA`, `INICARQREM` etc | `PROFUNDIDADE`, `ANALITICO`, `CONFIGTAXAADMIN`, `CONCEXTBANC`, `USASQLNUFIN` |
| Elementos por linha | `<COLUNAS QUANTIDADE=...>` com `<CAMPO>` (expressões Sankhya) | `<COLUNAS>` (sem QUANTIDADE) com `<NOME>` (apenas rótulos de captura) |
| Coordenadas dos campos | `TAMANHO` | `POSINI` + `POSFIM` |
| Catálogo de ocorrências | N/A | Bloco `<OCORRENCIAS>` aninhado na coluna `Ocorrencia` |
| Tipos de coluna (C/E/F/A) | Sim | **Não usar** — retorno não tem tipo de coluna |

### Esqueleto canônico do XML de retorno

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<LAYOUTS QUANTIDADE="1" TIPO="Retorno">
  <LAYOUT PROFUNDIDADE="3" ANALITICO="N" CONFIGTAXAADMIN="N" CONCEXTBANC="N" USASQLNUFIN="N">
    <TITULO><![CDATA[RETORNO NOMEBANCO]]></TITULO>
    <LINHAS QUANTIDADE="1">
      <!-- HEADER do arquivo -->
      <LINHA ANALITICO="N" CONFIGTAXAADMIN="N" CONCEXTBANC="N" USASQLNUFIN="N">
        <TITULO><![CDATA[HEADER NOMEBANCO]]></TITULO>
        <COLUNAS>
          <COLUNA SEQUENCIA="0" POSINI="1" POSFIM="1" IDLINHA="N">
            <NOME><![CDATA[IDENTIFICADOR]]></NOME>
            <IDVALOR><![CDATA[0]]></IDVALOR>  <!-- 0 = tipo registro header -->
          </COLUNA>
          <!-- ... outras colunas de identificação do arquivo (banco, agência, conta) ... -->
        </COLUNAS>
        <LINHAS QUANTIDADE="1">
          <!-- DETALHE (registro analítico = por título) -->
          <LINHA ANALITICO="S" CONFIGTAXAADMIN="N" CONCEXTBANC="N" USASQLNUFIN="N">
            <TITULO><![CDATA[DETALHE NOMEBANCO]]></TITULO>
            <COLUNAS>
              <COLUNA SEQUENCIA="1" POSINI="1" POSFIM="1" IDLINHA="S">
                <NOME><![CDATA[IDENTIFICADOR]]></NOME>
                <IDVALOR><![CDATA[1]]></IDVALOR>  <!-- 1 = tipo registro detalhe -->
              </COLUNA>
              <!-- ... campos do título: NossoNumero, ValorTitulo, DataCredito, Ocorrencia, etc ... -->
              <COLUNA SEQUENCIA="N" POSINI="X" POSFIM="Y" IDLINHA="N">
                <NOME><![CDATA[Ocorrencia]]></NOME>
                <OCORRENCIAS>
                  <!-- catálogo de ocorrências aqui -->
                </OCORRENCIAS>
              </COLUNA>
              <!-- colunas Erro1..Erro5 logo após Ocorrencia -->
            </COLUNAS>
          </LINHA>
        </LINHAS>
      </LINHA>
    </LINHAS>
  </LAYOUT>
</LAYOUTS>
```

### Campos obrigatórios do DETALHE (cobertura mínima)

Para o Sankhya conseguir processar o retorno e dar baixa/conciliação corretamente, o registro
detalhe DEVE capturar no mínimo:

| `<NOME>` do campo | Significado | Onde encontrar no layout CNAB 400 Itaú |
|---|---|---|
| `IDENTIFICADOR` | Tipo do registro (sempre 1 para detalhe) | Posição 1 |
| `NossoNumero` | Identifica o título no Sankhya | Posições 86-93 (carteira+nosso número) ou 71-82 |
| `Nufin` | Chave do financeiro (opcional, casa por Nufin se enviado na remessa) | Posições 38-60 (Uso da Empresa) |
| `Carteira` | Carteira de cobrança | Posições 23-24 ou 83-85 |
| `ValorTitulo` | Valor nominal | Posições 153-165 |
| `ValorBaixa` | Valor recebido | Posições 153-165 (mesmo do ValorTitulo) ou 254-266 |
| `DataCredito` | Data crédito da liquidação | Posições 296-301 |
| `DataBaixa` | Data ocorrência | Posições 111-116 |
| `ValorJuros` | Juros/multa pagos | Posições 267-279 |
| `ValorDescontos` | Descontos concedidos | Posições 241-253 |
| `ValorAbatimentos` | Abatimentos | Posições 228-240 |
| `Ocorrencia` | **Código da ocorrência** — carrega o bloco `<OCORRENCIAS>` | Posições 109-110 |
| `Erro1` a `Erro5` | Códigos de motivo de rejeição (2 caracteres cada) | Posições 318-327 (5 grupos de 2) |

### Bloco `<OCORRENCIAS>` — o catálogo no XML

Para CADA código de ocorrência da Tabela de Códigos do banco (Nota 17 do layout Itaú BBA),
criar uma tag `<OCORRENCIA>` com 7-9 atributos boolean (S/N) + descrição:

```xml
<OCORRENCIA CODOCORRENCIA="06   " REPORTAR="N" INTERROMPER="N"
            BAIXAR="S" CONCILIAR="S" REGISTRARNOSSONUM="N" REGISTRARLOG="S"
            BAIXAPARCIAL="N" INSERIR="N" TRATAROCORRENCIA="N"
            ATUALIZACAOREMESSA="P" CODOCORRENCIAREMESSA="01">
  <DESCRICAO><![CDATA[Liquidação normal]]></DESCRICAO>
</OCORRENCIA>
```

**Regra de padding crítica**: `CODOCORRENCIA` é sempre **5 caracteres** (código de 2 dígitos + 3 espaços
à direita), pois o campo da TSIOBC tem 5 posições e o Sankhya bate strings literalmente. Exemplo:
`"02   "` para código 02, `"15   "` para código 15, `"100  "` para código 100.

**Atributos `ATUALIZACAOREMESSA` e `CODOCORRENCIAREMESSA`**: incluir APENAS em ocorrências que
fecham um ciclo de remessa (entrada confirmada → atualiza remessa "01", liquidação → atualiza
remessa "01"). Para ocorrências informacionais ou de erro, **omitir esses 2 atributos**.

Os 7 atributos S/N de cada ocorrência seguem a tabela de mapeamento de comportamento (próxima seção).

---

## Parte 2 — Tabela de Comportamento das Ocorrências

Este é o coração da skill. Cada código de ocorrência da Tabela de Códigos do banco deve ser
mapeado para um conjunto de flags que dizem ao Sankhya o que fazer.

### Atributos e seus significados

| Atributo XML | Coluna TSIOBC | Significado | Default |
|---|---|---|---|
| `CODOCORRENCIA` | `CODOCORRENCIA` | Código de 2 dígitos + 3 espaços (5 chars) | — |
| `REPORTAR` | `REPORTAR` | Mostrar a ocorrência no log/relatório de retorno | `N` |
| `INTERROMPER` | `INTERROMPER` | Parar processamento se aparecer | `N` |
| `BAIXAR` | `BAIXAR` | Dar baixa no título financeiro | `N` |
| `CONCILIAR` | `CONCILIAR` | Conciliar com extrato bancário | `N` |
| `REGISTRARNOSSONUM` | `REGISTRARNOSSONUM` | Gravar/atualizar o Nosso Número no título | `N` |
| `REGISTRARLOG` | `REGISTRARLOG` | Registrar log de movimentação | `S` (quase sempre) |
| `BAIXAPARCIAL` | `BAIXAPARCIAL` | Permitir baixa parcial (B2B/Inteligente) | `N` |
| `INSERIR` | `INSERIR` | Inserir novo título no financeiro (caso DDA) | `N` |
| `TRATAROCORRENCIA` | `TRATAROCORRENCIA` | Exibir alerta para tratamento manual | `N` |
| `ATUALIZACAOREMESSA` | `ATUALIZACAOREMESSA` | `P`=Pago / `R`=Rejeitado / `NULL` | (varia) |
| `CODOCORRENCIAREMESSA` | `CODOCOREMESSA` | Código equivalente na remessa (01=entrada) | (varia) |

### Padrões por tipo de ocorrência (CNAB 400 Itaú)

Use estes padrões como base. Variam ligeiramente por banco — confirme com o usuário se houver dúvida.

| Código | Descrição | BAIXAR | CONCILIAR | REGISTRARNOSSONUM | TRATAROCORRENCIA | ATUALIZACAOREMESSA | CODOCORRENCIAREMESSA |
|---|---|---|---|---|---|---|---|
| 02 | Entrada Confirmada | N | N | **S** | N | P | 01 |
| 03 | Entrada Rejeitada | N | N | N | **S** | — (omitir) | — |
| 06 | Liquidação normal | **S** | **S** | N | N | P | 01 |
| 09 | Baixado Auto via Arquivo | N | N | N | N | — | — |
| 10 | Baixado pela Agência | (variável) | N | N | N | P | 10 |
| 14 | Vencimento Alterado | N | N | N | N | — | — |
| 15 | Liquidação em Cartório | **S** | **S** | N | N | P | 01 |
| 17 | Liquidação após baixa | N | N | N | N | P | 01 |
| 24 | Entrada rejeitada por CEP | N | N | N | **S** | — | — |
| 27 | Baixa Rejeitada | N | N | N | **S** | — | — |
| 30 | Alteração Dados Rejeitada | N | N | N | **S** | — | — |
| 32 | Instrução Rejeitada | N | N | N | **S** | — | — |
| 33 | Confirma Alteração Outros | N | N | N | N | P | 01 |
| Demais informacionais | (16, 18-23, 25, 28-29, 34-35, 40, 55, 68-69) | N | N | N | N | — | — |

**REPORTAR padrão**: `N` para Multiplike, `S` para Daycoval/Yaaleh — varia por banco/cliente. Quando
em dúvida, pergunte ao usuário ou siga o padrão do banco mais próximo nas references.

**REGISTRARLOG padrão**: `S` em quase tudo.

A lista canônica de códigos CNAB 400 Itaú está no arquivo `references/tabela-ocorrencias-cnab400.md`.

---

## Parte 3 — Script SQL para popular a TSIOBC

### Estrutura da tabela TSIOBC

Consulte `references/tabela-tsiobc-dicionario.md` para o dicionário completo. Resumo das colunas
que o INSERT precisa preencher:

| Coluna | Tipo | Origem do valor |
|---|---|---|
| `CODIGO` | Número Inteiro | **Variável `@CODIGO`** definida no início do script — o usuário informa |
| `SEQUENCIA` | Número Inteiro | SEQUENCIA da coluna `Ocorrencia` no XML (geralmente 4; Multiplike=15) |
| `CODOCORRENCIA` | Texto (5) | Código + padding de espaços à direita ("06   ") |
| `DESCRICAO` | Texto (50) | Descrição padronizada (50 chars com padding) |
| `REPORTAR`, `INTERROMPER`, `BAIXAR`, `CONCILIAR`, `REGISTRARNOSSONUM`, `REGISTRARLOG`, `BAIXAPARCIAL` | Texto (1) | `'S'` ou `'N'` |
| `INSERIR`, `ALTERAR`, `TRATAROCORRENCIA`, `INSERIRBAIXARDESP` | Texto (1) | `'S'` ou `'N'` |
| `CODOCOREMESSA` | Número Inteiro ou NULL | Código da ocorrência de remessa correspondente |
| `ATUALIZACAOREMESSA` | Texto (1) | `'P'`, `'R'` ou `NULL` |
| `CODEMP`, `CODTIPTIT`, `CODCENCUS`, `CODNAT`, `CODTIPOPER`, `CODPARC`, `CODCTABCOINT`, `CODBCO`, `CODMODELO`, `NURFE` | Vários | `NULL` por default |
| `SEQCAMPOVLRDESP`, `CODLANCDESP`, `REMOVERMONCOB`, `CAMPOSAJUSTAR`, `REGISTRARQRCODE`, `ENVPIXEMAIL` | Vários | `NULL` por default |

### Template do script SQL

```sql
-- ============================================================================
-- Script para popular ocorrências bancárias na TSIOBC
-- Banco: [NomeBanco]
-- Layout: CNAB 400 (padrão Itaú BBA)
-- Total de ocorrências: [N]
-- ============================================================================

-- IMPORTANTE: Informe o CODIGO da instância OcorrenciaBancariaRetorno cadastrada na TSIOBC.
-- Encontre em: Sankhya → Modelagem → Tabela TSIOBC → registro pai do retorno deste banco.
DECLARE @CODIGO INT = [CODIGO_AQUI];   -- ex: 331100 (Multiplike), 341100 (Daycoval), 321100 (Yaaleh)
DECLARE @SEQUENCIA INT = [SEQUENCIA_AQUI];  -- SEQUENCIA da coluna "Ocorrencia" no XML (ex: 4 ou 15)

-- Limpeza prévia (opcional — descomente se quiser refazer do zero)
-- DELETE FROM TSIOBC WHERE CODIGO = @CODIGO AND SEQUENCIA = @SEQUENCIA;

INSERT INTO TSIOBC
    (CODIGO, SEQUENCIA, CODOCORRENCIA, DESCRICAO,
     REPORTAR, INTERROMPER, BAIXAR, CONCILIAR, REGISTRARNOSSONUM, REGISTRARLOG, BAIXAPARCIAL,
     CODEMP, CODTIPTIT, CODCENCUS, CODNAT, CODTIPOPER, CODPARC,
     INSERIR, ALTERAR, TRATAROCORRENCIA, INSERIRBAIXARDESP,
     SEQCAMPOVLRDESP, CODLANCDESP, CODOCOREMESSA, ATUALIZACAOREMESSA,
     CODCTABCOINT, CODBCO, REMOVERMONCOB, CAMPOSAJUSTAR,
     REGISTRARQRCODE, CODMODELO, NURFE, ENVPIXEMAIL)
VALUES
    -- Entrada Confirmada
    (@CODIGO, @SEQUENCIA, '02   ', 'Entrada Confirmada                                ',
     'N', 'N', 'N', 'N', 'S', 'S', 'N',
     NULL, NULL, NULL, NULL, NULL, NULL,
     'N', 'N', 'N', 'N',
     NULL, NULL, 1, 'P',
     NULL, NULL, NULL, NULL,
     NULL, NULL, NULL, NULL),

    -- Entrada Rejeitada
    (@CODIGO, @SEQUENCIA, '03   ', 'Entrada Rejeitada                                 ',
     'N', 'N', 'N', 'N', 'N', 'S', 'N',
     NULL, NULL, NULL, NULL, NULL, NULL,
     'N', 'N', 'S', 'N',
     NULL, NULL, NULL, NULL,
     NULL, NULL, NULL, NULL,
     NULL, NULL, NULL, NULL),

    -- ... (demais ocorrências)
;
GO

-- Verificação
SELECT COUNT(*) AS TotalOcorrencias FROM TSIOBC WHERE CODIGO = @CODIGO AND SEQUENCIA = @SEQUENCIA;
```

### Regras de formatação do SQL

1. **Padding da DESCRICAO**: sempre 50 caracteres (preencher com espaços à direita até completar 50).
2. **Padding do CODOCORRENCIA**: sempre 5 caracteres (código + espaços à direita).
3. **Use `NULL`** literal (não `'NULL'` string) para colunas vazias.
4. **Use `@CODIGO` e `@SEQUENCIA`** como variáveis no topo — não hardcode, isso facilita reuso.
5. **Codificação UTF-8 com BOM** — caracteres acentuados em descrições (`Liquidação`, `Cartório`) devem ser preservados.
6. **Use vírgula entre VALUES**, ponto-e-vírgula só no final.
7. **Comentário antes de cada linha de VALUES** com a descrição da ocorrência para legibilidade.

---

## Processo de Geração

Ao receber o PDF do banco e o pedido para gerar retorno:

1. **Leia o PDF completo** — Localize a Tabela 17 ("Código de Ocorrência - Arquivo Retorno") e o
   layout do Registro Transação/Detalhe.

2. **Pergunte ao usuário** (se não tiver fornecido):
   - Nome do banco para nomenclatura
   - CODIGO da TSIOBC (instância pai)
   - SEQUENCIA da coluna Ocorrencia (default 4 para CNAB 400 padrão)
   - Se há códigos de ocorrência específicos do banco a incluir/excluir do catálogo padrão

3. **Identifique TODOS os códigos de ocorrência** listados na Tabela 17 do PDF. CNAB 400 Itaú tem
   ~60 códigos no total, mas a maioria das implementações usa um subconjunto comum (~30 códigos).
   Consulte `references/tabela-ocorrencias-cnab400.md` para a lista canônica.

4. **Mapeie cada ocorrência** para a tabela de comportamento (Parte 2). Quando o PDF indicar que
   uma ocorrência é específica de protesto/cartório/rejeição, aplique o flag correspondente
   (TRATAROCORRENCIA=S para rejeições, BAIXAR=S+CONCILIAR=S para liquidações).

5. **Gere o XML** seguindo o esqueleto da Parte 1. Atenção:
   - Encoding `ISO-8859-1` (não UTF-8 — Sankhya espera ISO-8859-1 nos XMLs de EDI)
   - Quebras de linha CRLF (`\r\n`)
   - Caracteres acentuados em CDATA preservados

6. **Gere o SQL** seguindo o template da Parte 3. Atenção:
   - Encoding UTF-8 com BOM (para SQL Server Management Studio ler corretamente acentos)
   - Variáveis `@CODIGO` e `@SEQUENCIA` parametrizadas

7. **Apresente os 2 arquivos juntos** via `present_files` — XML primeiro, SQL em seguida.

8. **Informe ao usuário**:
   - Quantas ocorrências foram catalogadas
   - Quais flags ele deve revisar manualmente (REPORTAR é o mais subjetivo — depende da política da empresa)
   - Onde editar `@CODIGO` no SQL antes de executar
   - Caminho de importação: *Sankhya → Financeiro → Configurações → EDI → Importar Layout de Retorno*

---

## Outputs Esperados

- **Arquivo 1**: `Retorno_[NomeBanco].xml` (encoding ISO-8859-1)
- **Arquivo 2**: `Popular_TSIOBC_[NomeBanco].sql` (encoding UTF-8 com BOM)
- Ambos salvos em `/mnt/user-data/outputs/`
- Apresentar via `present_files` na ordem XML → SQL

---

## References

- `references/exemplo-multiplike-cnab400.md` — Exemplo COMPLETO Multiplike (XML + SQL como gabarito)
- `references/tabela-tsiobc-dicionario.md` — Dicionário de dados completo da TSIOBC
- `references/tabela-ocorrencias-cnab400.md` — Catálogo padrão de códigos CNAB 400 Itaú (NOTA 17 do layout)

Sempre consulte `exemplo-multiplike-cnab400.md` ao iniciar uma nova geração — é o gabarito de
referência ground-truth validado em produção na CPE.
