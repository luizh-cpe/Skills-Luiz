# Exemplo de XML CNAB 400 — Padrão Sankhya

Baseado no layout Grafeno/Yaaleh (CNAB400, 400 posições por linha).

## Características do CNAB 400
- `PROFUNDIDADE="3"` no `<LAYOUT>`
- `TAMANHO="402"` (400 + 2 bytes de CR+LF)
- `INICARQREM="CB"` ou `"COB"`
- Estrutura: Header (Reg 0) → Detalhe/Reg1 (analítico) → Trailer (Reg 9)

## Estrutura XML completa

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<LAYOUTS MODULO="B" QUANTIDADE="1" TIPO="Remessa">
  <LAYOUT PROFUNDIDADE="3" TAMANHO="402" ANALITICO="N" ORDENAR="N" FICHA="N"
          ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="CB">
    <TITULO><![CDATA[Remessa NomeDoBanco]]></TITULO>
    <LINHAS QUANTIDADE="2">

      <!-- ═══════════════════════════════════════════════
           REGISTRO 0 — HEADER DO ARQUIVO
           ═══════════════════════════════════════════════ -->
      <LINHA TAMANHO="402" ANALITICO="N" ORDENAR="N" FICHA="N"
             ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="COB">
        <TITULO><![CDATA[HEADER]]></TITULO>
        <COLUNAS>
          <!-- Pos 001: Identificação do registro = "0" -->
          <COLUNA SEQUENCIA="1" TAMANHO="1" TIPO="C">
            <CAMPO><![CDATA[0]]></CAMPO>
          </COLUNA>
          <!-- Pos 002: Identificação remessa = "1" -->
          <COLUNA SEQUENCIA="2" TAMANHO="1" TIPO="C">
            <CAMPO><![CDATA[1]]></CAMPO>
          </COLUNA>
          <!-- Pos 003-009: Literal "REMESSA" -->
          <COLUNA SEQUENCIA="3" TAMANHO="7" TIPO="E">
            <CAMPO><![CDATA['REMESSA']]></CAMPO>
          </COLUNA>
          <!-- Pos 010-011: Código do serviço = "01" -->
          <COLUNA SEQUENCIA="4" TAMANHO="2" TIPO="C">
            <CAMPO><![CDATA[01]]></CAMPO>
          </COLUNA>
          <!-- Pos 012-026: Literal do serviço = "COBRANCA" -->
          <COLUNA SEQUENCIA="5" TAMANHO="15" TIPO="E">
            <CAMPO><![CDATA['COBRANCA']]></CAMPO>
          </COLUNA>
          <!-- Pos 027-046: Número da conta no banco (com dígito) -->
          <COLUNA SEQUENCIA="6" TAMANHO="20" TIPO="C">
            <CAMPO><![CDATA[Dados_Gerais.CONTA]]></CAMPO>
          </COLUNA>
          <!-- Pos 047-076: Nome da empresa -->
          <COLUNA SEQUENCIA="7" TAMANHO="30" TIPO="E">
            <CAMPO><![CDATA[trocaesp(Dados_Detalhe.RAZAO_SOCIAL)]]></CAMPO>
          </COLUNA>
          <!-- Pos 077-079: Código do banco na câmara -->
          <COLUNA SEQUENCIA="8" TAMANHO="3" TIPO="C">
            <CAMPO><![CDATA[Dados_Gerais.CODIGO_BANCO]]></CAMPO>
          </COLUNA>
          <!-- Pos 080-094: Nome do banco -->
          <COLUNA SEQUENCIA="9" TAMANHO="15" TIPO="E">
            <CAMPO><![CDATA[Dados_Gerais.NOME_BANCO]]></CAMPO>
          </COLUNA>
          <!-- Pos 095-100: Data de gravação DDMMAA -->
          <COLUNA SEQUENCIA="10" TAMANHO="6" TIPO="E">
            <CAMPO><![CDATA[COPY(DATAATUALPADRAO,0,2)+''+COPY(DATAATUALPADRAO,3,2)+''+COPY(DATAATUALPADRAO,7,2)]]></CAMPO>
          </COLUNA>
          <!-- Pos 101-108: Não utilizado (brancos) -->
          <COLUNA SEQUENCIA="11" TAMANHO="8" TIPO="E" />
          <!-- Pos 109-110: Identificação do sistema = "MX" -->
          <COLUNA SEQUENCIA="12" TAMANHO="2" TIPO="E">
            <CAMPO><![CDATA['MX']]></CAMPO>
          </COLUNA>
          <!-- Pos 111-117: Número sequencial de remessa -->
          <COLUNA SEQUENCIA="13" TAMANHO="7" TIPO="C">
            <CAMPO><![CDATA[Dados_Detalhe.NUMERO_REMESSA]]></CAMPO>
          </COLUNA>
          <!-- Pos 118-394: Não utilizado (brancos) -->
          <COLUNA SEQUENCIA="14" TAMANHO="277" TIPO="E" />
          <!-- Pos 395-400: Número sequencial do registro = "000001" -->
          <COLUNA SEQUENCIA="15" TAMANHO="6" TIPO="C">
            <CAMPO><![CDATA[000001]]></CAMPO>
          </COLUNA>
          <!-- Fim de linha - OBRIGATÓRIO -->
          <COLUNA SEQUENCIA="16" TAMANHO="2" TIPO="E">
            <CAMPO><![CDATA[FIMLINHA]]></CAMPO>
          </COLUNA>
        </COLUNAS>

        <!-- ═══════════════════════════════════════════════
             REGISTRO 1 — DETALHE (analítico, por título)
             ═══════════════════════════════════════════════ -->
        <LINHAS QUANTIDADE="1">
          <LINHA TAMANHO="402" ANALITICO="S" ORDENAR="N" FICHA="N"
                 ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="COB">
            <TITULO><![CDATA[Detalhes Tipo 1]]></TITULO>
            <COLUNAS>
              <!-- Pos 001: Identificação do registro = "1" -->
              <COLUNA SEQUENCIA="1" TAMANHO="1" TIPO="C">
                <CAMPO><![CDATA[1]]></CAMPO>
              </COLUNA>
              <!-- Pos 002-006: Não utilizado -->
              <COLUNA SEQUENCIA="2" TAMANHO="5" TIPO="E" />
              <!-- ... campos do título conforme layout do banco ... -->
              <!-- Pos 021-037: Identificação empresa (carteira+agência+conta) -->
              <COLUNA SEQUENCIA="7" TAMANHO="1" TIPO="E">
                <CAMPO><![CDATA['0']]></CAMPO>
              </COLUNA>
              <COLUNA SEQUENCIA="8" TAMANHO="3" TIPO="F">
                <CAMPO><![CDATA[Dados_Gerais.CARTEIRA]]></CAMPO>
              </COLUNA>
              <COLUNA SEQUENCIA="9" TAMANHO="5" TIPO="F">
                <CAMPO><![CDATA[Dados_Gerais.CODIGO_AGENCIA]]></CAMPO>
              </COLUNA>
              <COLUNA SEQUENCIA="10" TAMANHO="8" TIPO="F">
                <CAMPO><![CDATA[Dados_Gerais.CONTA]]></CAMPO>
              </COLUNA>
              <!-- Pos 063-065: Código do banco -->
              <COLUNA SEQUENCIA="13" TAMANHO="3" TIPO="C">
                <CAMPO><![CDATA[Dados_Gerais.CODIGO_BANCO]]></CAMPO>
              </COLUNA>
              <!-- Pos 071-081: Nosso número -->
              <COLUNA SEQUENCIA="15" TAMANHO="11" TIPO="F">
                <CAMPO><![CDATA[Dados_Detalhe.NOSSO_NUMERO]]></CAMPO>
              </COLUNA>
              <!-- Pos 082: Dígito nosso número -->
              <COLUNA SEQUENCIA="16" TAMANHO="1" TIPO="F">
                <CAMPO><![CDATA[Dados_Detalhe.DIGITO_NOSSO_NUMERO]]></CAMPO>
              </COLUNA>
              <!-- Pos 109-110: Ocorrência = "01" (remessa) -->
              <COLUNA SEQUENCIA="23" TAMANHO="2" TIPO="C">
                <CAMPO><![CDATA[01]]></CAMPO>
              </COLUNA>
              <!-- Pos 111-120: Número do documento -->
              <COLUNA SEQUENCIA="24" TAMANHO="10" TIPO="E">
                <CAMPO><![CDATA[Dados_Detalhe.NUMERO_NOTA + Dados_Detalhe.DESDOBRAMENTO_NOTA]]></CAMPO>
              </COLUNA>
              <!-- Pos 121-126: Data de vencimento DDMMAA -->
              <COLUNA SEQUENCIA="26" TAMANHO="6" TIPO="E">
                <CAMPO><![CDATA[COPY(Dados_Detalhe.DATA_VENCIMENTO_PADRAO,0,2)+''+COPY(Dados_Detalhe.DATA_VENCIMENTO_PADRAO,3,2)+''+COPY(Dados_Detalhe.DATA_VENCIMENTO_PADRAO,7,2)]]></CAMPO>
              </COLUNA>
              <!-- Pos 127-139: Valor do título (13 dígitos, 2 decimais) -->
              <COLUNA SEQUENCIA="27" TAMANHO="13" TIPO="A" QTDDEC="2">
                <CAMPO><![CDATA[Dados_Detalhe.VALOR_TITULO]]></CAMPO>
              </COLUNA>
              <!-- Pos 140-142: Banco encarregado (zeros) -->
              <COLUNA SEQUENCIA="28" TAMANHO="3" TIPO="E">
                <CAMPO><![CDATA['000']]></CAMPO>
              </COLUNA>
              <!-- Pos 148-149: Espécie = "01" (Duplicata) -->
              <COLUNA SEQUENCIA="30" TAMANHO="2" TIPO="C">
                <CAMPO><![CDATA[01]]></CAMPO>
              </COLUNA>
              <!-- Pos 150: Aceite = "N" -->
              <COLUNA SEQUENCIA="31" TAMANHO="1" TIPO="E">
                <CAMPO><![CDATA['N']]></CAMPO>
              </COLUNA>
              <!-- Pos 151-156: Data de emissão DDMMAA -->
              <COLUNA SEQUENCIA="32" TAMANHO="6" TIPO="C">
                <CAMPO><![CDATA[COPY(Dados_Detalhe.DATA_NEGOCIACAO_PADRAO,0,2)+''+COPY(Dados_Detalhe.DATA_NEGOCIACAO_PADRAO,3,2)+''+COPY(Dados_Detalhe.DATA_NEGOCIACAO_PADRAO,7,2)]]></CAMPO>
              </COLUNA>
              <!-- Pos 219-220: Tipo inscrição pagador (01=CPF, 02=CNPJ) -->
              <COLUNA SEQUENCIA="40" TAMANHO="2" TIPO="C">
                <CAMPO><![CDATA[IF(Dados_Detalhe.TAMANHO_CGC_CPF= 11,01,02)]]></CAMPO>
              </COLUNA>
              <!-- Pos 221-234: CNPJ/CPF do pagador -->
              <COLUNA SEQUENCIA="41" TAMANHO="14" TIPO="F">
                <CAMPO><![CDATA[Dados_Detalhe.CGC_CPF_PARCEIRO]]></CAMPO>
              </COLUNA>
              <!-- Pos 235-274: Nome do pagador -->
              <COLUNA SEQUENCIA="42" TAMANHO="40" TIPO="E">
                <CAMPO><![CDATA[Dados_Detalhe.RAZAOSOCIAL_PARCEIRO]]></CAMPO>
              </COLUNA>
              <!-- Pos 275-314: Endereço do pagador -->
              <COLUNA SEQUENCIA="43" TAMANHO="40" TIPO="E">
                <CAMPO><![CDATA[Dados_Detalhe.TIPO_ENDERECO+' '+Dados_Detalhe.ENDERECO_PARCEIRO+', '+Dados_Detalhe.NUMERO_END_PARCEIRO+','+Dados_Detalhe.CIDADE_PARCEIRO+', '+Dados_Detalhe.UF_PARCEIRO]]></CAMPO>
              </COLUNA>
              <!-- Pos 395-400: Número sequencial do registro -->
              <COLUNA SEQUENCIA="47" TAMANHO="6" TIPO="C">
                <CAMPO><![CDATA[NROSEQUENCIAL+1]]></CAMPO>
              </COLUNA>
              <!-- Fim de linha -->
              <COLUNA SEQUENCIA="49" TAMANHO="2" TIPO="E">
                <CAMPO><![CDATA[FIMLINHA]]></CAMPO>
              </COLUNA>
            </COLUNAS>
          </LINHA>
        </LINHAS>
      </LINHA>

      <!-- ═══════════════════════════════════════════════
           REGISTRO 9 — TRAILER DO ARQUIVO
           ═══════════════════════════════════════════════ -->
      <LINHA TAMANHO="402" ANALITICO="N" ORDENAR="N" FICHA="N"
             ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="COB">
        <TITULO><![CDATA[TRAILLER]]></TITULO>
        <COLUNAS>
          <!-- Pos 001: Identificação = "9" -->
          <COLUNA SEQUENCIA="1" TAMANHO="1" TIPO="C">
            <CAMPO><![CDATA[9]]></CAMPO>
          </COLUNA>
          <!-- Pos 002-394: Brancos -->
          <COLUNA SEQUENCIA="2" TAMANHO="393" TIPO="E" />
          <!-- Pos 395-400: Número sequencial do último registro -->
          <COLUNA SEQUENCIA="3" TAMANHO="6" TIPO="C">
            <CAMPO><![CDATA[NROSEQUENCIAL+1]]></CAMPO>
          </COLUNA>
          <!-- Fim de linha -->
          <COLUNA SEQUENCIA="4" TAMANHO="2" TIPO="E">
            <CAMPO><![CDATA[FIMLINHA]]></CAMPO>
          </COLUNA>
        </COLUNAS>
      </LINHA>

    </LINHAS>
  </LAYOUT>
</LAYOUTS>
```

## Notas Importantes para CNAB 400

- Todo campo numérico vazio → preencher com zeros (`TIPO="C"`, `<CAMPO><![CDATA[0]]></CAMPO>`)
- Todo campo alfanumérico vazio → preencher com espaços (`TIPO="E"`, `<CAMPO><![CDATA['']]></CAMPO>`)
- Campos vazios sem `<CAMPO>` → `<COLUNA ... TIPO="E" />` (branco automático)
- Data no formato DDMMAA usa `COPY()` a partir de `DATA_xxx_PADRAO`
- Data no formato DDMMAAAA usa `COPY()` também, ajustando o índice do ano para posição 6
- O número sequencial do registro no trailer usa `NROSEQUENCIAL+1`
