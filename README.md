# Power BI Construction Analytics Suite

**5 dashboards for real estate construction portfolio management, procurement, and quality control**

Author: Vitor Rodovalho

---

## Overview

This repository contains the extracted DAX measures, Power Query (M) transformations, data model schemas, and relationship definitions from a production Power BI analytics suite used in Brazilian real estate development (incorporadoras). The suite provides unified visibility across construction portfolio health, procurement efficiency, quality control, and cost benchmarking.

### Key Highlights

- **Real estate financial indicators**: TIR (Internal Rate of Return), VPL (Net Present Value), VGV (Valor Geral de Vendas / Gross Sales Value), Margem (Margin)
- **Multi-format dynamic formatting in DAX**: percentage, currency R$, and whole numbers dynamically selected based on indicator type using VAR + SWITCH(TRUE()) pattern
- **Procurement analysis across 3 document types**: OC/OS (Ordem de Compra/Ordem de Servico / Purchase/Service Orders), CTR (Contratos / Contracts), REQ (Requisicoes / Requisitions)
- **Weighted average pattern** using SUMX + KEEPFILTERS for construction progress metrics
- **Physical-financial progress tracking**: % Fisica Acumulada (Cumulative Physical Progress), % Financeira Acumulada (Cumulative Financial Progress), ICP (Indice de Custo Previsto / Cost Performance Index)
- **1,209 total DAX measures** across 5 dashboards

## Dashboard Inventory

| # | Dashboard | DAX Measures | Focus Area |
|---|-----------|:---:|------------|
| 1 | Portfolio Gestao Imobiliaria (Real Estate Portfolio Management) | 758 | Financial KPIs: TIR, VPL, VGV, cash flow projections |
| 2 | DVQ (Document Verification & Quality) | 184 | Construction quality control, payment processes |
| 3 | Dashboard Portfolio (Portfolio Progress) | 152 | Weighted physical-financial progress, ICP |
| 4 | Builders Suprimentos (Procurement/Supply Chain) | 82 | OC/OS, CTR, REQ analysis with approval ranges |
| 5 | Medias de Custos de Servico (Service Cost Benchmarking) | 33 | Land development cost averaging and estimation |

## Repository Structure

```
powerbi-construction-analytics/
├── README.md
├── LICENSE                          # MIT
├── ANONYMIZATION_RULES.md           # Anonymization methodology
├── .gitignore
├── docs/
│   ├── architecture.md              # Data flow and DAX patterns
│   ├── business-case.md             # Industry context and value proposition
│   └── dashboard-catalog.md         # Detailed dashboard descriptions
├── dax/
│   ├── portfolio-gestao-imobiliaria/  # 758 DAX measures + columns
│   ├── dvq/                           # 184 DAX measures + columns
│   ├── dashboard-portfolio/           # 152 DAX measures + columns
│   ├── suprimentos/                   # 82 DAX measures + columns
│   └── medias-custos/                 # 33 DAX measures + columns
├── power-query/                     # M (Power Query) transformations per dashboard
├── data-model/
│   ├── schema/                      # Table schemas per dashboard (CSV)
│   └── relationships/               # Table relationships per dashboard (CSV)
└── assets/
    └── screenshots/
```

## How to Use

1. **Browse DAX measures**: Each `dax/{dashboard}/measures.md` file contains all DAX expressions organized by table and display folder.
2. **Explore data models**: Schema CSVs in `data-model/schema/` show all tables, columns, and data types. Relationship CSVs in `data-model/relationships/` show how tables connect.
3. **Review Power Query**: Each `power-query/{dashboard}.md` file contains the M code for all data source transformations.
4. **Study patterns**: See `docs/architecture.md` for documented DAX patterns you can reuse.

## Language Note

All documentation is written in English (EN-US). DAX measure names, Power Query column names, and table names remain in their original Portuguese as these are code identifiers tied to the data model. Portuguese terms are followed by English translations in parentheses on first use.

## Anonymization

All sensitive information (server addresses, company names, personal identifiers, cloud workspace URLs) has been replaced with generic placeholders. See [ANONYMIZATION_RULES.md](ANONYMIZATION_RULES.md) for the full methodology.

## Tech Stack

- **Power BI** (Desktop + Service)
- **SQL Server** via UAU-based ERP (real estate management system)
- **Azure Databricks** (analytics warehouse)
- **SharePoint** (document and data source integration)

## License

MIT -- see [LICENSE](LICENSE).
