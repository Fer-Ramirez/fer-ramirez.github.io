---
layout: default
theme: jekyll-theme-cayman
title: María Fernanda Ramírez, PhD | BI Portfolio
---
**Contact:** <a href="https://www.linkedin.com/in/mariafernandaramirez-analytics/">LinkedIn</a> | <a href="https://github.com/Fer-Ramirez">GitHub</a> | fer.ramirezm4@gmail.com

## About Me

> **BI & Operations Analyst** with a PhD in Biomedical Sciences and experience in the pharmaceutical industry, including client-facing and cross-functional roles.
> 
> My research background trained me to approach complex problems with **structured thinking** and to translate data into **clear, evidence-based narratives**.
> 
> In industry, I’ve worked closely with stakeholders across functions, developing the ability to communicate insights, align teams, and connect data analysis with business decisions. I apply this foundation with **SQL, Python, and Predictive Analytics** to drive revenue-focused and operational insights.

---

## Driving Revenue & Retention Through Data

**Quantified $132M in revenue risk and uncovered operational drivers of customer churn using predictive analytics.**

---

## Featured Case Studies

**Key Project**

### *From Revenue to Retention: A Full-Funnel Performance Audit of a Subscription Commerce Platform*
 
**A Latin American subscription commerce platform generating $9.66M in revenue at a 30.5% profit margin showed a critical structural vulnerability: 13.3% of ready-to-buy users — users who had already added items, selected them, and initiated checkout — abandoned at the payment step alone. A five-layer analysis covering profitability, funnel behavior, cohort retention, and a controlled A/B experiment consistently pointed to the same friction point. A redesigned checkout UI showed a +3.80% relative lift in conversion, translating to an estimated ~$92,800 in incremental revenue per 10,000 users — a business decision, not just a statistical one.**
 
---
 
#### Business Question
 
Is RappiPlus's revenue model structurally resilient — or does it depend on a narrow set of conditions that a single friction point can disrupt? Where in the user journey are the highest-confidence revenue levers, and what data infrastructure is needed to validate them?
 
---
 
#### Data
 
- **Source:** TripleTen bootcamp datasets structured as a real-world case study; behavioral data via PostgreSQL on AWS RDS
- **Scope:** Transactional, behavioral, and experimental data — January–May 2025
- **Datasets:**
  - Orders — pricing, discounts, and revenue
  - Product Catalog — unit costs and categories
  - Marketing Spend — by channel and country
  - Platform Events — 7,796 unique users across 6 funnel stages
  - User Activity — 5 monthly cohorts, 4-week tracking window
  - A/B Experiment — 9,906 users: 4,965 control / 5,035 treatment
---
 
#### Process
 
<details>
<summary><strong>1. Data Quality Audit</strong> (Python + Pandas) — click to expand</summary>
<br>
Validated 3 datasets across structure, completeness, consistency, and numeric integrity. Removed 100 duplicate rows; handled nulls by variable type — categorical fields filled with `'unknown'`; monetary fields dropped to avoid fabricating financial data. Standardized categorical text with `.strip().str.title()` to resolve silent aggregation errors (`pais` collapsed from 7 to 4 unique values). Flagged 4 returns as `es_devolucion = True` and excluded from downstream calculations. Removed 10 quantity outliers (IQR upper limit: 3.5 units) — values of 10,000–20,000 units have no valid e-commerce interpretation; winsorizing rejected because it would require recalculating `monto_total`. Documented a key data quality limitation: `precio_unitario` showed distributions inconsistent with catalog costs — profit figures are directional, not audited.
 
</details>
<details>
<summary><strong>2. Profitability & Sales Analysis</strong> (Python + Pandas) — click to expand</summary>
<br>
Calculated global KPIs — total revenue, COGS (via orders × catalog join), marketing spend, gross profit, profit margin, and ROAS. Broke down performance by country, acquisition channel, and product category. Computed channel-level ROAS by merging revenue with marketing spend per channel. Analyzed sales behavior: average order ticket, average units per order, top 3 products by units and revenue, discount penetration rate and revenue impact. All calculations run on `orders_clean` — returns excluded, outliers removed.
 
</details>
<details>
<summary><strong>3. Conversion Funnel</strong> (SQL — PostgreSQL on AWS RDS) — click to expand</summary>
<br>
Connected to AWS RDS PostgreSQL database; queried `events` table directly. Built a 6-stage funnel using `COUNT(DISTINCT CASE WHEN...)` in a single SQL pass: `first_visit → add_to_cart → select_item → begin_checkout → add_payment_info → purchase`. Computed step-over-step conversion rates, drop-off rates, and overall conversion vs. top-of-funnel. Segmented funnel by acquisition channel (Social, Paid Search, Organic) to test whether identical ROAS implies identical conversion behavior inside the platform.
 
