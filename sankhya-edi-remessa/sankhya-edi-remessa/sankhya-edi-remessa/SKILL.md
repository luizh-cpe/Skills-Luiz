---
name: sankhya-edi-remessa
description: >
  Gera o XML de configuração de EDI bancário de remessa no padrão do Sankhya ERP, a partir do
  PDF de layout CNAB disponibilizado pelo banco. Use esta skill SEMPRE que o usuário mencionar:
  "EDI bancário", "remessa", "configurar banco no Sankhya", "gerador de remessa", "CNAB",
  "layout bancário XML", "novo banco Sankhya", "configurar cobrança bancária", ou qualquer
  variação relacionada à configuração de remessa bancária no Sankhya. O output é um arquivo
  XML pronto para importação no módulo de EDI do Sankhya.
---

# Gerador de XML de Remessa Bancária — Sankhya EDI

## Objetivo

Gerar o arquivo XML de configuração de EDI de **remessa bancária** compatível com o Sankhya ERP,
mapeando as posições do layout CNAB (PDF do banco) para as variáveis e estrutura XML do Sankhya.

---

## Contexto do Sistema Sankhya

O Sankhya utiliza um XML hierárquico com a seguinte estrutura raiz:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<LAYOUTS MODULO="B" QUANTIDADE="1" TIPO="Remessa">
  <LAYOUT PROFUNDIDADE="[3 ou 4]" TAMANHO="[tamanho_linha+2]" ANALITICO="N" ORDENAR="N"
          FICHA="N" ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="[COB|CB]">
    <TITULO><![CDATA[Remessa NomeDoBanco]]></TITULO>
    <LINHAS QUANTIDADE="[n]">
      <!-- Registros aqui (Header, Detalhe, Trailer) -->
    </LINHAS>
  </LAYOUT>
</LAYOUTS>
```

### Atributos do `<LAYOUT>`
| Atributo | Significado | Valor típico |
|---|---|---|
| `PROFUNDIDADE` | Níveis de aninhamento (CNAB240=4, CNAB400=3) | `3` ou `4` |
| `TAMANHO` | Tamanho da linha + 2 (CR+LF) | `242` (CNAB240), `402` (CNAB400) |
| `INICARQREM` | Prefixo do arquivo gerado | `"COB"` ou `"CB"` |

---

## Estrutura de Registros por Padrão CNAB

### CNAB 240 (PROFUNDIDADE=4)
```
LAYOUT
└── Header Arquivo (ANALITICO="N")
    └── Header Lote (ANALITICO="N")
        ├── Segmento P (ANALITICO="S") ← registro analítico (por título)
        ├── Segmento Q (ANALITICO="S")
        └── Trailler Lote (ANALITICO="N")
└── Trailler Arquivo (ANALITICO="N")
```

### CNAB 400 (PROFUNDIDADE=3)
```
LAYOUT
└── Header (ANALITICO="N")
    └── Detalhe/Registro 1 (ANALITICO="S") ← registro analítico
