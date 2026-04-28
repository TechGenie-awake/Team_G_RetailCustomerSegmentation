# NST DVA Capstone 2 - Project Repository

> **Newton School of Technology | Data Visualization & Analytics**
> A 2-week industry simulation capstone using Python, GitHub, and Tableau to convert raw data into actionable business intelligence.

---

## Before You Start

1. Rename the repository using the format `SectionName_TeamID_ProjectName`.
2. Fill in the project details and team table below.
3. Add the raw dataset to `data/raw/`.
4. Complete the notebooks in order from `01` to `05`.
5. Publish the final dashboard and add the public link in `tableau/dashboard_links.md`.
6. Export the final report and presentation as PDFs into `reports/`.

### Quick Start

If you are working locally:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook
```

If you are working in Google Colab:

- Upload or sync the notebooks from `notebooks/`
- Keep the final `.ipynb` files committed to GitHub
- Export any cleaned datasets into `data/processed/`

---

## Project Overview

| Field | Details |
|---|---|
| **Project Title** | Customer Retention Strategy via RFM Segmentation — Online Retail II |
| **Sector** | Retail / E-Commerce |
| **Team ID** | G |
| **Section** | Team |
| **Faculty Mentor** | _To be filled by team_ |
| **Institute** | Newton School of Technology |
| **Submission Date** | April 28, 2026 |

### Team Members

| Role | Name | GitHub Username |
|---|---|---|
| Project Lead | Gayatri Jaiswal | [`TechGenie-awake`](https://github.com/TechGenie-awake) |
| Data Lead | Gayatri Jaiswal | [`TechGenie-awake`](https://github.com/TechGenie-awake) |
| ETL Lead | Ananya | [`ANANYA542`](https://github.com/ANANYA542) |
| Analysis Lead | Mahek Motwani | [`MAHEK-ops`](https://github.com/MAHEK-ops) |
| Visualization Lead | Sneha Chepurwar | [`sneha31-debug`](https://github.com/sneha31-debug) |
| Strategy Lead | Srishty Shree | [`Srishtyshree`](https://github.com/Srishtyshree) |
| PPT and Quality Lead | Srishty Shree | [`Srishtyshree`](https://github.com/Srishtyshree) |

---

## Business Problem

A UK-based online gift retailer serves both individual shoppers and wholesale buyers across 30+ countries. Like most mid-sized e-commerce businesses, its marketing and retention spend is currently distributed evenly across the customer base — meaning low-value customers receive the same treatment as the small group driving most of the revenue, while high-value customers slip silently toward churn without targeted intervention. This project segments the active customer base using RFM (Recency, Frequency, Monetary) analysis to identify the segments worth protecting and the segments worth re-engaging, so the retention budget can be reallocated where it returns the most.

**Core Business Question**

> Which customer segments drive the most revenue, which are at the highest risk of churn, and where should retention spend be reallocated to recover lost value over the next 12 months?

**Decision Supported**

> Reallocation of retention and marketing campaign budget across customer segments — including which segments to reward, which to win back, and which to deprioritize — to reduce 12-month customer churn by 10–15% and lift repeat-purchase revenue from the top-quartile segment.

---

## Dataset

| Attribute | Details |
|---|---|
| **Source Name** | UCI Machine Learning Repository (donor: Daqing Chen, Sept 2019); accessed via Kaggle mirror `mashlyn/online-retail-ii-uci` |
| **Direct Access Link** | https://archive.ics.uci.edu/dataset/502/online+retail+ii |
| **Row Count** | 1,067,371 |
| **Column Count** | 8 |
| **Time Period Covered** | December 1, 2009 – December 9, 2011 |
| **Format** | CSV (committed as 15 MB compressed ZIP at `data/raw/online_retail_II.zip`) |

**Key Columns Used**

| Column Name | Description | Role in Analysis |
|---|---|---|
| InvoiceNo | Six-digit transaction ID; `C` prefix = cancellation | Returns/cancellation analysis, transaction-level KPIs |
| InvoiceDate | Transaction timestamp | RFM Recency, time-series trends, dashboard filter |
| Quantity | Units purchased per transaction | Revenue computation, returns flagging |
| UnitPrice | Price per unit (GBP) | Revenue computation, RFM Monetary |
| CustomerID | Five-digit customer ID (~25% missing) | RFM segmentation, churn analysis (excluded if null) |
| Country | Customer's country of residence | Geographic dashboard filter, regional KPIs |
| StockCode | Five-digit product ID | Product-level analysis (excluded for non-product codes) |
| Description | Product name (free text) | Tableau filter, product-level reporting |

For full column definitions, see [`docs/data_dictionary.md`](docs/data_dictionary.md).

---

## KPI Framework

| KPI | Definition | Formula / Computation |
|---|---|---|
| Active Customer Count | Customers with ≥1 purchase in trailing 12 months | `nunique(CustomerID where InvoiceDate >= max_date - 365 days)` |
| Average Order Value (AOV) | Average revenue per invoice | `sum(Quantity * UnitPrice) / nunique(InvoiceNo)` |
| Customer Lifetime Value (CLV) | Total spend per customer over the dataset period | `sum(Quantity * UnitPrice) group by CustomerID` |
| 90-Day Churn Rate | Share of customers with no purchase in last 90 days | `count(CustomerID where recency_days > 90) / count(CustomerID)` |
| Revenue Concentration (Top 20%) | Revenue share of the top quintile of customers | `sum(revenue of top 20% of CustomerIDs by CLV) / sum(total revenue)` |
| Repeat Purchase Rate | Share of customers with ≥2 distinct invoices | `count(CustomerID with frequency >= 2) / count(CustomerID)` |
| Country Revenue Mix | Revenue share per country | `sum(revenue) group by Country / sum(total revenue)` |

Document KPI logic clearly in `notebooks/04_statistical_analysis.ipynb` and `notebooks/05_final_load_prep.ipynb`.

---

## Tableau Dashboard

| Item | Details |
|---|---|
| **Dashboard URL** | https://public.tableau.com/shared/SYPQX3FNP?:display_count=n&:origin=viz_share_link |
| **Executive View** | KPI banner (Total Revenue £17.1M, 5,861 customers, 41 countries, 36,639 orders) + segment revenue bar chart + segment size-vs-revenue bubble chart |
| **Operational View** | RFM scatter plot (recency × monetary, sized by frequency, coloured by segment) + segment comparison heat map |
| **Main Filters** | Segment multi-select · country · year selector · revenue threshold slider |

Store dashboard screenshots in [`tableau/screenshots/`](tableau/screenshots/) and document the public links in [`tableau/dashboard_links.md`](tableau/dashboard_links.md).

---

## Key Insights

1. **Champions are 25.2% of customers but generate 69.2% (£11.8M) of revenue** — protecting this segment from churn is the single highest-leverage retention action; even a 5% Champion-loss rate equals £590k in annual revenue at risk.
2. **The At Risk segment is the largest by count (26.1%, 1,530 customers) and holds £1.93M of recoverable revenue** — these customers were once active (avg historical spend £1,261) but haven't purchased in ~400 days; they are the most actionable win-back target.
3. **The bottom 37% of customers (Lost + Loyal Customers + Potential Loyalists) generate only 5.6% of revenue** — treating them with the same retention spend as Champions is economically unjustified.
4. **International customers earn 4–6× more revenue per customer than UK customers** — Netherlands (£2,545 AOV), Australia (£1,885), and Eire (£1,091) outperform the UK's £428 AOV; the brand commands a premium abroad.
5. **The 90-day mark is the inflection point for churn** — Champions average 19 days recency, Loyal Customers 68 days, At Risk jumps to 401 days; customers crossing 90 days rarely recover without intervention.
6. **K-Means clustering validates the rule-based RFM segmentation with >80% overlap** — the six segments represent statistically separable customer groups, giving the marketing team a defensible foundation for differentiated campaigns.
7. **Lost segment customers (788, avg £276) cost more to reactivate than they return** — these should be removed from active campaign budget and pursued only via low-cost organic channels.
8. **Big Spenders (683 customers, avg £3,471) average only 6.6 orders** — they have substantial upsell headroom; lifting them to Champion-level frequency would add ~£1.9M annually if even half convert.
9. **Loyal Customers (10.5% of base) generate only 2.5% of revenue** — they are frequent but low-spend; a clear basket-size and AOV upsell opportunity.
10. **The dataset captures two complete holiday seasons (Q4 2010 and Q4 2011), each materially elevated above baseline** — inventory and marketing pacing should plan for a 30–50% Q4 revenue lift.

---

## Recommendations

| # | Insight | Recommendation | Expected Impact |
|---|---|---|---|
| 1 | Insights 1, 5 | **Launch a Champion Protection Program** — premium loyalty tier, dedicated CSM, alert at 30 days inactive | Defend £11.8M Champion revenue. Cutting Champion churn from 10% to 5% saves ~£590k. **Net +£485k–£530k Year 1.** |
| 2 | Insights 2, 5 | **At Risk win-back campaign** — personalised reactivation email + 15% discount sequence to all 1,530 At Risk customers | At 15% recovery rate: 230 customers × £1,261 avg = **£290k recovered.** Net +£260k–£275k. |
| 3 | Insight 3 | **Reallocate budget away from Lost & low-value segments** — remove Lost (788) and most Potential Loyalists from active retention | Free up ~£20–30k/yr at near-zero opportunity cost. |
| 4 | Insight 4 | **International premium expansion** — targeted ads + currency-localised offers in Netherlands, Australia, Germany, France | 50 new international customers × ~£1,800 AOV = **+£90k.** Net +£50k–£70k. |
| 5 | Insights 8, 9 | **Second-purchase + Big Spender upsell** — 60-day post-purchase nudge; push Big Spender frequency up | Convert 30% of Potential Loyalists (+£92k) + lift Big Spender frequency by 1 order (+£480k). **Net +£530k–£550k.** |

**Total projected Year-1 net impact: +£1.35M–£1.45M** at marketing spend of £90k–£185k (under 1% of revenue). See [`reports/project_report.md`](reports/project_report.md) sections 12–13 for full impact estimation.

---

## Repository Structure

```text
SectionName_TeamID_ProjectName/
|
|-- README.md
|
|-- data/
|   |-- raw/                         # Original dataset (never edited)
|   `-- processed/                   # Cleaned output from ETL pipeline
|
|-- notebooks/
|   |-- 01_extraction.ipynb
|   |-- 02_cleaning.ipynb
|   |-- 03_eda.ipynb
|   |-- 04_statistical_analysis.ipynb
|   `-- 05_final_load_prep.ipynb
|
|-- scripts/
|   `-- etl_pipeline.py
|
|-- tableau/
|   |-- screenshots/
|   `-- dashboard_links.md
|
|-- reports/
|   |-- README.md
|   |-- project_report_template.md
|   `-- presentation_outline.md
|
|-- docs/
|   `-- data_dictionary.md
|
|-- DVA-oriented-Resume/
`-- DVA-focused-Portfolio/
```

