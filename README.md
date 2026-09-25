Fintech Fraud & Anomaly Detection Pipeline — Uzbekistan Digital Payments

An end-to-end, cloud-native analytics pipeline that shifts fraud detection from static, rule-based thresholds to dynamic, behavioral anomaly detection using parametric statistics built on a simulated dataset of the Uzbekistan digital payment ecosystem (2023–2024).

Processes 150,000+ transactions totaling 41 billion UZS, detecting fraud through personalized, per-user statistical baselines rather than one-size-fits-all rules.

Overview

Traditional fraud systems flag transactions using fixed rules (e.g., "flag anything over 5,000,000 UZS") — which miss fraud that's small in absolute terms but abnormal for a specific user, and over-flag high-spending users who are behaving normally.

This project instead computes a personalized behavioral baseline for every user (their historical mean and standard deviation of spend) and scores each new transaction against that baseline using a Z-score, enabling detection that adapts to individual behavior rather than a fixed cutoff.

Architecture
1. Raw CSV Data (Volumes)
       
2. Delta Lake Tables (raw_transactions, raw_users, raw_merchants)
       
3. SQL Feature Engineering Pipeline (Z-Scores & Risk Tiers)
       
4. Enriched Delta Table (enriched_transactions)
        
5. Databricks AI/BI Dashboard (Executive KPIs & Risk Insight)

Tech Stack
Layer	Tools
1. Storage / Lakehouse	 Databricks Unity Catalog Volumes, Delta Lake
2. Transformation	 Advanced SQL — Window Functions, CTEs
3. Statistical Modeling  Parametric Anomaly Detection (Z-Scores)
4. Visualization	        Databricks AI/BI Dashboards (multi-tab executive layout)

Statistical Methodology

Each transaction is scored against the specific user's own spending history, not a global threshold:

Z = (x − μ) / σ
1. μ (mean) — the user's historical average transaction amount
2. σ (standard deviation) — that user's spending volatility
3. x — the current transaction amount

Example: a user who typically spends ~50,000 UZS with low variance suddenly makes a 300,000 UZS transaction → this yields a high Z-score (>3.0) and is flagged, even though 300,000 UZS might look unremarkable at a platform-wide scale.

Risk Tiers
Tier	             Z-Score Range	              Action
1. 🔴 High Risk	    Z > 3.0	       Statistical outlier (99.7% confidence of deviance) — triggers automated account freeze
2. 🟡 Medium Risk    2.0 < Z ≤ 3.0	       Moderate deviation — passive monitoring or step-up verification
3. 🟢 Low Risk	    Z ≤ 2.0	       Normal behavior

Executive Dashboard

Built as a 3-page Databricks AI/BI Dashboard, backed by 4 semantic datasets (Transaction Behavioral Analytics, Enriched Transactions (Fraud & Risk), Merchant Risk Profiles, User Demographics & Credit) with dedicated dimensions and measures for each.

1. Fraud & Risk Overview

1. KPI cards: Total Transactions, Fraud Detected, Fraud Rate, Total Volume (UZS)
2. Bar chart: Fraud by Channel, Fraud by Region, Fraud by Merchant Category, Avg Z-Score by Channel, Avg Amount by Risk Tier
3. Pie chart: Risk Tier Distribution
4. Line chart: Transaction Volume Over Time 


2. Merchant Risk Profiling

1. KPI cards: Total Merchants, Avg Risk Score, Avg Transaction (UZS)
2. Bar Chart: Merchant Count by Category, Avg Risk Score by Region, Avg Risk Score by Category
3. Pie Chart: Online vs Offline
4. Table: Top Merchants by Risk Score 

3. User & Behavioral Analytics

1. KPI cards: Total Users, Avg Credit Score, Identity Verified Rate, Avg Monthly Income (UZS)
2. Bar Chart: Users by Region, Users by Age Group, Fraud by Hour of Day, Fraud by Day of Week, Fraud by Login Attempts, Fraud: Weekend vs Weekday
3. Pie Chart: Identity Status Distribution 

This page also tracks cross-city transaction flags and session duration, enabling deeper behavioral fraud patterns beyond just transaction amount — e.g. correlating fraud with unusual login attempt counts, off-hours activity, or cross-city spending spikes.

Key Findings: 
Metric	Value
1. Total Transactions	             150,000+
2. Total Volume Monitored	             41 billion UZS
3. Fraudulent Transactions Detected      5,210
4. Overall Fraud Rate	             3.47%
   
Behavioral risk tiering (via Z-scores) catches fraud invisible to flat-amount rules, since it flags deviation from a user's own baseline rather than an absolute value.

Channel-level analysis revealed that certain transaction channels carry disproportionately higher fraud rates, allowing security teams to prioritize authentication resources on the most vulnerable endpoints.

Merchant-level profiling surfaces merchant categories with abnormal fraud/chargeback ratios for targeted compliance review.

Business Value
1. Proactive risk control — high-confidence outliers (Z > 3.0) can trigger automated freezes or step-up MFA in real time, rather than relying on manual review after the fact.
2. Resource-efficient security — channel and merchant risk profiling lets teams focus authentication and compliance effort where fraud actually concentrates, instead of applying blanket friction everywhere.
3. Personalized, adaptive detection — because the baseline is per-user, the system scales fraud sensitivity to individual behavior instead of a fixed platform-wide rule.

Tech Highlights:
1.Delta Lake + Unity Catalog Volumes for governed, versioned lakehouse storage
2.Advanced SQL: CTEs and window-style aggregation for per-user statistical modeling
3.Parametric statistics (Z-score anomaly detection) instead of static rule-based thresholds
4.Multi-tab executive BI dashboarding for stakeholder-ready insights

Conclusion

This project demonstrates a shift away from static, rule-based fraud detection toward a statistically grounded, per-user behavioral model one that adapts to each customer's own spending pattern rather than applying the same fixed threshold to everyone. By combining a governed lakehouse architecture (Delta Lake + Unity Catalog), SQL-based feature engineering, and Z-score anomaly detection, the pipeline turns 150,000+ raw transactions into a decision-ready risk signal that can drive real-time actions — account freezes, step-up authentication, or manual review — rather than static after-the-fact reporting.

Beyond fraud scoring itself, the three-page executive dashboard extends the analysis into merchant risk profiling and user behavioral patterns (time of day, day of week, login attempts, cross-city activity), giving risk and compliance teams multiple angles to investigate why fraud is occurring, not just how much. This makes the system useful not only as a detection tool, but as a foundation for ongoing investigation and policy tuning as fraud patterns evolve.

Dashboard link: https://dbc-a6b481b0-6de2.cloud.databricks.com/dashboardsv3/01f1b8a96b5f1cb79de89d2f36bf0383/published?o=7474647451048864

<img width="819" height="548" alt="Image" src="https://github.com/user-attachments/assets/f94c5b2c-7bdb-441b-8d42-efb6bf2a1367" />

<img width="838" height="588" alt="Image" src="https://github.com/user-attachments/assets/df9a8796-cdcd-4260-bb7c-c99301104eb3" />