└── Trailler (ANALITICO="N")
```

---

## Tipos de COLUNA (`TIPO`)

| TIPO | Comportamento | Uso típico |
|---|---|---|
| `C` | Numérico, alinhado à direita, preenchido com zeros | Códigos, valores numéricos |
| `E` | Alfanumérico, alinhado à esquerda, preenchido com espaços | Nomes, textos, literais |
| `F` | Numérico, alinhado à direita, sem zeros à esquerda | Agência, conta |
| `A` | Valor monetário com decimais | Valores financeiros — usar `QTDDEC="2"` |
| `D` | Valor numérico direto | Uso específico |
| `B` | Branco fixo | Campos reservados |

---

## Variáveis do Sankhya (use exatamente esses nomes)

### Variáveis de Dados Gerais (configurações do banco)
```
Dados_Gerais.CODIGO_BANCO         → Código do banco na compensação
Dados_Gerais.NOME_BANCO           → Nome do banco por extenso
Dados_Gerais.CODIGO_AGENCIA       → Código da agência
Dados_Gerais.DIGITO_AGENCIA       → Dígito verificador da agência
Dados_Gerais.CONTA                → Número da conta corrente
Dados_Gerais.DIGITO_CONTA         → Dígito da conta
Dados_Gerais.RAZAOSOCIAL_EMPRESA  → Razão social da empresa beneficiária
Dados_Gerais.CARTEIRA             → Código da carteira de cobrança
Dados_Gerais.CONVENIO             → Código do convênio bancário
```

### Variáveis de Dados do Título (por duplicata)
```
Dados_Detalhe.CODIGO_BANCO            → Código do banco (repetido no detalhe)
Dados_Detalhe.CGC_EMPRESA             → CNPJ/CPF da empresa
Dados_Detalhe.TAMANHO_CGC_CPF         → Tamanho do CGC (11=CPF, 14=CNPJ)
Dados_Detalhe.CGC_CPF_PARCEIRO        → CNPJ/CPF do pagador
Dados_Detalhe.RAZAO_SOCIAL            → Razão social do pagador (empresa emissora)
Dados_Detalhe.RAZAOSOCIAL_PARCEIRO    → Nome do pagador/sacado
Dados_Detalhe.NOSSONUM                → Nosso número (formato banco Sicoob)
Dados_Detalhe.NOSSO_NUMERO            → Nosso número (formato padrão)
Dados_Detalhe.DIGITO_NOSSO_NUMERO     → Dígito do nosso número
Dados_Detalhe.NUMERO_NOTA             → Número da nota/documento
Dados_Detalhe.DESDOBRAMENTO_NOTA      → Desdobramento/parcela
Dados_Detalhe.SERIE_NOTA              → Série da nota
Dados_Detalhe.NUMERO_UNICO_FINANCEIRO → NUFIN — chave do lançamento financeiro
Dados_Detalhe.NUMERO_REMESSA          → Número sequencial da remessa
Dados_Detalhe.DATA_VENCIMENTO         → Vencimento (formato AAAAMMDD)
Dados_Detalhe.DATA_VENCIMENTO_PADRAO  → Vencimento (formato DD/MM/AAAA)
Dados_Detalhe.DATA_NEGOCIACAO         → Data de emissão/negociação (AAAAMMDD)
Dados_Detalhe.DATA_NEGOCIACAO_PADRAO  → Data emissão (formato DD/MM/AAAA)
Dados_Detalhe.VALOR_LIQUIDO           → Valor líquido do título
Dados_Detalhe.VALOR_TITULO            → Valor bruto do título
Dados_Detalhe.ENDERECO_PARCEIRO       → Endereço do pagador
Dados_Detalhe.NUMERO_END_PARCEIRO     → Número do endereço
Dados_Detalhe.TIPO_ENDERECO           → Tipo de logradouro (Rua, Av, etc)
Dados_Detalhe.CIDADE_PARCEIRO         → Cidade do pagador
Dados_Detalhe.UF_PARCEIRO             → UF do pagador
Dados_Detalhe.CEP_PARCEIRO            → CEP do pagador
Dados_Detalhe.FINAD_NUMNFSE           → Número NFS-e
```

### Variáveis de Sistema (geradas automaticamente)
```
DATAATUAL           → Data atual formato AAAAMMDD
DATAATUALPADRAO     → Data atual formato DD/MM/AAAA
HORAATUAL           → Hora atual formato HHMMSS
NROSEQUENCIAL       → Número sequencial do registro no lote
SEQUENCIALOTE       → Número sequencial do lote
FIMLINHA            → Marcador de fim de linha (CR+LF) — SEMPRE tamanho 2, TIPO="E"
```

### Funções disponíveis nas expressões
```
IF(cond, valor_true, valor_false)
STR(valor)                          → Converte para string
COPY(string, inicio, tamanho)       → Substring (base 0)
trocaesp(string)                    → Remove caracteres especiais
replaceString(str, de, para)
PDES('campo','tabela','filtro')     → Consulta banco de dados Sankhya
```

---

## Regras de Mapeamento PDF → XML

### Passo 1 — Identificar o padrão CNAB
- Se o PDF menciona registros com segmentos P, Q, R, S → **CNAB 240** (PROFUNDIDADE=4)
- Se o PDF menciona Registro 0, 1, 9 com 400 posições → **CNAB 400** (PROFUNDIDADE=3)

### Passo 2 — Mapear cada seção do layout
Para cada seção do PDF (Header Arquivo, Header Lote, Segmentos, Trailer):
1. Criar uma `<LINHA>` com o `TAMANHO` correto e `ANALITICO` adequado
2. Para cada campo da tabela do PDF, criar uma `<COLUNA>`:
   - `SEQUENCIA` = número sequencial da coluna nessa linha (1, 2, 3...)
   - `TAMANHO` = tamanho em bytes do campo (Até - De + 1)
   - `TIPO` = conforme tabela de tipos acima
   - `<CAMPO>` = variável Sankhya correspondente, valor fixo, ou expressão

### Passo 3 — Regras de preenchimento por tipo de campo

**Campos com valor fixo (literal do banco):**
```xml
<COLUNA SEQUENCIA="1" TAMANHO="3" TIPO="C">
  <CAMPO><![CDATA[756]]></CAMPO>  <!-- código do banco fixo -->