---

## Analytical Pipeline

The project follows a structured 7-step workflow:

1. **Define** - Sector selected, problem statement scoped, mentor approval obtained.
2. **Extract** - Raw dataset sourced and committed to `data/raw/`; data dictionary drafted.
3. **Clean and Transform** - Cleaning pipeline built in `notebooks/02_cleaning.ipynb` and optionally `scripts/etl_pipeline.py`.
4. **Analyze** - EDA and statistical analysis performed in notebooks `03` and `04`.
5. **Visualize** - Interactive Tableau dashboard built and published on Tableau Public.
6. **Recommend** - 3-5 data-backed business recommendations delivered.
7. **Report** - Final project report and presentation deck completed and exported to PDF in `reports/`.

---

## Tech Stack

| Tool | Status | Purpose |
|---|---|---|
| Python + Jupyter Notebooks | Mandatory | ETL, cleaning, analysis, and KPI computation |
| Google Colab | Supported | Cloud notebook execution environment |
| Tableau Public | Mandatory | Dashboard design, publishing, and sharing |
| GitHub | Mandatory | Version control, collaboration, contribution audit |
| SQL | Optional | Initial data extraction only, if documented |

**Recommended Python libraries:** `pandas`, `numpy`, `matplotlib`, `seaborn`, `scipy`, `statsmodels`

