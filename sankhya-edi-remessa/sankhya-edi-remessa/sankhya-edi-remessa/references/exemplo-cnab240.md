# Exemplo de XML CNAB 240 — Padrão Sankhya

Baseado nos layouts Banco do Brasil e Coopermata/Sicoob (CNAB240, 240 posições por linha).

## Características do CNAB 240
- `PROFUNDIDADE="4"` no `<LAYOUT>`
- `TAMANHO="242"` (240 + 2 bytes de CR+LF)
- `INICARQREM="COB"`
- Estrutura: Header Arquivo → Header Lote → Segmentos P/Q/R (analíticos) → Trailer Lote → Trailer Arquivo

## Estrutura XML completa

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<LAYOUTS MODULO="B" QUANTIDADE="1" TIPO="Remessa">
  <LAYOUT PROFUNDIDADE="4" ANALITICO="N" ORDENAR="N" FICHA="N"
          ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="COB">
    <TITULO><![CDATA[Remessa NomeDoBanco]]></TITULO>
    <LINHAS QUANTIDADE="2">

      <!-- ═══════════════════════════════════════════════
           HEADER DO ARQUIVO — Registro tipo "0"
           ═══════════════════════════════════════════════ -->
      <LINHA TAMANHO="242" ANALITICO="N" ORDENAR="N" FICHA="N"
             ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="COB">
        <TITULO><![CDATA[Header Arquivo - 0]]></TITULO>
        <COLUNAS>
          <!-- Pos 001-003: Código do banco na compensação -->
          <COLUNA SEQUENCIA="1" TAMANHO="3" TIPO="C">
            <CAMPO><![CDATA[Dados_Detalhe.CODIGO_BANCO]]></CAMPO>
          </COLUNA>
          <!-- Pos 004-007: Lote = "0000" -->
          <COLUNA SEQUENCIA="2" TAMANHO="4" TIPO="C">
            <CAMPO><![CDATA[0]]></CAMPO>
          </COLUNA>
          <!-- Pos 008: Tipo de registro = "0" -->
          <COLUNA SEQUENCIA="3" TAMANHO="1" TIPO="C">
            <CAMPO><![CDATA[0]]></CAMPO>
          </COLUNA>
          <!-- Pos 009-017: CNAB - brancos -->
          <COLUNA SEQUENCIA="4" TAMANHO="9" TIPO="E">
            <CAMPO><![CDATA['']]></CAMPO>
          </COLUNA>
          <!-- Pos 018: Tipo inscrição empresa (1=CPF, 2=CNPJ) -->
          <COLUNA SEQUENCIA="5" TAMANHO="1" TIPO="C">
            <CAMPO><![CDATA[IF(Dados_Detalhe.TAMANHO_CGC_CPF = 14,2,1)]]></CAMPO>
          </COLUNA>
          <!-- Pos 019-032: CNPJ/CPF da empresa -->
          <COLUNA SEQUENCIA="6" TAMANHO="14" TIPO="F">
            <CAMPO><![CDATA[Dados_Detalhe.CGC_EMPRESA]]></CAMPO>
          </COLUNA>
          <!-- Pos 033-052: Código do convênio (brancos se não usado) -->
          <COLUNA SEQUENCIA="7" TAMANHO="20" TIPO="E">
            <CAMPO><![CDATA['']]></CAMPO>
          </COLUNA>
          <!-- Pos 053-057: Código/Prefixo da Agência -->
          <COLUNA SEQUENCIA="8" TAMANHO="5" TIPO="C">
            <CAMPO><![CDATA[Dados_Gerais.CODIGO_AGENCIA]]></CAMPO>
          </COLUNA>
          <!-- Pos 058: DV da Agência -->
          <COLUNA SEQUENCIA="9" TAMANHO="1" TIPO="E">
            <CAMPO><![CDATA[Dados_Gerais.DIGITO_AGENCIA]]></CAMPO>
          </COLUNA>
          <!-- Pos 059-070: Número da conta corrente -->
          <COLUNA SEQUENCIA="10" TAMANHO="12" TIPO="C">
            <CAMPO><![CDATA[Dados_Gerais.CONTA]]></CAMPO>
          </COLUNA>
          <!-- Pos 071: DV da conta -->
          <COLUNA SEQUENCIA="11" TAMANHO="1" TIPO="E">
            <CAMPO><![CDATA[Dados_Gerais.DIGITO_CONTA]]></CAMPO>
          </COLUNA>
          <!-- Pos 072: DV Agência/Conta (zeros) -->
          <COLUNA SEQUENCIA="12" TAMANHO="1" TIPO="E">
            <CAMPO><![CDATA['']]></CAMPO>
          </COLUNA>
          <!-- Pos 073-102: Nome da empresa (30 chars) -->
          <COLUNA SEQUENCIA="13" TAMANHO="30" TIPO="E">
            <CAMPO><![CDATA[Dados_Gerais.RAZAOSOCIAL_EMPRESA]]></CAMPO>
          </COLUNA>
          <!-- Pos 103-132: Nome do banco (30 chars) -->
          <COLUNA SEQUENCIA="14" TAMANHO="30" TIPO="E">
            <CAMPO><![CDATA[Dados_Gerais.NOME_BANCO]]></CAMPO>
          </COLUNA>
          <!-- Pos 133-142: CNAB - brancos -->
          <COLUNA SEQUENCIA="15" TAMANHO="10" TIPO="E">
            <CAMPO><![CDATA['']]></CAMPO>
          </COLUNA>
          <!-- Pos 143: Código Remessa = "1" -->
          <COLUNA SEQUENCIA="16" TAMANHO="1" TIPO="C">
            <CAMPO><![CDATA[1]]></CAMPO>
          </COLUNA>
          <!-- Pos 144-151: Data de geração DDMMAAAA -->
          <COLUNA SEQUENCIA="17" TAMANHO="8" TIPO="C">
            <CAMPO><![CDATA[DATAATUAL]]></CAMPO>
          </COLUNA>
          <!-- Pos 152-157: Hora de geração HHMMSS -->
          <COLUNA SEQUENCIA="18" TAMANHO="6" TIPO="C">
            <CAMPO><![CDATA[HORAATUAL]]></CAMPO>
          </COLUNA>
          <!-- Pos 158-163: Número sequencial do arquivo -->
          <COLUNA SEQUENCIA="19" TAMANHO="6" TIPO="C">
            <CAMPO><![CDATA[Dados_Detalhe.NUMERO_REMESSA]]></CAMPO>
          </COLUNA>
          <!-- Pos 164-166: Versão do layout = "081" ou "041" conforme banco -->
          <COLUNA SEQUENCIA="20" TAMANHO="3" TIPO="C">
            <CAMPO><![CDATA['081']]></CAMPO>
          </COLUNA>
          <!-- Pos 167-171: Densidade = "00000" -->
          <COLUNA SEQUENCIA="21" TAMANHO="5" TIPO="C">
            <CAMPO><![CDATA['00000']]></CAMPO>
          </COLUNA>
          <!-- Pos 172-191: Reservado banco (brancos) -->
          <COLUNA SEQUENCIA="22" TAMANHO="20" TIPO="C">
            <CAMPO><![CDATA[0]]></CAMPO>
          </COLUNA>
          <!-- Pos 192-211: Reservado empresa (brancos) -->
          <COLUNA SEQUENCIA="23" TAMANHO="20" TIPO="C">
            <CAMPO><![CDATA[0]]></CAMPO>
          </COLUNA>
          <!-- Pos 212-240: CNAB (brancos) -->
          <COLUNA SEQUENCIA="24" TAMANHO="29" TIPO="E">
            <CAMPO><![CDATA['']]></CAMPO>
          </COLUNA>
          <!-- Fim de linha -->
          <COLUNA SEQUENCIA="29" TAMANHO="2" TIPO="C">
            <CAMPO><![CDATA[FIMLINHA]]></CAMPO>
          </COLUNA>
        </COLUNAS>

        <!-- ═══════════════════════════════════════════════
             HEADER DO LOTE — Registro tipo "1"
             ═══════════════════════════════════════════════ -->
        <LINHAS QUANTIDADE="2">
          <LINHA TAMANHO="242" ANALITICO="N" ORDENAR="N" FICHA="N"
                 ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="COB">
            <TITULO><![CDATA[Header Lote - 1]]></TITULO>
            <COLUNAS>
              <!-- Pos 001-003: Código do banco -->
              <COLUNA SEQUENCIA="1" TAMANHO="3" TIPO="C">
                <CAMPO><![CDATA[Dados_Detalhe.CODIGO_BANCO]]></CAMPO>
              </COLUNA>
              <!-- Pos 004-007: Lote sequencial -->
              <COLUNA SEQUENCIA="2" TAMANHO="4" TIPO="C">
                <CAMPO><![CDATA[SEQUENCIALOTE]]></CAMPO>
              </COLUNA>
              <!-- Pos 008: Tipo de registro = "1" -->
              <COLUNA SEQUENCIA="3" TAMANHO="1" TIPO="C">
                <CAMPO><![CDATA[1]]></CAMPO>
              </COLUNA>
              <!-- Pos 009: Tipo operação = "R" -->
              <COLUNA SEQUENCIA="4" TAMANHO="1" TIPO="E">
                <CAMPO><![CDATA['R']]></CAMPO>
              </COLUNA>
              <!-- Pos 010-011: Tipo serviço = "01" -->
              <COLUNA SEQUENCIA="5" TAMANHO="2" TIPO="C">
                <CAMPO><![CDATA[1]]></CAMPO>
              </COLUNA>
              <!-- Pos 012-013: CNAB brancos -->
              <COLUNA SEQUENCIA="6" TAMANHO="2" TIPO="E" />
              <!-- Pos 014-016: Versão layout lote = "040" ou "045" conforme banco -->
              <COLUNA SEQUENCIA="7" TAMANHO="3" TIPO="C">
                <CAMPO><![CDATA['040']]></CAMPO>
              </COLUNA>
              <!-- Pos 017: CNAB branco -->
              <COLUNA SEQUENCIA="8" TAMANHO="1" TIPO="E">
                <CAMPO><![CDATA[' ']]></CAMPO>
              </COLUNA>
              <!-- Pos 018: Tipo inscrição empresa -->
              <COLUNA SEQUENCIA="9" TAMANHO="1" TIPO="C">
                <CAMPO><![CDATA[IF(Dados_Detalhe.TAMANHO_CGC_CPF = 14,2,1)]]></CAMPO>
              </COLUNA>
              <!-- Pos 019-033: CNPJ empresa (15 chars no lote) -->
              <COLUNA SEQUENCIA="10" TAMANHO="15" TIPO="C">
                <CAMPO><![CDATA[Dados_Detalhe.CGC_EMPRESA]]></CAMPO>
              </COLUNA>
              <!-- Pos 034-053: Código do convênio (brancos) -->
              <COLUNA SEQUENCIA="11" TAMANHO="20" TIPO="E">
                <CAMPO><![CDATA['']]></CAMPO>
              </COLUNA>
              <!-- Pos 054-058: Agência -->
              <COLUNA SEQUENCIA="12" TAMANHO="5" TIPO="C">
                <CAMPO><![CDATA[Dados_Gerais.CODIGO_AGENCIA]]></CAMPO>
              </COLUNA>
              <!-- Pos 059: DV Agência -->
              <COLUNA SEQUENCIA="13" TAMANHO="1" TIPO="E">
                <CAMPO><![CDATA[Dados_Gerais.DIGITO_AGENCIA]]></CAMPO>
              </COLUNA>
              <!-- Pos 060-071: Conta -->
              <COLUNA SEQUENCIA="14" TAMANHO="12" TIPO="C">
                <CAMPO><![CDATA[Dados_Gerais.CONTA]]></CAMPO>
              </COLUNA>
              <!-- Pos 072: DV Conta -->
              <COLUNA SEQUENCIA="15" TAMANHO="1" TIPO="E">
                <CAMPO><![CDATA[Dados_Gerais.DIGITO_CONTA]]></CAMPO>
              </COLUNA>
              <!-- Pos 073: DV Ag/Conta (branco) -->
              <COLUNA SEQUENCIA="16" TAMANHO="1" TIPO="E">
                <CAMPO><![CDATA['']]></CAMPO>
              </COLUNA>
              <!-- Pos 074-103: Nome da empresa -->
              <COLUNA SEQUENCIA="17" TAMANHO="30" TIPO="E">
                <CAMPO><![CDATA[Dados_Gerais.RAZAOSOCIAL_EMPRESA]]></CAMPO>
              </COLUNA>
              <!-- Pos 104-143: Mensagem 1 (brancos) -->
              <COLUNA SEQUENCIA="18" TAMANHO="40" TIPO="E">
                <CAMPO><![CDATA['']]></CAMPO>
              </COLUNA>
              <!-- Pos 144-183: Mensagem 2 (brancos) -->
              <COLUNA SEQUENCIA="19" TAMANHO="40" TIPO="E">
                <CAMPO><![CDATA['']]></CAMPO>
              </COLUNA>
              <!-- Pos 184-191: Número Remessa/Retorno -->
              <COLUNA SEQUENCIA="20" TAMANHO="8" TIPO="C">
                <CAMPO><![CDATA[Dados_Detalhe.NUMERO_REMESSA]]></CAMPO>
              </COLUNA>
              <!-- Pos 192-199: Data de gravação -->
              <COLUNA SEQUENCIA="21" TAMANHO="8" TIPO="C">
                <CAMPO><![CDATA[DATAATUAL]]></CAMPO>
              </COLUNA>
              <!-- Pos 200-207: Data crédito = "00000000" -->
              <COLUNA SEQUENCIA="22" TAMANHO="8" TIPO="C">
                <CAMPO><![CDATA[0]]></CAMPO>
              </COLUNA>
              <!-- Pos 208-240: CNAB brancos -->
              <COLUNA SEQUENCIA="23" TAMANHO="33" TIPO="E">
                <CAMPO><![CDATA[' ']]></CAMPO>
              </COLUNA>
              <!-- Fim de linha -->
              <COLUNA SEQUENCIA="24" TAMANHO="2" TIPO="C">
                <CAMPO><![CDATA[FIMLINHA]]></CAMPO>
              </COLUNA>
            </COLUNAS>

            <!-- ═══════════════════════════════════════════════
                 SEGMENTOS — Registros tipo "3" (analíticos)
                 ═══════════════════════════════════════════════ -->
            <LINHAS QUANTIDADE="3">

              <!-- SEGMENTO P — dados principais do título -->
              <LINHA TAMANHO="242" ANALITICO="S" ORDENAR="N" FICHA="N"
                     ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="COB">
                <TITULO><![CDATA[Segmento - 3P]]></TITULO>
                <COLUNAS>
                  <COLUNA SEQUENCIA="1" TAMANHO="3" TIPO="C">
                    <CAMPO><![CDATA[Dados_Detalhe.CODIGO_BANCO]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="2" TAMANHO="4" TIPO="C">
                    <CAMPO><![CDATA[SEQUENCIALOTE]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="3" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[3]]></CAMPO>
                  </COLUNA>
                  <!-- Nº sequencial no lote -->
                  <COLUNA SEQUENCIA="4" TAMANHO="5" TIPO="C">
                    <CAMPO><![CDATA[NROSEQUENCIAL]]></CAMPO>
                  </COLUNA>
                  <!-- Segmento = "P" -->
                  <COLUNA SEQUENCIA="5" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA['P']]></CAMPO>
                  </COLUNA>
                  <!-- CNAB branco -->
                  <COLUNA SEQUENCIA="6" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA[' ']]></CAMPO>
                  </COLUNA>
                  <!-- Código movimento remessa = "01" (entrada) -->
                  <COLUNA SEQUENCIA="7" TAMANHO="2" TIPO="C">
                    <CAMPO><![CDATA[01]]></CAMPO>
                  </COLUNA>
                  <!-- Agência -->
                  <COLUNA SEQUENCIA="8" TAMANHO="5" TIPO="C">
                    <CAMPO><![CDATA[Dados_Gerais.CODIGO_AGENCIA]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="9" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA[Dados_Gerais.DIGITO_AGENCIA]]></CAMPO>
                  </COLUNA>
                  <!-- Conta -->
                  <COLUNA SEQUENCIA="10" TAMANHO="12" TIPO="C">
                    <CAMPO><![CDATA[Dados_Gerais.CONTA]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="11" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA[Dados_Gerais.DIGITO_CONTA]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="12" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA['']]></CAMPO>
                  </COLUNA>
                  <!-- Nosso número (20 chars no Sicoob, formato específico) -->
                  <COLUNA SEQUENCIA="13" TAMANHO="20" TIPO="E">
                    <CAMPO><![CDATA[Dados_Detalhe.NOSSONUM]]></CAMPO>
                  </COLUNA>
                  <!-- Carteira -->
                  <COLUNA SEQUENCIA="14" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[Dados_Gerais.CARTEIRA]]></CAMPO>
                  </COLUNA>
                  <!-- Forma cadastro título = "1" -->
                  <COLUNA SEQUENCIA="15" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[1]]></CAMPO>
                  </COLUNA>
                  <!-- Tipo documento (branco) -->
                  <COLUNA SEQUENCIA="16" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA['1']]></CAMPO>
                  </COLUNA>
                  <!-- Emissão boleto (1=banco, 2=beneficiário) -->
                  <COLUNA SEQUENCIA="17" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[2]]></CAMPO>
                  </COLUNA>
                  <!-- Distribuição boleto -->
                  <COLUNA SEQUENCIA="18" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA['2']]></CAMPO>
                  </COLUNA>
                  <!-- Número do documento / Seu Número -->
                  <COLUNA SEQUENCIA="19" TAMANHO="15" TIPO="E">
                    <CAMPO><![CDATA[Dados_Detalhe.NUMERO_NOTA + '/' + Dados_Detalhe.DESDOBRAMENTO_NOTA]]></CAMPO>
                  </COLUNA>
                  <!-- Data de vencimento DDMMAAAA -->
                  <COLUNA SEQUENCIA="20" TAMANHO="8" TIPO="C">
                    <CAMPO><![CDATA[Dados_Detalhe.DATA_VENCIMENTO]]></CAMPO>
                  </COLUNA>
                  <!-- Valor nominal do título (13 chars, 2 decimais) -->
                  <COLUNA SEQUENCIA="21" TAMANHO="15" TIPO="A" QTDDEC="2">
                    <CAMPO><![CDATA[Dados_Detalhe.VALOR_LIQUIDO]]></CAMPO>
                  </COLUNA>
                  <!-- Agência cobradora = "00000" -->
                  <COLUNA SEQUENCIA="22" TAMANHO="5" TIPO="C">
                    <CAMPO><![CDATA['00000000']]></CAMPO>
                  </COLUNA>
                  <!-- DV agência cobradora (branco) -->
                  <COLUNA SEQUENCIA="23" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA[' ']]></CAMPO>
                  </COLUNA>
                  <!-- Espécie = "02" (DM - Duplicata Mercantil) -->
                  <COLUNA SEQUENCIA="24" TAMANHO="2" TIPO="C">
                    <CAMPO><![CDATA[02]]></CAMPO>
                  </COLUNA>
                  <!-- Aceite = "N" -->
                  <COLUNA SEQUENCIA="25" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA['N']]></CAMPO>
                  </COLUNA>
                  <!-- Data emissão -->
                  <COLUNA SEQUENCIA="26" TAMANHO="8" TIPO="C">
                    <CAMPO><![CDATA[Dados_Detalhe.DATA_NEGOCIACAO]]></CAMPO>
                  </COLUNA>
                  <!-- Código juros mora = "2" (taxa mensal) -->
                  <COLUNA SEQUENCIA="27" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[2]]></CAMPO>
                  </COLUNA>
                  <!-- Data início juros -->
                  <COLUNA SEQUENCIA="28" TAMANHO="8" TIPO="C">
                    <CAMPO><![CDATA[Dados_Detalhe.DATA_VENCIMENTO]]></CAMPO>
                  </COLUNA>
                  <!-- Taxa de juros (ex: 250 = 2,50% ao mês) -->
                  <COLUNA SEQUENCIA="29" TAMANHO="15" TIPO="A" QTDDEC="2">
                    <CAMPO><![CDATA['250']]></CAMPO>
                  </COLUNA>
                  <!-- Código desconto = "0" (sem desconto) -->
                  <COLUNA SEQUENCIA="30" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- Data desconto (zeros) -->
                  <COLUNA SEQUENCIA="31" TAMANHO="8" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- Valor desconto (zeros) -->
                  <COLUNA SEQUENCIA="32" TAMANHO="15" TIPO="A" QTDDEC="2">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- IOF (zeros) -->
                  <COLUNA SEQUENCIA="33" TAMANHO="15" TIPO="A" QTDDEC="2">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- Abatimento (zeros) -->
                  <COLUNA SEQUENCIA="34" TAMANHO="15" TIPO="A" QTDDEC="2">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- Identificação título empresa (NUFIN) -->
                  <COLUNA SEQUENCIA="35" TAMANHO="25" TIPO="E">
                    <CAMPO><![CDATA[Dados_Detalhe.NUMERO_UNICO_FINANCEIRO]]></CAMPO>
                  </COLUNA>
                  <!-- Código protesto = "3" (não protestar) -->
                  <COLUNA SEQUENCIA="36" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[3]]></CAMPO>
                  </COLUNA>
                  <!-- Prazo protesto = "00" -->
                  <COLUNA SEQUENCIA="37" TAMANHO="2" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- Código baixa = "2" -->
                  <COLUNA SEQUENCIA="38" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA['2']]></CAMPO>
                  </COLUNA>
                  <!-- Prazo baixa -->
                  <COLUNA SEQUENCIA="39" TAMANHO="3" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- Código moeda = "09" (Real) -->
                  <COLUNA SEQUENCIA="40" TAMANHO="2" TIPO="C">
                    <CAMPO><![CDATA[09]]></CAMPO>
                  </COLUNA>
                  <!-- Número contrato / zeros -->
                  <COLUNA SEQUENCIA="41" TAMANHO="10" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- CNAB branco -->
                  <COLUNA SEQUENCIA="42" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA['']]></CAMPO>
                  </COLUNA>
                  <!-- Fim de linha -->
                  <COLUNA SEQUENCIA="43" TAMANHO="2" TIPO="C">
                    <CAMPO><![CDATA[FIMLINHA]]></CAMPO>
                  </COLUNA>
                </COLUNAS>
              </LINHA>

              <!-- SEGMENTO Q — dados do pagador -->
              <LINHA TAMANHO="242" ANALITICO="S" ORDENAR="N" FICHA="N"
                     ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="COB">
                <TITULO><![CDATA[Segmento - 3Q]]></TITULO>
                <COLUNAS>
                  <COLUNA SEQUENCIA="1" TAMANHO="3" TIPO="C">
                    <CAMPO><![CDATA[Dados_Detalhe.CODIGO_BANCO]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="2" TAMANHO="4" TIPO="C">
                    <CAMPO><![CDATA[SEQUENCIALOTE]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="3" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[3]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="4" TAMANHO="5" TIPO="C">
                    <CAMPO><![CDATA[NROSEQUENCIAL]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="5" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA['Q']]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="6" TAMANHO="1" TIPO="E">
                    <CAMPO><![CDATA[' ']]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="7" TAMANHO="2" TIPO="C">
                    <CAMPO><![CDATA[01]]></CAMPO>
                  </COLUNA>
                  <!-- Tipo inscrição pagador -->
                  <COLUNA SEQUENCIA="8" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[IF(Dados_Detalhe.TAMANHO_CGC_CPF = 14,2,1)]]></CAMPO>
                  </COLUNA>
                  <!-- CPF/CNPJ pagador -->
                  <COLUNA SEQUENCIA="9" TAMANHO="15" TIPO="C">
                    <CAMPO><![CDATA[Dados_Detalhe.CGC_CPF_PARCEIRO]]></CAMPO>
                  </COLUNA>
                  <!-- Nome pagador -->
                  <COLUNA SEQUENCIA="10" TAMANHO="40" TIPO="E">
                    <CAMPO><![CDATA[Dados_Detalhe.RAZAOSOCIAL_PARCEIRO]]></CAMPO>
                  </COLUNA>
                  <!-- Endereço -->
                  <COLUNA SEQUENCIA="11" TAMANHO="40" TIPO="E">
                    <CAMPO><![CDATA[Dados_Detalhe.TIPO_ENDERECO+' '+Dados_Detalhe.ENDERECO_PARCEIRO+', '+Dados_Detalhe.NUMERO_END_PARCEIRO]]></CAMPO>
                  </COLUNA>
                  <!-- Bairro -->
                  <COLUNA SEQUENCIA="12" TAMANHO="15" TIPO="E">
                    <CAMPO><![CDATA['']]></CAMPO>
                  </COLUNA>
                  <!-- CEP (5 dígitos) -->
                  <COLUNA SEQUENCIA="13" TAMANHO="5" TIPO="C">
                    <CAMPO><![CDATA[Dados_Detalhe.CEP_PARCEIRO]]></CAMPO>
                  </COLUNA>
                  <!-- Sufixo CEP (3 dígitos) -->
                  <COLUNA SEQUENCIA="14" TAMANHO="3" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- Cidade -->
                  <COLUNA SEQUENCIA="15" TAMANHO="15" TIPO="E">
                    <CAMPO><![CDATA[Dados_Detalhe.CIDADE_PARCEIRO]]></CAMPO>
                  </COLUNA>
                  <!-- UF -->
                  <COLUNA SEQUENCIA="16" TAMANHO="2" TIPO="E">
                    <CAMPO><![CDATA[Dados_Detalhe.UF_PARCEIRO]]></CAMPO>
                  </COLUNA>
                  <!-- Sacador/Avalista: tipo inscrição (0=isento) -->
                  <COLUNA SEQUENCIA="17" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- Sacador: inscrição (zeros) -->
                  <COLUNA SEQUENCIA="18" TAMANHO="15" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- Nome sacador (brancos) -->
                  <COLUNA SEQUENCIA="19" TAMANHO="40" TIPO="E">
                    <CAMPO><![CDATA['']]></CAMPO>
                  </COLUNA>
                  <!-- Banco correspondente (000 = não usa) -->
                  <COLUNA SEQUENCIA="20" TAMANHO="3" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <!-- Nosso número banco correspondente (zeros) -->
                  <COLUNA SEQUENCIA="21" TAMANHO="20" TIPO="E">
                    <CAMPO><![CDATA['']]></CAMPO>
                  </COLUNA>
                  <!-- CNAB brancos -->
                  <COLUNA SEQUENCIA="22" TAMANHO="8" TIPO="E">
                    <CAMPO><![CDATA['']]></CAMPO>
                  </COLUNA>
                  <!-- Fim de linha -->
                  <COLUNA SEQUENCIA="23" TAMANHO="2" TIPO="C">
                    <CAMPO><![CDATA[FIMLINHA]]></CAMPO>
                  </COLUNA>
                </COLUNAS>
              </LINHA>

              <!-- TRAILER DO LOTE — Registro tipo "5" -->
              <LINHA TAMANHO="242" ANALITICO="N" ORDENAR="N" FICHA="N"
                     ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="COB">
                <TITULO><![CDATA[Trailer Lote - 5]]></TITULO>
                <COLUNAS>
                  <COLUNA SEQUENCIA="1" TAMANHO="3" TIPO="C">
                    <CAMPO><![CDATA[Dados_Detalhe.CODIGO_BANCO]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="2" TAMANHO="4" TIPO="C">
                    <CAMPO><![CDATA[SEQUENCIALOTE]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="3" TAMANHO="1" TIPO="C">
                    <CAMPO><![CDATA[5]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="4" TAMANHO="9" TIPO="E">
                    <CAMPO><![CDATA['']]></CAMPO>
                  </COLUNA>
                  <!-- Qtde registros no lote -->
                  <COLUNA SEQUENCIA="5" TAMANHO="6" TIPO="C">
                    <CAMPO><![CDATA[NROSEQUENCIAL]]></CAMPO>
                  </COLUNA>
                  <!-- Demais totalizadores (zeros) -->
                  <COLUNA SEQUENCIA="6" TAMANHO="6" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="7" TAMANHO="17" TIPO="C">
                    <CAMPO><![CDATA[0]]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="8" TAMANHO="117" TIPO="E">
                    <CAMPO><![CDATA['']]></CAMPO>
                  </COLUNA>
                  <COLUNA SEQUENCIA="9" TAMANHO="2" TIPO="C">
                    <CAMPO><![CDATA[FIMLINHA]]></CAMPO>
                  </COLUNA>
                </COLUNAS>
              </LINHA>

            </LINHAS>
          </LINHA>

          <!-- ═══════════════════════════════════════════════
               TRAILER DO ARQUIVO — Registro tipo "9"
               ═══════════════════════════════════════════════ -->
          <LINHA TAMANHO="242" ANALITICO="N" ORDENAR="N" FICHA="N"
                 ARQPORLINHA="N" UTILIZASEQALT="N" UTILIZASEQINFO="N" INICARQREM="COB">
            <TITULO><![CDATA[Trailler Arquivo - 9]]></TITULO>
            <COLUNAS>
              <COLUNA SEQUENCIA="1" TAMANHO="3" TIPO="C">
                <CAMPO><![CDATA[Dados_Detalhe.CODIGO_BANCO]]></CAMPO>
              </COLUNA>
              <!-- Lote = "9999" -->
              <COLUNA SEQUENCIA="2" TAMANHO="4" TIPO="C">
                <CAMPO><![CDATA['9999']]></CAMPO>
              </COLUNA>
              <!-- Tipo = "9" -->
              <COLUNA SEQUENCIA="3" TAMANHO="1" TIPO="C">
                <CAMPO><![CDATA[9]]></CAMPO>
              </COLUNA>
              <!-- CNAB brancos -->
              <COLUNA SEQUENCIA="4" TAMANHO="9" TIPO="E">
                <CAMPO><![CDATA['']]></CAMPO>
              </COLUNA>
              <!-- Qtde lotes -->
              <COLUNA SEQUENCIA="5" TAMANHO="6" TIPO="C">
                <CAMPO><![CDATA[SEQUENCIALOTE]]></CAMPO>
              </COLUNA>
              <!-- Qtde registros -->
              <COLUNA SEQUENCIA="6" TAMANHO="6" TIPO="C">
                <CAMPO><![CDATA[NROSEQUENCIAL]]></CAMPO>
              </COLUNA>
              <!-- Qtde contas conciliação = "000000" -->
              <COLUNA SEQUENCIA="7" TAMANHO="6" TIPO="C">
                <CAMPO><![CDATA[0]]></CAMPO>
              </COLUNA>
              <!-- CNAB brancos -->
              <COLUNA SEQUENCIA="8" TAMANHO="205" TIPO="E">
                <CAMPO><![CDATA['']]></CAMPO>
              </COLUNA>
              <!-- Fim de linha -->
              <COLUNA SEQUENCIA="9" TAMANHO="2" TIPO="C">
                <CAMPO><![CDATA[FIMLINHA]]></CAMPO>
              </COLUNA>
            </COLUNAS>
          </LINHA>

        </LINHAS>
      </LINHA>

    </LINHAS>
  </LAYOUT>
</LAYOUTS>
```

## Notas Importantes para CNAB 240

- A soma dos TAMANHOs de todas as COLUNAs por LINHA (sem contar FIMLINHA) deve ser 240
- Segmentos P e Q são os mínimos obrigatórios para a maioria dos bancos
- Segmento R é opcional (desconto 2 e 3, multa, mensagens extras)
- A versão do layout varia por banco: confirmar no PDF (ex: "081", "040", "045")
- `SEQUENCIALOTE` é gerenciado automaticamente pelo Sankhya — não calcular manualmente
- `NROSEQUENCIAL` incrementa por segmento dentro do lote (P=1, Q=2, R=3...)