</COLUNA>
```

**Campos "Preencher com espaços em branco":**
```xml
<COLUNA SEQUENCIA="4" TAMANHO="9" TIPO="E">
  <CAMPO><![CDATA['']]></CAMPO>
</COLUNA>
```

**Campos "Preencher com zeros":**
```xml
<COLUNA SEQUENCIA="22" TAMANHO="5" TIPO="C">
  <CAMPO><![CDATA[0]]></CAMPO>
</COLUNA>
```

**Tipo inscrição empresa (CPF=1, CNPJ=2):**
```xml
<COLUNA SEQUENCIA="5" TAMANHO="1" TIPO="C">
  <CAMPO><![CDATA[IF(Dados_Detalhe.TAMANHO_CGC_CPF = 14,2,1)]]></CAMPO>
</COLUNA>
```

**Data no formato DDMMAAAA a partir de DATA_VENCIMENTO_PADRAO:**
```xml
<CAMPO><![CDATA[COPY(Dados_Detalhe.DATA_VENCIMENTO_PADRAO,0,2)+''+COPY(Dados_Detalhe.DATA_VENCIMENTO_PADRAO,3,2)+''+COPY(Dados_Detalhe.DATA_VENCIMENTO_PADRAO,7,2)]]></CAMPO>
```

**Valor monetário:**
```xml
<COLUNA SEQUENCIA="21" TAMANHO="13" TIPO="A" QTDDEC="2">
  <CAMPO><![CDATA[Dados_Detalhe.VALOR_TITULO]]></CAMPO>
</COLUNA>
```

**Fim de linha — SEMPRE a última coluna de cada linha:**
```xml
<COLUNA SEQUENCIA="99" TAMANHO="2" TIPO="E">
  <CAMPO><![CDATA[FIMLINHA]]></CAMPO>
</COLUNA>
```

### Passo 4 — Campos com informações sensíveis (criptografados)
Campos como conta corrente, dígito, CNPJ da empresa própria que contenham dados reais
**NÃO devem ser incluídos em texto puro** — use variáveis Sankhya no lugar:
- Conta corrente → `Dados_Gerais.CONTA`
- Dígito da conta → `Dados_Gerais.DIGITO_CONTA`
- CNPJ da empresa → `Dados_Detalhe.CGC_EMPRESA`

---

## Processo de Geração

Ao receber o PDF do banco:

1. **Leia o PDF** completo — identifique todos os registros e campos
2. **Determine o padrão**: CNAB 240 ou CNAB 400
3. **Calcule o TAMANHO** do `<LAYOUT>`: tamanho da linha + 2
4. **Monte o XML** seguindo a estrutura hierárquica correta
5. **Para cada campo** da tabela do PDF:
   - Calcule o TAMANHO: coluna "Até" menos coluna "De" + 1
   - Identifique o TIPO adequado
   - Mapeie para a variável Sankhya ou valor fixo correto
6. **Verifique** se a soma dos TAMANHOs de todas as COLUNAs de cada LINHA (exceto FIMLINHA) = tamanho total da linha do padrão (240 ou 400)
7. **Gere o arquivo** XML com encoding ISO-8859-1

---

## Exemplo de Estrutura Mínima — CNAB 400

Consulte o arquivo de referência `references/exemplo-cnab400.md` para um exemplo completo
de CNAB 400 (3 níveis), incluindo Header, Detalhe e Trailer.

## Exemplo de Estrutura Mínima — CNAB 240

Consulte o arquivo de referência `references/exemplo-cnab240.md` para um exemplo completo
de CNAB 240 (4 níveis), incluindo todos os segmentos P, Q e Trailer.

---

## Output Esperado

- Arquivo: `Remessa_[NomeBanco].xml`
- Encoding: `ISO-8859-1`
- Salvar em `/mnt/user-data/outputs/`
- Apresentar ao usuário via `present_files`

Após gerar, informe ao usuário:
1. O padrão CNAB detectado (240 ou 400)
2. Quais registros/segmentos foram mapeados
3. Quais campos precisam de conferência (conta, agência, convênio — específicos do contrato)
4. Como importar no Sankhya: Módulo Financeiro → Configurações → EDI → Importar Layout