</details>
<details>
<summary><strong>4. Cohort Retention Analysis</strong> (SQL) — click to expand</summary>
<br>
Queried `users` and `user_activity` tables; assigned users to monthly cohorts based on `fecha_registro`. Applied `CAST(fecha_registro AS DATE)` for datetime consistency. Tracked weekly retention (W1–W4) per cohort: percentage of each cohort active in each post-registration week. Retention calculated as active users per week / cohort size at registration. Measured engagement stability across 5 cohorts over a 4-week window.
 
</details>
<details>
<summary><strong>5. A/B Statistical Testing</strong> (Python + scipy) — click to expand</summary>
<br>
Loaded `experiment_checkout_ui.csv` (4,965 control / 5,035 treatment). Applied a two-proportion Z-test (`scipy.stats`) to compare checkout conversion rates between groups. Computed relative lift, absolute difference, Z-statistic, p-value, and translated the result into a business revenue impact estimate. Result interpreted at both statistical (α = 0.05) and business decision levels — distinguishing between statistical power and business relevance.
 
</details>
<details>
<summary><strong>6. BI Dashboard</strong> (Tableau Public) — click to expand</summary>
<br>
Consolidated all findings into a 3-page interactive dashboard structured as an executive narrative: Overview → Products & Revenue → User Behavior. Data exported from Python as flat CSVs to enable joins-free visualization in Tableau. Dashboard designed for stakeholder communication — not as a metrics dump, but as a sequential story from revenue fundamentals to the specific friction point driving the analysis.
 
</details>

**Tools:** Python (Pandas, Seaborn, Scipy), SQL (PostgreSQL, pandasql), Tableau Public, Jupyter Notebook
 
---
 
#### Key Insights
 
**1. The revenue model is profitable — but volume-dependent, with no pricing buffer.**
 
RappiPlus delivers a 30.5% margin on $9.66M, but the average order contains only 1.5 units and price variation across products is negligible. Revenue growth depends entirely on transaction frequency — not basket size or price premium. Any disruption to purchase volume hits the top line with nothing to absorb it. That dependency makes it critical to understand where in the user journey that volume is being lost — and how much of it is preventable.
 
**2. A single, bounded friction point intercepts 13.3% of users who have already decided to buy.**
 
The platform converts 80% of visitors — a high-intent base. The first three funnel stages (first visit, add to cart, select item) lose under 3% combined. But at Payment Info, 13.3% of users who had already initiated checkout abandoned. Critically, 99.8% of users who *do* enter payment details complete the purchase. This is not a closing problem — it's a friction problem at one identifiable moment, confirmed independently across the funnel analysis, the cohort behavior, and the A/B test. Three independent analytical layers pointing to the same step is not a coincidence; it's a diagnosis.
 
**3. The A/B test is statistically inconclusive, but the business case isn't.**
 
The redesigned checkout UI produced a +3.80% relative lift (16.29% treatment vs. 15.69% control), with p = 0.4161 — below the power threshold to confirm the effect statistically. But the redesign is already deployed and the implementation cost already incurred. The ~$92,800 estimated incremental revenue per 10,000 users is a concrete number to weigh against that cost. The decision is whether to maintain the change while a better-powered experiment is designed — not whether to roll back. Statistical inconclusive ≠ business inconclusive.
 
---
 
#### Recommendations — Action Plan
 
**Pillar 1 — Product / UX: Fix the payment barrier first.**
Maintain the redesigned checkout UI; define a 30-day window to evaluate cost vs. the ~$92,800 estimated lift. Design a larger, adequately powered experiment to detect the ~0.60pp absolute difference at α = 0.05. *Responsible: Product + Engineering.*
 
**Pillar 2 — Analytics Infrastructure: Instrument purchase-level cohort tracking.**
The 41–43% weekly retention rate measures engagement, not revenue-generating return visits. The `activo` flag does not confirm purchases. Without this instrumentation, the 30.5% margin cannot be evaluated for structural resilience — this is the most critical analytical gap in the current model. *Responsible: Data Engineering + Analytics.*
 
**Pillar 3 — Marketing: Run an incrementality test before reallocating budget.**
ROAS uniformity (3.49x–3.73x) across all three channels confirms that revenue and spend move together — not that paid spend is generating incremental demand. A holdout experiment would determine whether organic volume holds without paid support. Without it, any budget reallocation is a bet, not a decision. *Responsible: Growth + Analytics.*
 
