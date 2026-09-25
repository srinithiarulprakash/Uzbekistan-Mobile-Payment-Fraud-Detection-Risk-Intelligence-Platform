Uzbekistan-Mobile-Payment-Fraud-Detection-Risk-Intelligence-Platform

Executive Summary
This project delivers an end-to-end cloud-native fintech analytics and anomaly detection pipeline engineered for the Uzbekistan digital payment ecosystem (simulated dataset 2023–2024). Processing over 150,000 transactions totaling 41 billion UZS, the platform shifts fraud detection away from rigid, static thresholds toward dynamic, behavioral anomaly detection using parametric statistics.


1. Key Business & Analytical Results (What We Concluded)
By analyzing the enriched transaction data through our Databricks AI/BI Dashboard, we derived critical business findings that directly inform risk mitigation and operational strategies:

Overall Fraud Volume & Rate: Out of 150k transactions, the system detected 5,21k fraudulent transactions, translating to an overall platform fraud rate of 3.47%. This establishes an immediate baseline threshold for risk control teams.

Capital Exposure: Total monitored transaction volume reached 41 billion UZS, highlighting the massive scale of digital payments flowing through the ecosystem and underscoring the urgent need for automated fraud triage.

Behavioral Risk Tiering (The Z-Score Impact):

Transactions were segmented into strict risk categories based on standard deviation boundaries.

High-Risk Outliers (Z>3.0): Represent severe anomalies (representing a 99.7% statistical confidence interval of deviance from a user's normal baseline), enabling automated account freezes or secondary MFA triggers.

Medium-Risk Deviations (2.0<Z≤3.0): Flagged for passive monitoring or step-up verification.

Channel Vulnerabilities: Regional and channel breakdowns (visible on the dashboard) proved that certain transaction channels experience disproportionate fraud rates, allowing security teams to reallocate real-time authentication resources specifically toward vulnerable endpoints (such as specific app or web pathways).

2. Architecture & Tech Stack
Storage & Lakehouse: Databricks Unity Catalog Volumes, Delta Lake (raw_transactions, raw_users, raw_merchants)

Transformation & Feature Engineering: Advanced SQL, Window Functions, Common Table Expressions (CTEs)

Statistical Modeling: Parametric Anomaly Detection via Z-Scores

Visualization: Databricks AI/BI Dashboards (Multi-tab executive layouts)

Plaintext
[Raw CSV Data (Volumes)] 
       │
       ▼
[Delta Lake Tables (`raw_*`)] 
       │
       ▼
[SQL Feature Engineering Pipeline (Z-Scores & Risk Tiers)]
       │
       ▼
[Enriched Delta Table (`enriched_transactions`)]
       │
       ▼
[Databricks AI/BI Dashboard (Executive KPIs & Risk Insights)]

3. Statistical Methodology: Parametric Anomaly Detection
Rather than using static business rules (e.g., "flag anything over 5 million UZS"), this engine computes a personalized behavioral baseline for every individual user using parametric statistics:

Z= σ/x−μ
​μ (Customer Mean): The historical average transaction amount for a specific user ID.

σ (Customer Standard Deviation): The spending volatility or variance of that specific user.

The Z-Score Rule:

If a user typically spends 50,000 UZS with a low standard deviation, a sudden 300,000 UZS transaction will yield a high Z-score (>3.0), instantly flagging it as an outlier despite appearing small on a macro scale.

4. Core SQL Feature Engineering Pipeline
The underlying transformation script (fraud_feature_engineering.sql) that powers the entire analytical model:

SQL
CREATE OR REPLACE TABLE enriched_transactions AS
WITH CustomerStats AS (
    SELECT 
        user_id, 
        AVG(amount_uzs) AS Mean_Amount,
        COALESCE(STDDEV(amount_uzs), 0) AS StdDev_Amount
    FROM raw_transactions
    WHERE amount_uzs IS NOT NULL AND user_id IS NOT NULL
    GROUP BY user_id
),
TransactionZScores AS (
    SELECT 
        t.*,
        c.Mean_Amount,
        c.StdDev_Amount,
        CASE 
            WHEN c.StdDev_Amount = 0 THEN 0 
            ELSE (t.amount_uzs - c.Mean_Amount) / c.StdDev_Amount 
        END AS Z_Score
    FROM raw_transactions t
    LEFT JOIN CustomerStats c ON t.user_id = c.user_id
)
SELECT 
    tz.tx_id,
    tz.timestamp,
    tz.amount_uzs,
    tz.channel,
    tz.is_fraud,
    u.region AS User_Region,
    m.category AS MerchantCategory,
    ROUND(tz.Z_Score, 2) AS Amount_Z_Score,
    CASE 
        WHEN tz.Z_Score > 3.0 THEN 'High Risk (Statistical Outlier)'
        WHEN tz.Z_Score > 2.0 THEN 'Medium Risk (Moderate Deviation)'
        ELSE 'Low Risk (Normal Behavior)'
    END AS Risk_Tier
FROM TransactionZScores tz
LEFT JOIN raw_users u ON tz.user_id = u.user_id
LEFT JOIN raw_merchants m ON tz.merchant_id = m.merchant_id;
5. Executive Dashboard Design & Decision-Making Capabilities
The Databricks AI/BI Dashboard is structured into three specialized tabs optimized for stakeholders:

Fraud & Risk Overview:

Features high-level executive KPI cards (Total Transactions: 150k, Fraud Detected: 5.21k, Fraud Rate: 3.47%, Total Volume: UZS 41bn).

Includes donut charts tracking Risk Tier Distribution and bar charts mapping fraud concentrations across Uzbekistan regions.

Merchant Risk Profiling:

Isolates high-risk merchant categories to help partnerships and compliance teams audit merchants with abnormal chargeback or fraud ratios.

User & Behavioral Analytics:

Visualizes channel preferences (App, POS, QR, USSD, Web) and spending behaviors to optimize user experience without compromising security.

Dashboard link: https://dbc-a6b481b0-6de2.cloud.databricks.com/dashboardsv3/01f1b8a96b5f1cb79de89d2f36bf0383/published?o=7474647451048864

