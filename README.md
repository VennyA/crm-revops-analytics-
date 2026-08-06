# CRM RevOps Analytics

**End to end Revenue Operations analysis of a synthetic B2B SaaS business from lead acquisition to customer churn conducted across Python, PostgreSQL, and Power BI.**

## Executive Summary

This project simulates a real world Revenue Operations and CRM Analytics engagement for a B2B SaaS company operating across five global regions. Using a synthetic dataset containing 10,000 leads across seven interconnected tables, I analyzed the entire customer lifecycle from lead acquisition and sales conversion to revenue generation, customer retention, and overall business health.

The analysis was structured around six core business questions:

Who should we be targeting?
How are we attracting them?
How are we converting them?
How much are they worth?
Are we keeping them?
How healthy is the business overall?

Key findings revealed that CyberSecurity and HealthTech represent the strongest customer segments, combining high conversion rates, revenue potential, and strategic value. Paid Search generated the highest lead volume and revenue, while the largest funnel drop-off occurred between Lead and Marketing Qualified Lead (MQL), highlighting opportunities to improve lead quality and qualification.

From a revenue perspective, Enterprise customers generated $27.7M in ARR, accounting for 63% of total annual recurring revenue. Existing customers also contributed nearly as much revenue as new business through renewals, expansions, and upsells, reinforcing the importance of customer retention and account growth strategies.

Retention analysis identified poor customer support as the leading churn driver, while customer health scores showed a strong relationship with churn risk. Net Revenue Retention (NRR) was calculated at 81.69%, indicating the company cannot currently grow from its existing customer base alone and remains dependent on new customer acquisition to offset revenue loss.

The project concludes with recommendations focused on improving lead qualification, prioritizing high-value Enterprise accounts, reducing preventable churn, strengthening customer success initiatives, and improving revenue retention to support sustainable growth.


## Project Overview

| Field | Details |
|-------|---------|
| Industry | B2B SaaS |
| Domain | Revenue Operations & CRM Analytics |
| Tools | Python, PostgreSQL, Power BI |
| Timeline | 2022 – 2025 |

This project operates within the B2B SaaS industry, focusing on Revenue Operations. The function responsible for aligning sales, marketing, and customer success to drive predictable revenue growth.

The business context simulates a company that needs a centralized view of its overall health: whether it is acquiring the right customers, converting them efficiently, generating sustainable revenue, and retaining them long enough to grow. Leadership needs answers across the full customer lifecycle and not just top of funnel metrics or lagging revenue figures, but a connected view from first lead touch to churn.

This project matters because it mirrors how a working RevOps or CRM Analyst operates in practice. Starting from messy and incomplete data, making structured cleaning decisions, conducting layered analysis across multiple tools, and translating findings into business recommendations that leadership can act on.


## Data Source

The dataset is a synthetic B2B SaaS CRM dataset generated using Python (Faker library) and Claude AI, designed to simulate realistic revenue operations data with intentional data quality issues including inconsistent formatting, missing values, duplicates, and outliers  to mirror real world CRM exports.

| Table | Rows | Columns | Description |
|-------|------|---------|-------------|
| crm_companies | 8,811 | 18 | Company firmographics and account details |
| crm_leads | 10,000 | 25 | Lead records with funnel stage and contact info |
| crm_deals | 1,659 | 18 | Deal pipeline with stage, value, and competitor data |
| crm_customers | 871 | 21 | Converted customers with health and contract data |
| crm_churn | 207 | 14 | Churned customer records with reasons and win-back data |
| crm_pricing | 4 | 22 | Plan tier pricing and feature details |
| crm_buyer_evidence | 1,659 | 11 | Buyer engagement signals per deal |

**Data type:** Transactional B2B CRM data spanning 2022–2025

**Limitations:**
- Synthetic data may not capture all real-world CRM complexities
- 45 customer records have no plan tier — $1.4M in ARR is unattributed for 
- Health scores may be understated due to data quality issues in the metrics
- All relationships are single direction 1:Many. Deals and customers are independent branches off leads and do not connect directly to each other.