**Pillar 4 — Commercial: Evaluate basket size as the next revenue lever.**
With 1.5 units per order and no price differentiation, the lowest-friction growth path is increasing revenue per transaction. Cross-sell or bundling logic at the product selection stage operates at the moment of highest purchase intent — before the payment step where 13.3% of users are already lost. *Responsible: Product + Commercial.*
 
---

[Explore Full Analysis](https://github.com/Fer-Ramirez/rappiplus-business-performance-analysis) | [View Interactive Dashboard](https://public.tableau.com/views/RappiPlusBusinessPerformanceAnalysis/Historia1)

![RappiPlus Dashboard Overview](https://fer-ramirez.github.io/images/dashboard-rappiplus.png)

*From Revenue to Retention: a 3-page interactive dashboard built for executive stakeholders.*

---

### 📊 Strategic Retention Audit: Revenue Risk & Operational Levers

**A fintech subscription platform with 440K customers faced significant revenue instability driven by customer attrition. Analysis of behavioral, contractual, and financial data revealed that ~48.6% of total revenue ($135M out of $278M) was generated by customers who ultimately churned — with monthly contracts carrying the highest structural risk and support friction emerging as the #1 operational driver of attrition (feature importance: 0.47). Recommendations target three levers: a support Root Cause Analysis, a contract migration incentive program, and a predictive churn alert system integrated into the CRM workflow.**

---

#### Business Question

A fintech subscription platform is losing nearly half its revenue to customer churn. The core question driving this analysis: *What operational and structural factors are causing customers to leave — and how much revenue is actually at risk?*

---

#### Data

- **Source:** Synthetic dataset designed for analytical practice (publicly available via Kaggle)
- **Scope:** 440,832 customer records
- **Variables:** Age, Tenure, Usage Frequency, Support Calls, Payment Delay, Last Interaction, Subscription Type (Basic / Standard / Premium), Contract Length (Monthly / Quarterly / Annual), Total Spend, Churn label

---

<details>
<summary><strong>Process</strong> — click to expand</summary>

<br>

1. **Data Cleaning (Python + Pandas)**
Inspected 12 variables across 440K records. Converted float columns representing discrete counts (Age, Tenure, Support Calls, etc.) to integers for consistency. Identified and removed one row with missing values to preserve data integrity without introducing imputed values. Validated for duplicates at both row and CustomerID level — none found.

2. **Customer Segmentation (SQL via DuckDB)**
Analyzed churn rates by Subscription Type and Contract Length. Flagged and validated an anomalous 100% churn rate for monthly contracts by cross-checking customer counts against churned records — confirmed as a structural characteristic of the dataset, not a calculation error.

3. **Revenue Analysis (SQL + Python)**
Computed total revenue distribution by contract type and subscription tier. Identified that average revenue per customer is nearly identical across tiers (~$628–633), meaning volume, not pricing differentiation, drives top-line performance. Quantified the revenue split between retained and churned customers.

4. **Behavioral Risk Factor Analysis (Python — Scikit-learn)**
Trained a Random Forest Classifier using four behavioral features: Usage Frequency, Support Calls, Payment Delay, and Last Interaction. Ranked predictors by feature importance to identify the operational levers most associated with churn.

5. **Revenue at Risk Quantification (SQL)**
Calculated the dollar value of revenue generated by customers who eventually churned — isolating the financial exposure embedded in the current customer base.

**Tools:** Python (Pandas, Seaborn, Scikit-learn), SQL (DuckDB), Jupyter Notebook

</details>

---

#### Key Insights

1. **Contract structure is the clearest retention signal.** Monthly contracts carry a 100% churn rate in this dataset, while quarterly and annual plans form the stable revenue core. The subscription model itself is a primary risk variable — not customer demographics or tenure.

2. **Support friction is the #1 churn predictor.** Support Calls account for 47% of the Random Forest model's predictive importance, followed by Payment Delay at 31%. Customers aren't leaving because of price or disengagement — they're leaving because of unresolved operational friction. Usage Frequency and Last Interaction rank far lower (10–11%), which challenges the common assumption that "low engagement = churn signal."

3. **$135M in revenue is exposed.** Approximately 48.6% of total platform revenue ($135M out of ~$278M) was generated by customers who churned. Retained customers spend ~$750 on average vs. ~$541 for churned customers — meaning the business is not just losing its customers, it's losing its highest-value ones at a disproportionate rate.

---

#### Recommendations — Action Plan

**Pillar 1 — Support Audit (Immediate)**
Run a Root Cause Analysis on support tickets to categorize the most frequent issue types. The goal is to determine whether churn is driven by product bugs, onboarding failures, or service gaps — and assign accountability to the right team. A 10% reduction in repeat support contacts could meaningfully shift the churn curve.

**Pillar 2 — Contract Migration Strategy (Short-term)**
Design an incentive program to migrate monthly subscribers to quarterly or annual plans — pricing discounts, loyalty benefits, or bundled value. Monthly customers represent the highest-churn, lowest-stability segment; reducing their share of the portfolio directly reduces revenue volatility.

**Pillar 3 — Predictive Early Warning System (Long-term)**
Integrate the trained Random Forest model into the CRM workflow to flag high-risk customers before they churn. Trigger proactive "Customer Success" outreach when a customer crosses defined thresholds on Support Calls or Payment Delay — converting reactive support into preventive retention.

---

**Tech:** Python (Scikit-learn), SQL (DuckDB), Revenue Analytics


[Explore Full Analysis](https://github.com/Fer-Ramirez/customer-churn-operations-analytics) | [Download Executive Summary](https://raw.githubusercontent.com/Fer-Ramirez/customer-churn-operations-analytics/main/Presentation/churn_analysis_slides.pdf)

<img src="images/feature_importance.png" alt="Feature Importance — Customer Churn" width="700"> 
<p style="margin-top: 12 px; font-style: italic;">
Support interactions are the strongest predictor of churn, driving nearly 50% of model importance.
</p>
---

### 💊 [IN DEVELOPMENT] Pharma Supply Chain: Strategic Operational Audit
*Optimizing the 'Clinical-Commercial Nexus' in Global Health Logistics.*

**The Business Case:**
In the high-stakes ARV market, a 10-day delay triggers a "Treatment Interruption" risk. This leads to **permanent market share loss** as patients migrate to competitor second-line therapies, alongside direct losses from **Inventory Obsolescence**.

**Strategic Research Questions:**
1. **Operational:** Which vendors are "Chronic Delayers" exceeding the 10% safety buffer?
2. **Financial:** What is the **Inventory Value-at-Risk** due to shelf-life loss during transit?
3. **Clinical:** What is the projected revenue loss if a delay triggers a 5% patient switch to second-line therapy?

**The Analysis Roadmap:**
* **Advanced SQL Audit:** Ranking carrier reliability and "Cost-of-Delay" logic.
* **Inventory Obsolescence:** Quantifying "Expiry Velocity" and shelf-life degradation.
* **Simulation:** "What-If" engine for 2026 freight inflation and ROI of route optimization.
* **PhD Edge:** Modeling the Clinical-Commercial impact of Treatment Interruption.

*Project Status: Phase 1 (Data Schema & SQL Diagnostic) in progress.*
  
---

## Technical Toolkit

<div style="text-align:center; margin-top:20px;">
  <strong>Data & Modeling</strong>
</div>
<div style="text-align:center; margin-top:8px;">
  <span style="background:#2d6a4f; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">PYTHON</span>
  <span style="background:#2d6a4f; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">PANDAS</span>
  <span style="background:#2d6a4f; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">SCIKIT-LEARN</span>
  <span style="background:#2d6a4f; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">SQL</span>
</div>

<div style="text-align:center; margin-top:20px;">
  <strong>Analytics</strong>
</div>

<div style="text-align:center; margin-top:8px;">
  <span style="background:#40916c; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">REVENUE ANALYSIS</span>
  <span style="background:#40916c; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">CHURN MODELING</span>
  <span style="background:#40916c; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">KPI DESIGN</span>
</div>

<div style="text-align:center; margin-top:20px;">
  <strong>Visualization</strong>
</div>

<div style="text-align:center; margin-top:8px;">
  <span style="background:#52796f; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">POWER BI</span>
  <span style="background:#52796f; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">TABLEAU</span>
  <span style="background:#52796f; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">SEABORN</span>
</div>

<div style="text-align:center; margin-top:20px;">
  <strong>Domain</strong>
</div>

<div style="text-align:center; margin-top:8px;">
  <span style="background:#344e41; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">PHARMA OPERATIONS</span>
  <span style="background:#344e41; color:white; padding:8px 14px; margin:6px; display:inline-block; font-size:14px;">CUSTOMER ANALYTICS</span>
</div>

---

## 📫 Open to Opportunities

<a href="https://www.linkedin.com/in/mariafernandaramirez-analytics/">LinkedIn</a> | <a href="https://github.com/Fer-Ramirez">GitHub</a> | fer.ramirezm4@gmail.com
