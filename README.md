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


## Exploratory Data Analysis (EDA)

An in depth EDA was conducted across all seven tables to assess data quality, understand distributions, and identify patterns before analysis.

### Structure
All six primary keys were confirmed unique across their respective tables. Foreign key integrity was validated, all relationships returned 100% match rates, confirming no ununique records across the schema.

### Key Patterns
Significant inconsistent formatting was identified across categorical columns including `industry`, `region`, `company_size`, `funnel_stage`, `plan_tier`, and `lead_source`. The same values appeared in multiple formats — mixed casing, abbreviations, spacing variations, and alternate labels for the same category (e.g. "MQL", "Marketing Qualified", "marketing qualified lead"). Standardisation was required before any grouping or aggregation.

### Distributions
Deal values showed a wide spread ranging from negative values (data entry errors) to over $5.6M (inflated outliers). MRR ranged from $46 to $21,843 per customer, indicating a mix of small Starter plan accounts and large Enterprise accounts. Days to close ranged from under 10 days to over 100 days depending on deal complexity and segment.

### Trends
Revenue from new contracts grew consistently from $11M in 2022 to $15.7M in 2024. Churn decreased as a proportion of the customer base over the same period. 2025 figures represent partial year data (January to October) and are not directly comparable to full prior years.

### Outliers
Several columns contained impossible or inflated values requiring correction:
- 12 records with negative `deal_value_annual_usd` — set to null
- 5 records with inflated deal values exceeding $3M — set to null
- 15 records with `nps_score` outside the valid 0–10 range — set to null
- 10 records with `product_usage_score` outside the valid 0–100 range — set to null
- 1 record with negative `days_to_churn` — set to null
- 5 records with `health_score` above 100 — set to null