---

## Evaluation Rubric

| Area | Marks | Focus |
|---|---|---|
| Problem Framing | 10 | Is the business question clear and well-scoped? |
| Data Quality and ETL | 15 | Is the cleaning pipeline thorough and documented? |
| Analysis Depth | 25 | Are statistical methods applied correctly with insight? |
| Dashboard and Visualization | 20 | Is the Tableau dashboard interactive and decision-relevant? |
| Business Recommendations | 20 | Are insights actionable and well-reasoned? |
| Storytelling and Clarity | 10 | Is the presentation professional and coherent? |
| **Total** | **100** | |

> Marks are awarded for analytical thinking and decision relevance, not chart quantity, visual decoration, or code length.

---

## Submission Checklist

**GitHub Repository**

- [x] Public repository created with the correct naming convention (`Team_G_RetailCustomerSegmentation`)
- [x] All notebooks committed in `.ipynb` format
- [x] `data/raw/` contains the original, unedited dataset
- [x] `data/processed/` contains the cleaned pipeline output
- [x] `tableau/screenshots/` contains dashboard screenshots
- [x] `tableau/dashboard_links.md` contains the Tableau Public URL
- [x] `docs/data_dictionary.md` is complete
- [x] `README.md` explains the project, dataset, and team
- [x] All members have visible commits and pull requests

