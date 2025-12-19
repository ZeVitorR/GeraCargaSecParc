# Rotina de Carga de Dados para Securitizadoras e Parceiros (THCM)

## 1. Identificação
* **Programa:** `GeraCargaSecParc.prw`
* **Namespace:** `FIN`
* **Autor:** José Vitor Rodrigues
* **Data:** 19/08/2025
* **linguagem:** tlpp
* **Versão:** 1.0
* **Módulo:** Financeiro (SIGAFIN) / Gestão Imobiliária
* **Descrição:** Esta rotina desenvolvida em ADVPL (Protheus) é um processo robusto de extração de dados financeiros e imobiliários, estruturado especificamente para atender às exigências de Securitizadoras e Parceiros Financeiros. Ela atua como uma ponte entre o ERP Protheus e o mercado de capitais, consolidando o lastro necessário para operações de crédito.

## 2. Objetivo
Automatizar a extração e consolidação de dados financeiros e imobiliários do ERP Protheus para geração de relatórios em formato **XLSX**. Este arquivo serve como lastro e fluxo de caixa para operações de securitização de recebíveis ou acompanhamento de parceiros financeiros.

## 3. Fluxo do Processo

### A. Parametrização (`PerguntasRotina`)
O usuário define os filtros iniciais:
1.  **Tipo de Origem:** 1 para Securitizadora ou 2 para Parceiro.
2.  **Nome da Entidade:** Nome exato gravado nos campos customizados `E1_ZZSECUR` ou `E1_ZZPARCE`.

### B. Seleção de Filiais (`SelecionaFilial`)
Interface gráfica que utiliza a classe `FWMarkBrowse` para listar as filiais que possuem registros correspondentes aos parâmetros informados. Apenas filiais marcadas serão processadas.

### C. Geração do Arquivo (`GeraRelatorio`)
O sistema cria um arquivo Excel nativo no diretório `C:\spool\` com nomenclatura dinâmica baseada na data e hora da execução.

## 4. Estrutura do Relatório (Sheets)

| Aba | Descrição | Principais Origens |
| :--- | :--- | :--- |
| **Empresas** | Dados cadastrais das filiais selecionadas. | `SM0` |
| **Empreendimentos** | Cadastro de lotes/projetos vinculados. | `SB1` (B1_GERALOT) |
| **Blocos** | Agrupamento por quadras ou torres. | `SE1` / `SB1` |
| **Unidades** | Detalhes físicos da unidade (Área, Fração, Venda). | `SB1` |
| **Clientes** | Dados de compradores e cônjuges (inclui De/Para de regime de bens). | `SA1` / `SU5` / `AC8` |
| **Contratos** | Status do contrato, data de emissão e valor total. | `SE1` / `SF2` / `SE5` |
| **Participações** | Composição de titulares no contrato. | `SE1` / `SA1` |
| **Parcelas** | Fluxo detalhado (Vencimentos, Índices, Pagamentos, Multas). | `SE1` / `SE5` |
| **Ocorrências** | Log de eventos (Estrutura pronta para integração). | - |

## 5. Regras de Negócio Implementadas

### Mapeamento de Índices Financeiros
A rotina traduz os códigos internos do Protheus para o padrão esperado pelas securitizadoras:
* **IPCA** (`1`) -> `7`
* **IGPM** (`2`) -> `2`
* **INCC** (`3`) -> `1`
* **FIXO** (`6` ou vazio) -> `11`

### Identificação de Distratos
A aba de **Contratos** valida se houve baixa por motivos de distrato (`DIS`, `RES`, `DEV`, etc.) na tabela `SE5`, alterando o status do contrato para `3` (Distratado) ou `2` (Ativo).

### Tratamento de Pagamentos Parciais
Na aba de **Parcelas**, a rotina percorre as baixas (`SE5`) para consolidar pagamentos parciais, garantindo que o valor principal e os juros de mora sejam reportados corretamente por título/parcela.

## 6. Requisitos Técnicos
* **Tabelas Consultadas:** `SE1`, `SE5`, `SA1`, `SB1`, `SF2`, `SU5`, `AC8`, `SM0`.
* **Classes Utilizadas:** `FwPrinterXlsx`, `FWMarkBrowse`, `FWTemporaryTable`, `FWXlsxCellAlignment`.
* **Diretório de Saída:** Necessário acesso de escrita em `C:\spool\`.

## 7. Como Utilizar
1. Acesse o menu do Protheus onde a função `U_GeraCargaSecParc` foi adicionada.
2. Informe o tipo (Securitizadora ou Parceiro) e o nome cadastrado.
3. Selecione as filiais desejadas na tela de marcação.
4. Clique em **Confirma**.
5. O arquivo será gerado e o caminho será exibido em tela ao final do processo.

---
*Documentação gerada em 19/08/2025 para apoio técnico THCM.*
