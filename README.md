# NovaTech Revenue Intelligence Dashboard

**Creator:** Moamen Mohasseb

**Role:** Lead Business Intelligence Analyst

**Date:** September 2026

**Platform:** Amazon QuickSight, AWS, Python, SQL

---

## Page 1: Project Overview, Architecture & Data Modeling

### 1. Project Background & Objective

NovaTech Solutions previously maintained business data across three isolated repositories: Sales CRM, Marketing Campaigns, and Customer Support. Executive leadership required an end-to-end Revenue Intelligence System to eliminate manual slide reporting, unify full-funnel customer journeys, track product reliability against contract value, and support conversational ad-hoc querying via Amazon Q.

### 2. Source Datasets & Schema Summary

* **Sales Pipeline (`novatech_crm_deals.csv`):** 499 closed opportunities, 20 attributes. Key fields include `account_id`, `opportunity_id`, `sales_rep`, `sales_region`, `product_name`, `deal_stage`, `deal_value`, and `loss_reason`.


* **Marketing Campaigns (`novatech_marketing_campaigns.csv`):** 2,240 campaign interactions, 20 attributes. Key fields include `account_id`, `lead_id`, `campaign_channel`, `funnel_stage`, `campaign_spend`, `revenue_attributed`, and `Mkt_Src_Cd`.


* **Support Tickets (`novatech_support_tickets.csv`):** 3,000 incident tickets, 20 attributes. Key fields include `account_id`, `ticket_id`, `priority`, `product_area`, `customer_tier`, `downtime_minutes`, and `customer_sentiment`.



### 3. Data Strategy & Engineering

* **Star Schema vs. Flat Joins:** Direct full joins across `account_id` create a Cartesian fan-out of 65,448 rows, falsely multiplying every lead, opportunity, and support ticket. The data model implements a central `Dim_Account` table connected via 1:Many relationships to individual fact tables to preserve metric integrity.


* **Orphan Key Integration:** The CRM contains 85 accounts (`ACCT-001` through `ACCT-085`), while Marketing and Support encompass 100 accounts (`ACCT-001` through `ACCT-115`). A unified outer-dimension strategy preserves the 15 orphan accounts representing 150 marketing leads and 204 support tickets without join drops.


* **Surrogate Key Generation:** Glitches in upstream primary key generation created 3 duplicate `opportunity_id` values in CRM (6 rows) and 4 duplicate `ticket_id` values in Support (8 rows). Synthetic composite keys (`account_id_opportunity_id` and `account_id_ticket_id`) enforce primary key uniqueness constraints.


* **Cleaning & Imputation:** Missing `annual_income` values in Marketing (24 rows, 1.1%) were median-imputed by `customer_segment`. 59 open support tickets with null resolution dates were assigned an explicit status attribute.



### 4. Amazon QuickSight Semantic Layer & Q Topic

* **Disambiguation:** Configured synonyms for *revenue*, *sales*, and *bookings* pointing strictly to `deal_value`, separating actual sales from client corporate size (`annual_revenue_usd`) and ad attribution (`revenue_attributed`).


* **Pre-Computed Metric Fields:** Built dedicated calculated fields for `Win_Rate`, `Days_to_Close`, and `Resolution_Time_Days` to enable natural language question answering.


* **Geographic Mapping:** Aliased two-letter ISO country codes in `Mkt_Src_Cd` to standard location taxonomies.



---

## Page 2: Dashboard Visuals, Strategic Insights & Technical Validation

### 1. Dashboard View Architecture

* **View 1: Marketing Funnel:** Analyzes multi-stage lead progression (`Prospect` $\rightarrow$ `Lead` $\rightarrow$ `Qualified Lead` $\rightarrow$ `Opportunity` $\rightarrow$ `Closed Won`), campaign spend vs. revenue, and acquisition channel ROI.


* **View 2: Sales Pipeline:** Tracks regional deal distribution, average deal value across company size tiers, overall win rate (64%), and categorical loss reasons.


* **View 3: Customer Health:** Monitors product issue frequency, SLA resolution durations across priorities, sentiment distributions, and an At-Risk Accounts matrix correlating ticket volume with annual recurring revenue.


* **Cross-Sheet Interactivity:** Configured dynamic navigation actions where selecting an account in Sales Pipeline transitions directly to Customer Health filtered to that client's open support tickets.



### 2. Quantified Strategic Insights & Recommended Actions

* **Acquisition Engine Concentration:** Partner Referral generates 63.0% of all Closed Won leads (237 of ~376) and 493 Qualified Leads, while Organic Search delivers 558 top-of-funnel leads that yield near-zero closed sales.


* *Action:* Shift top-of-funnel inbound advertising funds into co-marketing programs and tiered commission incentives for referral partners.




* **Marketing Budget Realignment:** Total campaign expenditure reached $353.51M against $25.05M in directly attributed revenue (-$328.46M net loss). NovaEdge Awareness alone incurred a -$55.24M net deficit ($55.45M spend vs. $0.21M revenue).


* *Action:* Freeze NovaEdge Awareness spending immediately and require multi-touch lifecycle attribution on active campaigns.




* **Enterprise Monetization Compression:** Small company tiers ($1.67K) and Enterprise tiers ($1.66K) exhibit nearly identical average deal values, with Large accounts lagging at $1.33K.


* *Action:* Enforce minimum user seat commitments on enterprise proposals and bundle NovaPulse Ultimate into enterprise sales motions.




* **Regional Pipeline Disparity:** The West ($42.67M) and Central ($38.70M) sales territories represent 80.6% of pipeline value, while the East territory ($19.53M) underperforms.


* *Action:* Conduct a sales management review in the East territory and reassign proven account executives from Central to drive pipeline execution.




* **Enterprise Support Exposure:** Notifications generates the largest ticket volume (19.98K), followed by Authentication (11.31K). Crucially, Enterprise and Plus tiers absorb 96.7% of all high-priority tickets (356 of 368).


* *Action:* Deploy a dedicated tier-1 engineering escalation pod for enterprise accounts and refactor notification delivery retries.





### 3. Automated AI vs. Dashboard Data Validation

* **Entity Uniqueness:** Automated AI reviews of the denormalized join incorrectly stated that zero uniquely-occurring leads existed due to Cartesian multiplication. The dashboard's `countDistinct(lead_id)` confirms that all 2,240 leads in the source table are completely unique.


* **Resolution Duration Sign:** Automated text summaries reported an average resolution time of -1.97 days due to inverted operand order on the visual axis. The actual operational resolution duration is positive 1.97 days (~47 hours).


* **Attribution vs. Pipeline Value:** Automated narratives flagged all marketing efforts as complete failures based solely on immediate single-touch revenue ($25.05M). Cross-table relational analysis confirms that partner leads feed enterprise accounts generating the $100.9M in closed CRM pipeline.
