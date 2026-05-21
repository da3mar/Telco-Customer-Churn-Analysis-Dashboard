# 📊 Telco Customer Churn Analysis Dashboard



---

## 📌 Project Overview

This project analyzes customer churn data for a telecom company to identify **why customers leave**, **who is most at risk**, and **how much revenue is being lost**. The goal is to deliver actionable insights that help businesses reduce churn and improve customer retention.

> **Dataset:** IBM Watson Telco Customer Churn Dataset — 7,043 customers, 21 features

---

## 🎯 Business Problem

Telecom companies lose significant revenue every month due to customer churn. This project answers three key questions:

- **Who** is most likely to churn?
- **Why** are they churning?
- **How much** revenue is at risk?

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| **MySQL** | Database creation, data storage, and SQL analysis |
| **MySQL Workbench** | Writing and executing SQL queries |
| **Power BI Desktop** | Interactive dashboard and data visualization |
| **DAX** | Custom measures and calculated columns |
| **SQL** | CTEs, Window Functions, Views, Joins |

---

## 🗄️ Database & SQL

### Database Setup
```sql
CREATE DATABASE telco_churn;
USE telco_churn;
```

### Key SQL Techniques Used

**1. CTE (Common Table Expressions)**
```sql
WITH churn_stats AS (
  SELECT
    Contract,
    COUNT(*) AS total,
    SUM(Churn = 'Yes') AS churned,
    ROUND(100.0 * SUM(Churn = 'Yes') / COUNT(*), 1) AS churn_pct,
    ROUND(AVG(MonthlyCharges), 2) AS avg_revenue
  FROM customers
  GROUP BY Contract
)
SELECT *,
  ROUND(churned * avg_revenue, 0) AS monthly_revenue_lost
FROM churn_stats
ORDER BY churn_pct DESC;
```

**2. Window Functions**
```sql
WITH customer_risk AS (
  SELECT
    customerID,
    Contract,
    tenure,
    MonthlyCharges,
    Churn,
    NTILE(4) OVER (ORDER BY MonthlyCharges DESC) AS revenue_quartile,
    RANK() OVER (ORDER BY MonthlyCharges DESC) AS revenue_rank,
    AVG(MonthlyCharges) OVER (PARTITION BY Contract) AS avg_charge_by_contract
  FROM customers
)
SELECT * FROM customer_risk
WHERE Churn = 'Yes'
ORDER BY MonthlyCharges DESC;
```

**3. View for Power BI**
```sql
CREATE VIEW vw_churn_dashboard AS
SELECT
  customerID, gender, SeniorCitizen, Contract, tenure,
  InternetService, OnlineSecurity, PaymentMethod,
  MonthlyCharges, TotalCharges, Churn,
  CASE WHEN Churn = 'Yes' THEN 1 ELSE 0 END AS ChurnBin,
  CASE
    WHEN tenure <= 12 THEN '0-12 months'
    WHEN tenure <= 24 THEN '13-24 months'
    WHEN tenure <= 48 THEN '25-48 months'
    ELSE '49+ months'
  END AS tenure_segment,
  CASE
    WHEN MonthlyCharges < 35 THEN 'Low'
    WHEN MonthlyCharges < 65 THEN 'Medium'
    ELSE 'High'
  END AS charge_segment
FROM customers;
```

---

## 📊 Power BI Dashboard

The dashboard consists of **4 interactive pages** connected live to MySQL:

### Page 1 — Executive Overview
- Overall churn rate, total customers, churned count, revenue lost
- Churn distribution donut chart
- Churn by contract type
- Churn trend by tenure segment

### Page 2 — Customer Segments
- Churn by payment method
- Churn by internet service
- Churn by senior citizen vs non-senior
- Churn by charge segment (Low / Medium / High)

### Page 3 — Revenue Impact
- Monthly & annual revenue lost
- Revenue lost by contract type
- Revenue at risk by tenure segment
- Churn Rate vs Target KPI

### Page 4 — Customer Details
- Full interactive table with all churned customers
- Filterable by contract, internet service, gender, senior citizen

---

## 📈 Key Findings

| Insight | Value |
|---------|-------|
| Overall Churn Rate | **26.58%** |
| Monthly Revenue Lost | **$139K** |
| Annual Revenue at Risk | **$1.67M** |
| Highest Risk Contract | Month-to-month (**42.7%**) |
| Highest Risk Payment | Electronic check (**45.3%**) |
| Highest Risk Service | Fiber Optic (**41.9%**) |
| Senior Citizen Churn | **41.7%** vs 23.6% non-senior |
| Lowest Churn Combo | DSL + Online Security (**9.5%**) |

---

## 💡 Business Recommendations

1. **Promote long-term contracts** — 2-year contract customers churn at only 2.8%
2. **Target new customers (0–12 months)** — nearly 1 in 2 churns in the first year
3. **Add Online Security upsell** — reduces Fiber Optic churn by 54%
4. **Encourage auto-payment** — electronic check users churn 3× more than auto-pay
5. **Focus retention on senior citizens** — churn at 41.7%, nearly double the average

---

## 📁 Project Structure

```
telco-churn-analysis/
│
├── sql/
│   ├── create_database.sql
│   ├── create_view.sql
│   ├── analysis_queries.sql
│
├── dashboard/
│   └── telco_churn_Dashboard.pbix
│
├── data/
│   └── WA_Fn-UseC_-Telco-Customer-Churn.csv
│
└── README.md
```

---

## 🚀 How to Run

1. Install **MySQL** and **MySQL Workbench**
2. Run `create_database.sql` to set up the database
3. Import the CSV file into the `customers` table
4. Run `create_view.sql` to create the dashboard view
5. Open `telco_churn_Dashboard.pbix` in **Power BI Desktop**
6. Connect to your local MySQL instance (`localhost → telco_churn`)
7. Refresh the data and explore the dashboard

---

## 📬 Contact

**Omar Alkhyat**

- 🔗 LinkedIn: https://www.linkedin.com/in/omar-alkhyat/
- 📧 Email: da3mar1812004@gmail.com

---

*Built using MySQL + Power BI*
