# Dashboard Catalog

## 1. Portfolio Gestao Imobiliaria (Real Estate Portfolio Management)

**DAX Measures**: 758
**Complexity**: High
**Purpose**: The flagship dashboard providing comprehensive financial KPI tracking for a portfolio of real estate developments. Monitors TIR (Internal Rate of Return), VPL (Net Present Value), VGV (Valor Geral de Vendas / Gross Sales Value), Margem (Margin), cash flow projections, sales performance, and fund exposure across all active projects.

### Top 5 Measures

| Measure | Description |
|---------|-------------|
| `011 - PLANEJADO` | Multi-format dynamic display of planned (target) values. Uses SWITCH(TRUE()) with 20+ indicator categories, formatting as percentage, currency R$, or integer depending on the selected KPI. |
| `012 - REAL_REPROJ` | Mirror of PLANEJADO but for realized/re-projected values. Same dynamic formatting logic applied to `indicadores_d1[realizado]`. |
| `001 - Vendas Realizadas` (Realized Sales) | Cumulative net sales up to the reference date, using CALCULATE with date filtering against `indicadores_d2[data_referencia]`. |
| `001 - TIRMeta` (TIR Target) | Extracts the target Internal Rate of Return by filtering `indicadores_d1` on `id_indicador = "E5"`. Returns 0 instead of BLANK for clean visual display. |
| `001 - FFCX PROJETO REALIZADO` (Realized Cumulative Project Cash Flow) | Running total of project cash flow up to the reference date, using FILTER on date dimension. |

### Data Sources
- Azure Databricks (indicator time series: `indicadores_d1`, `indicadores_d2`, `indicadores_d3`)
- Date dimension table (`dDataFato`)
- Indicator dimension (`D_Indicadores_D1`)

### Special Techniques
- **Multi-format dynamic formatting**: Single measure renders as %, R$, or integer based on slicer
- **Realized vs. projected cash flow split**: Time-based filtering at reference date
- **USERELATIONSHIP for date role-playing**: Switches between active and inactive date relationships
- **REMOVEFILTERS on date columns**: Enables cross-period calculations

---

## 2. DVQ -- Document Verification & Quality

**DAX Measures**: 184
**Complexity**: Medium
**Purpose**: Quality control and financial verification dashboard. Tracks bank balances (saldos gerenciais), unit inventory (estoque), sales activity, accounts payable/receivable, and payment process categorization across multiple process types.

### Top 5 Measures

| Measure | Description |
|---------|-------------|
| `001 - SaldoGerencial` (Managerial Balance) | Current bank balance using LASTNONBLANK to find the most recent date with data per account. |
| `001 - Unidades Estoque` (Inventory Units) | Count of unsold units in inventory, with BLANK-to-zero handling for clean totals. |
| `001 - TotalPagamentos` (Total Payments) | COUNT of payment processes across all types, serving as the denominator for process-type percentages. |
| `005 - Unidades Vendidas` (Sold Units) | Sum of sold units from the sales fact table (`fVendas`). |
| `002 - Cotacao` (Quotation) | Count of quotation-type payment processes (TipoProcPag = 0), used for procurement process classification. |

### Data Sources
- UAU-based ERP (SQL Server): bank balances (`Saldos Gerenciais`), inventory (`fEstoque`), sales (`fVendas`), accounts payable (`Contas a Pagar`), accounts receivable (`Contas Recebidas`), payment processes (`Processos de Pagamento`)

### Special Techniques
- **LASTNONBLANK for balance snapshots**: Finds the most recent non-blank balance per account
- **Payment process classification**: 10+ process types filtered by `TipoProcPag` code (quotation, payment process, quick purchase, linked process, transport, measurement, etc.)
- **BLANK-to-zero wrapping**: Every measure uses IF(CALCULATE(...) = BLANK(), 0, ...) for consistent visual behavior

---

## 3. Dashboard Portfolio (Portfolio Progress)

**DAX Measures**: 152
**Complexity**: Medium-High
**Purpose**: Physical-financial progress tracking for the construction portfolio. Calculates weighted progress metrics across developments, tracks budget adherence through ICP (Indice de Custo Previsto / Cost Performance Index), and compares baseline (Baseline 0) values against trend costs.

### Top 5 Measures

| Measure | Description |
|---------|-------------|
| `% Fisica Acumulada ponderado por A.L.V. por Empreendimento` (Weighted Cumulative Physical Progress) | Weighted average of physical completion across developments, using SUMX + KEEPFILTERS with A.L.V. (Area Liquida de Venda / Net Sellable Area) as weight. |
| `% Financeira Acumulada(por Orcado B0) ponderado por A.L.V. por Empreendimento` (Weighted Cumulative Financial Progress) | Same SUMX + KEEPFILTERS pattern applied to financial execution percentage against Baseline 0 budget. |
| `ICP ponderado por A.L.V. por Empreendimento` (Weighted Cost Performance Index) | Portfolio-level CPI calculated as weighted average of per-project ICP values. ICP > 1 means under budget. |
| `R$ Baseline 0 % de diferenca de R$ Tendencia Custo` (Baseline 0 vs. Cost Trend Variance %) | Percentage difference between baseline budget and cost trend, using VAR pattern for clean DIVIDE. |
| `Criterio tendencia orcamentaria` (Budget Trend Criterion) | Categorical classification: "Acima do Orcado" (Above Budget) or "Abaixo do Orcado" (Below Budget) based on the variance percentage. |

