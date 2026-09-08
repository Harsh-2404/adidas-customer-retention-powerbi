# 📊 Retail Customer Retention Analytics – ADIDAS

[![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![DAX](https://img.shields.io/badge/DAX-Data_Analysis_Expressions-blue?style=for-the-badge)](https://learn.microsoft.com/en-us/dax/)
[![Domain](https://img.shields.io/badge/Domain-Retail_&_E--commerce_Analytics-brightgreen?style=for-the-badge)]()

---

## 📌 Executive Summary
Adidas operates a global retail network encompassing owned physical stores, franchise outlets, and online e-commerce platforms. In an increasingly competitive landscape against brands like Nike and Puma, customer acquisition costs continue to rise, making long-term customer retention and Customer Lifetime Value (CLV) optimization critical.

This project delivers an end-to-end **Interactive Power BI Analytics Dashboard** across 7 key analytical tasks. It consolidates fragmented transactional, demographic, loyalty, and store footprint data to diagnose churn drivers, measure loyalty tier performance, evaluate promotional discounting efficiency, and provide executive decision-makers with actionable retention strategies.

---

## 🖼️ Dashboard Architecture & Visual Views

### Task 2: Churn & Retention Metrics
> Geographic, income, channel, and loyalty tier breakdown of customer drop-off alongside the conversion funnel.
![Task 2 - Churn & Retention](screenshots/task2_churn_metrics.png)

### Task 3: Repeat Purchase Analysis
> Purchase frequency comparison across regions, age groups, loyalty tiers, and top-performing product categories.
![Task 3 - Repeat Purchase](screenshots/task3_repeat_purchase.png)

### Task 4: Promotion & Loyalty Impact
> Analysis of Creators Club points issuance vs. redemptions, promotional purchase lifts, and tier churn dynamics.
![Task 4 - Promotion & Loyalty](screenshots/task4_loyalty_impact.png)

### Task 5: Store & Channel Performance vs Retention
> Average transaction values, churn rates across store formats (Flagship, Outlet, Franchise, Online), and vintage retention correlations.
![Task 5 - Store Performance](screenshots/task5_store_performance.png)

### Task 6: Customer Lifetime Value (CLV) Analysis
> CLV metrics mapped against recency (Days Since Last Purchase) and cross-analyzed by regional loyalty tiers.
![Task 6 - CLV Analysis](screenshots/task6_clv_analysis.png)

### Task 7: Executive Summary & Customer Prioritization
> Multi-page executive overview summarizing core KPIs, monthly spending trends, channel share, and CLV segment distributions.
![Task 7 - Executive Summary](screenshots/task7_executive_summary.png)

---

## 🛠️ Task 1: Data Modeling, Cleaning & Pipeline Architecture

The analytical pipeline ingests 5 primary source CSVs and builds a robust Data Model:

### 1. Data Cleaning & Transformation (Power Query)
- **Data Hygiene:** Standardized data types across text, numerical, and date fields. Handled null values and purged duplicate transaction logs.
- **Relational Data Modeling:** Established active 1-to-Many ($1:*\text{M}$) relationships between central Dimension Tables (`Customer Demographics`, `Store Locations`, `Loyalty Program`, `Churn Labelled Customers`) and the Fact Table (`Customer Transactions`).

### 2. Custom Calculated Columns Engine
- **Membership Duration:** Calculated total tenure in years:
  `Membership_Duration = Duration.Days(DateTime.LocalNow() - [Membership_Since]) / 365`
- **Temporal Extraction:** Engineered time-intelligence attributes from `Transaction_Date`:
  - `Transaction_Year` = `Date.Year([Transaction_Date])`
  - `Transaction_Month` = `Date.MonthName([Transaction_Date])`

---

## 📊 Dataset Description

1. **`Customer_Demographics.csv`** – `Customer_ID`, `Age`, `Gender`, `Region`, `Income_Level`, `Membership_Since`, `Preferred_Channel`
2. **`Customer_Transactions.csv`** – `Transaction_ID`, `Customer_ID`, `Store_ID`, `Product_Category`, `Transaction_Date`, `Amount`, `Promotion_Applied`
3. **`Store_Locations.csv`** – `Store_ID`, `Store_Type`, `Region`, `Opening_Year`
4. **`Loyalty_Program.csv`** – `Customer_ID`, `Loyalty_Tier`, `Points_Earned`, `Points_Redeemed`
5. **`Churn_Labelled_Customers.csv`** – `Customer_ID`, `Last_Purchase_Date`, `Churn_Flag`, `Churn_Reason`

---

## 📐 Key DAX Measures & Analytics Logic

```dax
// 1. Total Customers Measure
Total Customers = 
DISTINCTCOUNT('Customer demographics adidas - Customer_Demographics'[Customer_ID])

// 2. Churned Customers Measure
Churned Customers = 
CALCULATE(
    DISTINCTCOUNT('customer churned - Churn_Labelled_Customers'[Customer_ID]),
    'customer churned - Churn_Labelled_Customers'[Churn_Flag] = 1
)

// 3. Repeat Customers Measure
Repeat Customers = 
[Total Customers] - [Churned Customers]

// 4. Overall Churn Rate (%) Measure
Churn Rate = 
DIVIDE([Churned Customers], [Total Customers])

// 5. Customer Lifetime Value (CLV) Column / Measure
CLV = 
IF(
    'Customer demographics adidas - Customer_Demographics'[Membership_Duration] <= 0, 
    [Total_Spent], 
    DIVIDE(
        [Total_Spent], 
        'Customer demographics adidas - Customer_Demographics'[Membership_Duration]
    )
)
```
---

## 📈 Detailed Insights Across Tasks (Tasks 2 – 7)

* **Task 2 - Churn & Retention Metrics:**
  * **Geographic Risk:** Regional churn rate is highest in **Asia-Pacific (33.33%)** and **Middle East (28.99%)**[cite: 5].
  * **Loyalty Tier Risk:** **Elite Loyalty Tier** members show an unexpected highest drop-off rate at **44.83% churn**, followed by Premium (**32.26%**)[cite: 5].
  * **Income Breakdown:** **High Income** customers show the highest churn rate at **34.83%**, compared to Low Income (**21.13%**)[cite: 5].
  * **Funnel Analysis:** Total Customers (**300**) → Repeat Customers (**153 / 51.0%**) → Churned Customers (**147 / 49.0%**)[cite: 5].

* **Task 3 - Repeat Purchase Analysis:**
  * **Age Cohort Activity:** Younger demographic segments aged **18–24** and **35–44** demonstrate the highest average purchase frequency[cite: 5].
  * **Product Category Share:** Repeat customer purchases are heavily concentrated in **Footwear (42.11%)**, followed by **Accessories (31.58%)** and **Apparel (26.32%)**[cite: 5].

* **Task 4 - Promotion & Loyalty Impact:**
  * **Promotional Spending Lift:** Purchases with promotions applied yielded a higher Average Transaction Amount (**$272.43**) vs. non-promotional transactions (**$258.79**)[cite: 5].
  * **Promotion Penetration:** **52.70%** of total transactions utilized promotional discounts ($244.54K promo spent vs $230.33K non-promo)[cite: 5].
  * **Points Redemption Gap:** Base tier users earned ~1.00M points and redeemed 0.59M, while Elite members earned 0.26M points and redeemed only 0.17M[cite: 5].

* **Task 5 - Store & Channel Performance:**
  * **Average Transaction Value (ATV):** **Franchise stores** lead ATV at **$271**, followed by Outlets (**$269**), Online (**$260**), and Flagship stores (**$258**)[cite: 5].
  * **Channel Volume Split:** Online platforms generated **514 transactions ($134K revenue)** while Physical Stores generated **486 transactions ($131K revenue)**[cite: 5].
  * **Channel Churn:** Flagship store shoppers display higher churn (**31.30%**) compared to Online (**26.83%**) and Franchise (**26.89%**)[cite: 5].

* **Task 6 & 7 - CLV & Segmentation Analysis:**
  * **CLV Distribution:** **Low CLV (78.33% / 235 users)**, **Medium CLV (17.00% / 51 users)**, and **High CLV (4.67% / 14 users)**[cite: 5].
  * **Top Churn Reasons:** Out of churned customers, **Low Engagement** and **Competitor Switching** represent the top behavioral drivers[cite: 5].

---

## 💡 Strategic Recommendations for Adidas

1. **Restructure Elite Loyalty Tier Perks:**
   * Address the high **44.83% churn rate** in the Elite tier by introducing non-monetary experiential rewards like VIP event access, personalized concierge support, and early access to hype drops[cite: 5].
2. **Shift to Precision & Targeted Discounting:**
   * Promotions account for **52.70%** of transactions[cite: 5]. Move away from sitewide blanket discounts toward personalized, CLV-targeted promotional codes to protect gross margins while maintaining volume.
3. **Deploy Regional Win-Back Campaigns:**
   * Focus retention efforts and localized marketing campaigns on high-activity, high-churn hubs like **Asia-Pacific** and **Middle East**, while driving product visibility in underperforming lines like **Apparel**[cite: 5].

---

## 📁 Repository Structure

```text
├── datasets/                     # Source CSV files
│   ├── Customer_Demographics.csv
│   ├── Customer_Transactions.csv
│   ├── Store_Locations.csv
│   ├── Loyalty_Program.csv
│   └── Churn_Labelled_Customers.csv
├── screenshots/                  # High-resolution dashboard visual exports
├── Adidas_Customer_Analytics.pbix# Master Power BI Report File
├── Project_Report.pdf            # Full Technical & Executive Project Report
└── README.md                     # Portfolio Project Documentation
```
---

## 👤 Author & Contact
Harsh Srivastav

Role: Data Analyst / BI Developer

Domain Focus: Retail Analytics & Business Intelligence
