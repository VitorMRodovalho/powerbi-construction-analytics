# Business Case

## Industry Context

**Brazilian real estate development (incorporadoras)** is a capital-intensive industry where companies manage multiple simultaneous construction developments, each with its own financial model, procurement pipeline, and physical progress schedule. A typical mid-to-large developer may run 10-30+ active projects (empreendimentos) at any given time.

## Problem

Construction companies need unified visibility into:

- **Portfolio financial health**: Each development has projected and realized cash flows, sales targets (VGV / Gross Sales Value), internal rate of return (TIR), net present value (VPL), and margin targets. Tracking these across dozens of simultaneous projects is error-prone when done manually.
- **Procurement efficiency**: The procurement chain involves three document types -- OC/OS (Purchase/Service Orders), CTR (Contracts), and REQ (Requisitions) -- each requiring approval workflows and value-range analysis. Without consolidated analytics, procurement bottlenecks go undetected.
- **Construction quality control**: Payment processes, document verification, and bank reconciliation require systematic tracking to prevent financial leakage.
- **Cost benchmarking**: Estimating costs for new land developments (loteamentos) requires weighted averages from historical data across similar projects, adjusted by area ratios and subdivision characteristics.

## Solution

A suite of 5 Power BI dashboards covering:

1. **Portfolio Gestao Imobiliaria (Real Estate Portfolio Management)** -- The flagship dashboard with 758 DAX measures covering TIR, VPL, VGV, realized vs. projected cash flows, exposure analysis, and multi-format dynamic indicator display.
2. **DVQ (Document Verification & Quality)** -- Quality control dashboard tracking bank balances, unit inventory (estoque), sales, accounts payable/receivable, and payment process types.
3. **Dashboard Portfolio (Portfolio Progress)** -- Physical-financial progress tracking with weighted metrics (% Fisica Acumulada / Cumulative Physical Progress, % Financeira Acumulada / Cumulative Financial Progress) and ICP (Cost Performance Index).
4. **Builders Suprimentos (Procurement/Supply Chain)** -- Procurement analysis across OC/OS, CTR, and REQ document types with approval range filtering, cumulative spend tracking, and disbursement control.
5. **Medias de Custos de Servico (Service Cost Benchmarking)** -- Cost estimation tool for land developments using weighted averages by area, lot count, and sub-service quantities.

## Value Delivered

- **Unified real estate portfolio view**: Single source of truth for financial KPIs across all active developments
- **Procurement transparency**: End-to-end visibility into purchase orders, contracts, and requisitions with approval-range analysis
- **Construction cost benchmarking**: Data-driven cost estimation for new developments using historical weighted averages
- **Physical-financial progress control**: Real-time tracking of construction progress vs. budget with cost performance indexing

## Technology Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Visualization | Power BI Desktop + Service | Dashboard delivery |
| ERP backend | UAU-based ERP (real estate management system) | Procurement, finance, and construction data |
| Analytics warehouse | Azure Databricks | Data processing and advanced analytics |
| Document storage | SharePoint | Data source integration and document management |
| Query language | DAX + Power Query (M) | Business logic and data transformation |