### Data Sources
- SharePoint spreadsheets (`Planilha1 (2)`, `A V A`)
- Physical progress data (`Fisico`)
- Date dimension

### Special Techniques
- **SUMX + KEEPFILTERS weighted averaging**: Core pattern for all portfolio-level metrics
- **MoM% (Month-over-Month)**: Time intelligence using DATEADD for trend analysis
- **Budget variance analysis**: Baseline 0 vs. actual cost trend comparison
- **ISFILTERED guard**: Prevents incorrect time intelligence calculations when non-date columns are filtered

---

## 4. Builders Suprimentos (Procurement/Supply Chain)

**DAX Measures**: 82
**Complexity**: Medium
**Purpose**: Procurement analytics across three document types: OC/OS (Ordem de Compra/Ordem de Servico / Purchase/Service Orders), CTR (Contratos / Contracts), and REQ (Requisicoes / Requisitions). Includes approval range filtering, cumulative spend tracking, and disbursement control (controle de desembolso).

### Top 5 Measures

| Measure | Description |
|---------|-------------|
| `Faixa de Valores OC/OS Incorporacao` (OC/OS Value Range) | Filters purchase orders by a slicer-selected min/max approval range using SELECTEDVALUE. |
| `Soma Acumulada Total` | Running total across all three document types (OC/OS + CTR + REQ), each with its own cumulative CALCULATE over the date dimension. |
| `Soma SubTotal R$ Total` | Subtotal preserving slicer context using ALLSELECTED on each document type's table, summed across all three types. |
| `Sum Cont Des` (Disbursement Control Sum) | Total disbursement value from the `UAU Controle Desembolso` (Disbursement Control) table. |
| `% Faixa R$ Total` (Total Value Range %) | Combined percentage of the range-filtered value vs. the subtotal across all document types. |

### Data Sources
- UAU-based ERP (SQL Server, DirectQuery mode): purchase orders (`UAU Ordem Compra`), contracts (`UAU Contratos`), requisitions (`UAU Requisicao de Compra (OC)`), disbursement control (`UAU Controle Desembolso`)
- Date dimension (`dCalendario`)
- Approval range slicer table (`Faixa Aprovacao Incorporacao`)

### Special Techniques
- **Procurement trilogy pattern**: Parallel measure sets for OC/OS, CTR, REQ with identical structure (Sum, Count, Cumulative, SubTotal, Total, Range)
- **REMOVEFILTERS vs. ALLSELECTED**: Grand totals use REMOVEFILTERS; subtotals use ALLSELECTED to respect slicer context
- **SELECTEDVALUE range filtering**: Dynamic min/max from slicer table drives FILTER conditions
- **DirectQuery mode**: Measures optimized for live query execution against SQL Server

---

## 5. Medias de Custos de Servico (Service Cost Benchmarking)

**DAX Measures**: 33
**Complexity**: Medium
**Purpose**: Cost estimation tool for land developments (loteamentos). Uses weighted averages from historical project data to estimate quantities and costs for new developments based on area, lot count, and sub-service breakdowns. Designed to support feasibility analysis for new land development projects.

### Top 5 Measures

| Measure | Description |
|---------|-------------|
| `medida_qtd_servico_estimada` (Estimated Service Quantity) | Estimates service quantities using a cascading logic: (1) user-provided EAP (Estrutura Analitica do Projeto / Work Breakdown Structure) values, (2) per-m2 averages times lot area, or (3) per-lot averages times lot count. Uses nested IF + HASONEVALUE for aggregation control. |
| `medida_custo_unit_incc_estimada` (Estimated Unit Cost INCC-adjusted) | Multiplies INCC-corrected (Indice Nacional de Custo da Construcao / National Construction Cost Index) historical unit costs by estimated quantities. Uses SUMX for multi-sub-service aggregation. |
| `fix_medida_custo_unit_incc_por_area_loteada` (Fixed Cost per Developed Area) | Aggregates cost estimates across sub-services using SUMMARIZE + SUMX, providing a single total cost figure per development. |
| `AppAreaLoteamento` (Development Area Input) | Reads user-provided development area from the input table (`AppQtLoteamento`), returning "Nao informado" (Not provided) if missing. |
| `medida_lote_por_area_loteamento` (Lots per Development Area) | Calculates lot density ratio using AVERAGEX from historical data, scaled by the user-provided development area. |

### Data Sources
- Historical land development data (`Loteamentos`, `Orcamentos` / Budgets, `Fornecedores` / Suppliers)
- Sub-service quantity benchmarks (`Quantidade de Subservico por Concorrencia` / Sub-service Quantity by Bid)
- User input table (`AppQtLoteamento`)
- Work breakdown structure (`AppEAP`)

### Special Techniques
- **Cascading estimation logic**: Three-level fallback (user input > per-m2 average > per-lot average)
- **AVERAGEX for statistical benchmarks**: Historical averages used as coefficients for cost projection
- **HASONEVALUE aggregation control**: Prevents double-counting when aggregating across sub-service hierarchies
- **TREATAS for cross-table filtering**: Transfers filter context between unrelated tables (e.g., `Loteamentos` to `Fornecedores` via state)
- **INCC indexation**: Cost values corrected for construction cost inflation using national index
