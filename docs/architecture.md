# Architecture

## Data Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                        DATA SOURCES                             │
├──────────────────┬──────────────────┬───────────────────────────┤
│  UAU-based ERP   │ Azure Databricks │      SharePoint           │
│  (SQL Server)    │  (Warehouse)     │  (Files & Lists)          │
│                  │                  │                           │
│  - Procurement   │  - Indicators    │  - Spreadsheets           │
│  - Finance       │  - Time series   │  - Portfolio data         │
│  - Construction  │  - Aggregations  │  - Cost benchmarks        │
└────────┬─────────┴────────┬─────────┴─────────────┬─────────────┘
         │                  │                       │
         ▼                  ▼                       ▼
┌─────────────────────────────────────────────────────────────────┐
│                    POWER QUERY (M)                               │
│  Data ingestion, type casting, column renaming, joins           │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                      DATA MODEL                                  │
│  Star schema: fact tables + dimension tables                     │
│  Key relationships: date tables, indicator lookups, project dims │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    DAX MEASURES (1,209)                           │
├─────────────────┬───────────┬──────────┬──────────┬─────────────┤
│ Portfolio       │    DVQ    │Portfolio │Suprimen- │ Medias de   │
│ Gestao Imob.    │           │Dashboard │  tos     │ Custos      │
│ (758 measures)  │(184 meas.)│(152 meas)│(82 meas.)│ (33 meas.)  │
└─────────────────┴───────────┴──────────┴──────────┴─────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                     POWER BI SERVICE                             │
│            Published dashboards + scheduled refresh              │
└─────────────────────────────────────────────────────────────────┘
```

## Key DAX Patterns

### 1. Real Estate KPIs (TIR, VPL, VGV, Margem)

Each indicator is stored in a normalized fact table (`indicadores_d1`) with an `id_indicador` code. DAX measures filter by indicator code to extract the target (meta) and realized (realizado) values:

```dax
// TIR (Internal Rate of Return) - Realized value
IF(
    CALCULATE(
        SUM(indicadores_d1[realizado]),
        FILTER(indicadores_d1, indicadores_d1[id_indicador] = "E5")
    ) = BLANK(),
    0,
    CALCULATE(
        SUM(indicadores_d1[realizado]),
        FILTER(indicadores_d1, indicadores_d1[id_indicador] = "E5")
    )
)
```

**Key indicators**:
- **TIR** (Taxa Interna de Retorno / Internal Rate of Return): Monthly IRR for the development project
- **VPL** (Valor Presente Liquido / Net Present Value): NPV of projected cash flows
- **VGV** (Valor Geral de Vendas / Gross Sales Value): Total sales value of all units in a development
- **Margem** (Margin): Profit margin as percentage of VGV

### 2. Multi-Format Dynamic Formatting

A single measure displays values in different formats (percentage, currency, integer) depending on which indicator is selected via slicer. Uses VAR declarations for each format, then SWITCH(TRUE()) to pick the right one:

```dax
VAR vFormatoPerc = FORMAT(SUMX('indicadores_d1', indicadores_d1[meta]), "#,##0.00 %")
VAR vWholeNumber = FORMAT(SUMX('indicadores_d1', indicadores_d1[meta]), "#,##0")
VAR vFinanceNumber = FORMAT(SUMX('indicadores_d1', indicadores_d1[meta]), "R$#,##0")

RETURN
SWITCH(TRUE(),
    SELECTEDVALUE('D_Indicadores_D1'[indicador]) IN {"TIR FII (A.M.)", "MARGEM FII", ...},
        vFormatoPerc,
    SELECTEDVALUE('D_Indicadores_D1'[indicador]) IN {"VGV EMPREENDIMENTO", "SALDO ATUAL", ...},
        vFinanceNumber,
    vWholeNumber
)
```

**When to use**: Any dashboard where a single visual must display multiple KPIs with different units. Avoids creating separate visuals per indicator.

### 3. Procurement Trilogy (OC/OS + CTR + REQ)

Three parallel sets of measures for each document type, following the same pattern:

```dax
// Sum for each document type
[Soma R$ OC/OS] = SUM('UAU Ordem Compra'[total])
[Soma R$ CTR]   = SUM('UAU Contratos'[Total])
[Soma R$ Req]   = SUM('UAU Requisicao de Compra (OC)'[total])

