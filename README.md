# Power BI SLA Payment Dashboard

> Multi-source BI dashboard automating vendor SLA calculations — pulling live data from SQL Server, Data Hub, and Palantir Foundry into a single real-time Power BI report.

## 📋 Overview

Manual SLA payment calculations previously required analysts to pull data from multiple disconnected systems, reconcile it in Excel, and produce reports by hand — a process taking weeks per cycle.

This project consolidates all data sources into a single automated Power BI dashboard. SLA scores, penalty/bonus calculations, and partner-level summaries refresh automatically on schedule — zero manual intervention required.

**Role:** Sole architect, data engineer, and analyst

---

## ⚙️ Architecture

```
┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐
│   SQL Server    │   │    Data Hub     │   │    Palantir     │
│  (ops data)     │   │  (partner data) │   │   Foundry       │
└────────┬────────┘   └────────┬────────┘   └────────┬────────┘
         │                     │                      │
         └─────────────────────┼──────────────────────┘
                               ↓
                     Power Query (ETL layer)
                               ↓
                      Data Model (DAX)
                               ↓
                  ┌────────────────────────┐
                  │   Power BI Dashboard   │
                  ├────────────────────────┤
                  │  SLA Scorecards        │
                  │  Penalty/Bonus Calc    │
                  │  Partner Summaries     │
                  │  Trend Analysis        │
                  └────────────────────────┘
```

---

## 🔌 Data Sources

| Source | Data | Connection |
|--------|------|------------|
| SQL Server | Core operational data · transaction records | DirectQuery / Import |
| Data Hub | Partner metadata · contract terms · SLA thresholds | API / Scheduled refresh |
| Palantir Foundry | Enriched ops datasets · historical performance | Palantir connector |

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Data Sources | SQL Server · Data Hub · Palantir Foundry |
| ETL / Transform | Power Query (M) · SQL stored procedures |
| Data Model | DAX · Star schema |
| Visualisation | Power BI · Custom visuals |
| Scheduling | Power BI scheduled refresh |

---

## 📐 Data Model

```
fact_sla_transactions
    ├── dim_partner
    ├── dim_contract_terms
    ├── dim_date
    └── dim_sla_category

Calculated tables:
    ├── sla_scores         (DAX)
    ├── penalty_matrix     (DAX)
    └── bonus_eligibility  (DAX)
```

---

## 📊 Dashboard Pages

| Page | Description |
|------|-------------|
| **Executive Summary** | High-level SLA performance across all partners |
| **Partner Scorecard** | Per-partner SLA score, penalty, and bonus breakdown |
| **Trend Analysis** | Month-on-month SLA performance trends |
| **Calculation Detail** | Granular view of SLA scoring logic and inputs |
| **Payment Summary** | Final payment adjustments per partner per cycle |

---

## 💡 Key DAX Measures

```dax
-- SLA Score
SLA Score =
DIVIDE(
    [Compliant Events],
    [Total Events],
    0
) * 100

-- Penalty Amount
Penalty Amount =
IF(
    [SLA Score] < [SLA Threshold],
    ([SLA Threshold] - [SLA Score]) * [Penalty Rate],
    0
)

-- Bonus Amount
Bonus Amount =
IF(
    [SLA Score] >= [Bonus Threshold],
    [Base Payment] * [Bonus Rate],
    0
)

-- Net Payment
Net Payment = [Base Payment] - [Penalty Amount] + [Bonus Amount]
```

---

## 🗂️ Repository Structure

```
├── pbix/                  # Power BI report files
├── sql/                   # SQL queries and stored procedures
│   ├── source_queries/    # Data extraction queries per source
│   └── staging/           # Staging layer transformations
├── power_query/           # M code for Power Query transformations
├── dax/                   # DAX measure documentation
├── docs/                  # Data dictionary and model documentation
├── sample_data/           # Anonymised sample datasets for testing
└── README.md
```

---

## 🚀 Getting Started

```bash
git clone https://github.com/apoorvchauhan381-code/powerbi-sla-payment-dashboard
cd powerbi-sla-payment-dashboard
```

1. Open `pbix/sla_payment_dashboard.pbix` in Power BI Desktop
2. Update data source credentials in **Transform Data → Data Source Settings**
3. Configure SQL Server, Data Hub, and Palantir connection strings in `config/connections.json`
4. Click **Refresh** to load data
5. Publish to Power BI Service for scheduled refresh

---

## 📝 Notes

All connection strings, credentials, and partner data in sample files are anonymised. Replace with your own source configurations before use.