### Data Quality
Missing values were categorised as either **expected nulls** (structurally correct gaps, such as null `days_lead_to_mql` for leads that never became MQLs) or **data quality problems** (fields that should have values but don't, such as 45 customers with no plan tier and 49 deals with no deal value recorded). All nulls were retained rather than filled or deleted, with each decision documented.

### Correlations
- **Health score and churn** — Critical customers churned at 32.4% vs 5% for Healthy customers — strong relationship
- **Product usage score and churn** — low usage scores were associated with higher churn probability
- **Buyer evidence score and rep deal probability** — 0.44 correlation — moderate, not strong
- **Discount % and win rate** — no meaningful correlation — higher discounts did not lead to higher win rates
- **NPS score and churn** — lower NPS customers churned more frequently

  ## Key Insights

### 1. Revenue Growth vs Retention Gap
Revenue growth is strong, but NRR of 81.69% reveals the business is relying on new customer acquisition to offset churn. The business cannot sustain growth from its existing customer base alone. Improving retention is the highest leverage opportunity available.

### 2. ICP Clarity
Mid to large companies across multiple industries and regions represent the strongest customer segments. North America MarTech (11-50 employees, $924K avg deal value) and Europe CyberSecurity (201-500 employees) are the highest priority acquisition targets based on combined conversion rate, win rate, and churn performance.

### 3. Lead Quality Over Channel Volume
Paid Search generated the highest lead volume, but conversion rates across all channels were nearly identical (7–9.3%). Channel allocation is not the problem, lead quality and ICP targeting are. Better targeting could improve conversion performance across the entire funnel without increasing spend.

### 4. Top of the Funnel Bottleneck
Only 47% of leads progressed to MQL. The largest single drop off in the funnel. Sales performance improved significantly after qualification, indicating that fixing lead quality at the top of the funnel would have the greatest downstream impact on pipeline and revenue.

### 5. Expansion Revenue Declining
New business consistently outpaces expansion revenue, and expansion actually declined in 2024 ($2.3M → $2.1M) despite growing new business. The business is not successfully growing its existing customer base even as it acquires new customers. A warning sign for long term revenue sustainability.

### 6. Customer Health Predicts Churn
Customer health score is the strongest early warning signal for churn, Critical accounts churn at 32.4% vs 5% for Healthy accounts. Poor support is the primary churn driver (48 out of 207 churned customers). With win back success at only 17.7%, preventing churn is significantly more valuable than recovering lost customers.

### 7. Pipeline at Risk
Despite a $211.55M weighted pipeline, $13.23M in ARR is at risk from upcoming renewals concentrated in Critical and At Risk accounts. Customer retention is the primary lever for protecting forecast reliability and future revenue.



Visuals Preview
-Dashboard Screenshots-

<img width="614" height="341" alt="B2B CRM REVOPS OVERVIEW" src="https://github.com/user-attachments/assets/89e47107-c1f1-4f60-ad87-dd81cc93e4ef" />
<img width="596" height="339" alt="image" src="https://github.com/user-attachments/assets/3924c4ec-f9e7-4167-a1e3-85c0e8d2f7ed" />

-Before and After Cleaning-
<img width="895" height="304" alt="image" src="https://github.com/user-attachments/assets/97d68ab9-077c-4dba-8d9d-02486f7707c5" />
<img width="891" height="289" alt="image" src="https://github.com/user-attachments/assets/2ce46564-68c2-4d43-b22c-8bf1701baf41" />

-SQL Query-
<img width="770" height="368" alt="image" src="https://github.com/user-attachments/assets/6669d6d8-5412-46e8-8ad7-9f45df60b370" />

-Data Model Diagram- 
<img width="878" height="326" alt="image" src="https://github.com/user-attachments/assets/e5d8537d-c997-45f5-87cf-85248fb9a4a4" />


## Business Recommendations

###  Who Should We Be Targeting?
**Key Findings:**
- CyberSecurity generated the highest lead volume + highest win rate
- HealthTech generated the highest average customer value
- MEA generated the highest revenue but highest churn
- North America has the lowest churn and highest value customers

**Recommendation:**
Focus acquisition efforts on CyberSecurity and HealthTech companies, particularly in North America. For MEA, investigate why large LogisTech companies (1000+ employees) churn at 55.56%. The data suggests they are being sold Starter plans that do not fit their scale. Fixing plan tier alignment for enterprise LogisTech accounts in MEA could significantly reduce churn while protecting the region's $2.3M in LogisTech revenue.

### How Are We Attracting Them?
**Key Findings:**
- Paid Search generated the most leads and revenue
- Conversion rates across channels were very similar (7–9.3%)
- No channel had a major conversion advantage

**Recommendation:**
Continue investing in Paid Search because it drives scale, but shift analysis from channel performance to lead quality. Since conversion rates are nearly identical across sources, improving targeting and ICP alignment is likely to have a greater impact than reallocating channel spend. Newsletter campaigns generate the highest average deal value at $507K and should be prioritized for subscriber growth.


### How Are We Converting Them?
**Key Findings:**
- Largest drop off occurs at Lead to MQL (47%)
- Funnel improves significantly after qualification
- Organic Search has weakest Lead to MQL conversion at 30%
- LATAM MarTech companies (1-10 employees) convert at 20% with $808K average deal value

**Recommendation:**
Improve lead qualification and targeting at the top of the funnel. Sales performance is relatively healthy after leads become MQLs, so the highest return opportunity is increasing lead quality before prospects enter the sales process. Prioritize outreach to LATAM MarTech small companies (the highest converting and highest value segment identified in the analysis).

### How Much Are They Worth?
**Key Findings:**
- Enterprise accounts generate 63% of ARR ($27.7M)
- New business ($21.5M) and existing customer revenue ($21M) are nearly equal
- Expansion revenue declined in 2024 despite growing new business

**Recommendation:**
Prioritize Enterprise acquisition, retention, and expansion programs. The near equal contribution of new and existing customer revenue confirms the importance of protecting and growing the existing base. The decline in expansion revenue in 2024 is a warning sign that account growth programs need immediate attention to prevent further decline.


### Are We Keeping Them?
**Key Findings:**
- Poor support is the leading churn reason (48 out of 207 churned customers)
- Starter customers churn the most at 33.6%
- Critical accounts churn at 32.4% vs 5% for Healthy accounts
- Win-back success rate is only 17.7%

**Recommendation:**
Invest in proactive customer success and support. Build health score monitoring and intervention workflows that trigger action when customers become At Risk rather than waiting until they reach Critical status. Fix support quality first, it is both the top churn reason and likely the reason win back attempts fail, since customers who left due to poor support return to the same environment.


###  How Healthy Is The Business Overall?
**Key Findings:**
- NRR = 81.69% — below the 100% threshold
- Business depends on new customer acquisition to sustain revenue
- Pipeline confidence only moderately aligns with buyer signals (0.44 correlation)

**Recommendation:**
Make revenue retention the top strategic priority. Focus on reducing churn, improving customer support, and increasing expansion revenue. Pipeline forecasting accuracy should be improved.

---

### Executive Summary Recommendation
Acquire more of the right customers (CyberSecurity and HealthTech), improve lead quality at the top of the funnel, prioritize Enterprise accounts, reduce support driven churn, and increase retention so growth becomes less dependent on constantly acquiring new customers.


## Limitations

While this analysis provides meaningful insights across the full B2B SaaS customer lifecycle, the following limitations should be acknowledged when interpreting findings:

**Data Completeness**
- 45 customer records have no plan tier recorded, leaving $1.4M in ARR unattributed to any plan segment
- 49 deal records have no deal value recorded, understating total pipeline and revenue figures
- 438 company records are missing firmographic data (company size and annual revenue), excluding them from size-based segmentation analysis
- 400 leads have no assigned rep, affecting rep performance completeness

**Data Model Constraints**
- Deals and customers are independent branches off the leads table and do not connect directly. Cross analysis between deal attributes (contract type, competitor) and customer outcomes (churn, ARR) required workarounds such as adding `deal_type` directly to the customers table in PostgreSQL.
- ARR growth is calculated by contract start year rather than as a cumulative ARR snapshot, which understates true business growth since contracts signed in earlier years contribute revenue across multiple years

**Health Score Reliability**
- Health scores may be understated due to data quality issues in the underlying `product_usage_score` and `nps_score` columns, where invalid values were set to null during cleaning. This affects the reliability of the $42.8M ARR at risk figure and health-based segmentation

**Synthetic Data**
- The dataset is synthetic and may not capture all real world CRM complexities such as multi contact deals, partial renewals, or mid contract upgrades

**Partial Year Data**
- 2025 figures represent January to October only and are not directly comparable to full prior years (2022–2024). Year over year comparisons involving 2025 should be treated with caution.