// Cumulative (running total)
[Soma Acumulada OC/OS] = CALCULATE([Soma R$ OC/OS],
    FILTER(ALL('dCalendario'), 'dCalendario'[DATA] <= MAX('dCalendario'[DATA])))

// Approval range filtering with SELECTEDVALUE
VAR Minimo = SELECTEDVALUE('Faixa Aprovacao Incorporacao'[Min])
VAR Maximo = SELECTEDVALUE('Faixa Aprovacao Incorporacao'[Max])
RETURN CALCULATE([Soma R$ OC/OS],
    FILTER('UAU Ordem Compra',
        'UAU Ordem Compra'[total] >= Minimo &&
        'UAU Ordem Compra'[total] <= Maximo))
```

**Pattern**: Each document type gets: Sum, Count, Cumulative, SubTotal (ALLSELECTED), Total (REMOVEFILTERS), Range-filtered value, Range percentage.

### 4. Weighted Averages with SUMX + KEEPFILTERS

Used for portfolio-level progress metrics where each development (empreendimento) must be weighted by its A.L.V. (Area Liquida de Venda / Net Sellable Area):

```dax
VAR __CATEGORY_VALUES = VALUES('Planilha1 (2)'[Empreendimento])
RETURN
DIVIDE(
    SUMX(
        KEEPFILTERS(__CATEGORY_VALUES),
        CALCULATE(
            SUM('Planilha1 (2)'[% Fisica Acumulada])
            * SUM('Planilha1 (2)'[A.L.V.])
        )
    ),
    SUMX(
        KEEPFILTERS(__CATEGORY_VALUES),
        CALCULATE(SUM('Planilha1 (2)'[A.L.V.]))
    )
)
```

**Why KEEPFILTERS**: Preserves existing filter context from slicers while iterating over categories. Without it, the VALUES() call would ignore external filters.

### 5. Dynamic Range Filtering with SELECTEDVALUE

Slicer-driven analysis where user selects an approval range (faixa de aprovacao), and measures dynamically filter by min/max values:

```dax
VAR Minimo = SELECTEDVALUE('Faixa Aprovacao Incorporacao'[Min])
VAR Maximo = SELECTEDVALUE('Faixa Aprovacao Incorporacao'[Max])
RETURN
    CALCULATE([base_measure],
        FILTER(fact_table, fact_table[value] >= Minimo && fact_table[value] <= Maximo))
```

**When to use**: Any scenario where users need to slice data by custom value ranges (e.g., contract sizes, payment bands).

### 6. REMOVEFILTERS vs ALLSELECTED vs ALL -- When to Use Each

These three functions appear throughout the procurement dashboard in different contexts:

| Function | Effect | Use Case in This Suite |
|----------|--------|----------------------|
| `REMOVEFILTERS(table)` | Removes all filters from a table | **Grand total**: `[Soma Total R$ OC/OS]` -- total across all purchase orders regardless of any filter |
| `ALLSELECTED(table)` | Removes filters except those from slicers/visual-level filters | **Subtotal**: `[Soma SubTotal R$ OC/OS]` -- total respecting user slicer selections but ignoring row/column grouping |
| `ALL(date_column)` | Removes filter from date column only | **Running total**: `FILTER(ALL('dCalendario'), ...)` -- cumulative sum across all dates up to current |

### 7. Cash Flow Projections (Realized vs. Projected)

Splits time series at a reference date to show realized (historical) and projected (future) values:

```dax
// Realized: dates up to reference date
CALCULATE([base_measure],
    FILTER(dDataFato, dDataFato[DATA] <= MAX(indicadores_d2[data_referencia])))

// Projected: dates after reference date
CALCULATE([base_measure],
    FILTER(dDataFato, dDataFato[DATA] > MAX(indicadores_d2[data_referencia])))
```

This pattern enables dual-axis charts showing where actuals end and projections begin.