## Problem Statement

Despite generating $43.9M in ARR, the business seems to be having critical questions about the sustainability of its growth. Revenue is coming in, but customer retention is uncertain, outbound sales efforts are not producing enough conversions, and leadership lacks a unified view of commercial performance across the full customer lifecycle.

The core challenge is this: **is the business growing in a healthy, sustainable way  or is it masking underlying problems in retention, pipeline quality, and customer fit?**

Without clear answers, the business risks continuing to invest in the wrong segments, losing high-value customers to preventable churn, and making revenue forecasts that don't reflect actual buyer commitment.

To answer this, the analysis was structured around six business questions:

1. **Who should we be targeting?** — Which industries, regions, and company sizes convert best and generate the most value?
2. **How are we attracting them?** — Which lead sources and campaigns drive the highest quality leads?
3. **How are we converting them?** — Where is the funnel breaking down and how long does it take to close?
4. **How much are they worth?** — What is the revenue contribution by plan, segment, and deal type?
5. **Are we keeping them?** — What is driving churn and how effective are retention efforts?
6. **How healthy is the business overall?** — What does the pipeline, NRR, and forecast reliability tell us?


## Tools & Methodology

### Tools Used

| Tool | Purpose |
|------|---------|
| Python (Google Colab) | Exploratory data analysis, data cleaning, and quality checks |
| PostgreSQL | Analytical SQL queries across all business questions |
| Power BI | Data modelling, DAX measures, and interactive dashboard |

### Methodology

**Data Collection**
The dataset was synthetically generated using Python (Faker library) and Claude AI, then loaded into Google Colab as seven CSV files for initial exploration and cleaning.

**Data Cleaning & Preparation**
The following steps were performed across all seven tables:
- Standardized inconsistent categorical values (industry, region, plan tier, funnel stage)
- Removed 180 duplicate lead records identified through contact email and company ID matching
- Set negative deal values and out of range NPS and usage scores to null
- Converted all date columns from mixed text formats to datetime
- Documented expected nulls vs data quality gaps separately
- Records with invalid values were set to null rather than deleted, preserving all other valid fields for analysis. This approach ensured that data integrity while maintaining maximum record coverage across all seven tables.

**Data Modelling**
A snowflake schema was designed in Power BI with `crm_leads` as the central table, connected to all other tables:

* crm_companies → crm_leads (via company_id)
* crm_leads → crm_deals (via lead_id)
* crm_leads → crm_customers (via lead_id)
* crm_customers → crm_churn (via customer_id)
* crm_pricing → crm_deals (via plan_id)
* crm_deals → crm_buyer_evidence (via deal_id)

A DimDate calendar table was created with two active relationships:
* DimDate → crm_customers (via contract_start_date)
* DimDate → crm_deals (via close_date)

**DAX Measures**

| Measure | Description |
|---------|-------------|
| Revenue (ARR) | Total ARR from converted customers |
| MRR | Total monthly recurring revenue |
| NRR | Net Revenue Retention — (ARR + Expansion - Churned ARR) / ARR |
| Win Rate | Closed Won / (Closed Won + Closed Lost) |
| Churn Rate | Churned customers / Total customers |
| Weighted Pipeline | Sum of deal value × probability for open deals only |
| ICP Score | Composite score combining conversion rate, win rate, and churn rate |
| Pipeline Velocity | (Opportunities × Win Rate × Avg Deal Value) / Sales Cycle |
| Avg Sales Cycle | Average days from lead creation to deal close (Closed Won only) |
| Auto-Renewal Rate | % of customers on auto-renewal |
| Customers Due for Renewal | Customers with contract end date within 90 days |
| Renewal Revenue at Risk | ARR from customers not on auto-renewal |
| Expansion Revenue % | Expansion ARR as % of total ARR |
| Healthy Customers % | % of customers classified as Healthy |
| Win-Back Success Rate | Successful win-backs / Total win-back attempts |