**Tableau Dashboard**

- [x] Published on Tableau Public and accessible via public URL
- [x] At least one interactive filter included
- [x] Dashboard directly addresses the business problem

**Project Report**

- [x] Final report exported as PDF into `reports/`
- [x] Cover page, executive summary, sector context, problem statement
- [x] Data description, cleaning methodology, KPI framework
- [x] EDA with written insights, statistical analysis results
- [x] Dashboard screenshots and explanation
- [x] 8-12 key insights in decision language
- [x] 3-5 actionable recommendations with impact estimates
- [x] Contribution matrix matches GitHub history

**Presentation Deck**

- [x] Final presentation exported as PDF into `reports/`
- [x] Title slide through recommendations, impact, limitations, and next steps

**Individual Assets**

- [ ] DVA-oriented resume updated to include this capstone (per-member task)
- [ ] Portfolio link or project case study added (per-member task)

---

## Contribution Matrix

This table must match evidence in GitHub Insights, PR history, and committed files.

_Initial planned ownership shown below — will be finalized in Phase 2 based on actual GitHub commit + PR history._

| Team Member | Dataset and Sourcing | ETL and Cleaning | EDA and Analysis | Statistical Analysis | Tableau Dashboard | Report Writing | PPT and Viva |
|---|---|---|---|---|---|---|---|
| Gayatri Jaiswal (M1) | Owner | Support | — | — | — | Support | Support |
| Ananya (M2) | Support | Owner | Support | — | — | — | — |
| Mahek Motwani (M3) | — | Support | Owner | Support | — | — | — |
| Sneha Chepurwar (M4) | — | — | Support | Owner | Owner | — | — |
| Srishty Shree (M5) | — | — | — | Support | Support | Owner | Owner |

_Declaration: We confirm that the above contribution details are accurate and verifiable through GitHub Insights, PR history, and submitted artifacts._

**Team Lead Name:** Gayatri Jaiswal

**Date:** April 28, 2026

---

## Academic Integrity

All analysis, code, and recommendations in this repository must be the original work of the team listed above. Free-riding is tracked via GitHub Insights and pull request history. Any mismatch between the contribution matrix and actual commit history may result in individual grade adjustments.

---

*Newton School of Technology - Data Visualization & Analytics | Capstone 2*
