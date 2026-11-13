# Olist E-commerce Analysis (SQL + Power BI)

End-to-end analysis of a Brazilian e-commerce marketplace: PostgreSQL for the analysis (window functions, CTEs), Power BI for a 2-page dashboard with DAX measures.

## Business Problem

Olist is a marketplace connecting small sellers to customers across Brazil. This project answers four questions:

1. How is revenue growing month over month, and when did it peak?
2. Which product categories and sellers drive revenue?
3. How many customers come back for a second purchase?
4. Does late delivery hurt customer satisfaction?

## Dataset

* **Source:** [Brazilian E-Commerce Public Dataset by Olist (Kaggle)](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
* **Size:** about 99K orders and 112K order items (2016-2018), across 8 related tables (customers, orders, order\_items, payments, reviews, products, sellers, category translation)
* **Currency:** Brazilian Real (R$)
* **Scope:** only orders with status `delivered` (96,478 orders) are used for revenue and delivery analysis

## Tools Used

|Area|Tools|
|-|-|
|Database and analysis|PostgreSQL 13, pgAdmin 4|
|SQL techniques|Multi-table JOINs (INNER and LEFT), CTEs, window functions (`LAG`, `RANK`), `CASE WHEN`, `FILTER`, `COALESCE`|
|Dashboard|Power BI Desktop|
|DAX|`CALCULATE`, `DIVIDE`, `DATEADD`, `DATEDIFF`, `ADDCOLUMNS`, variables (`VAR`), a calendar table|

## Key Insights

All numbers come from the SQL queries in [`olist\_analysis.sql`](olist_analysis.sql) and are cross-checked in the Power BI dashboard.

**1. Revenue peaked in November 2017 (Black Friday).**
Revenue reached R$ 987,765 in Nov 2017, up 52.4% over October, then fell 26.5% in December. Revenue plateaued at about R$ 0.85M-0.98M per month through 2018, with a 12.4% drop in June 2018.

**2. Revenue is concentrated in a few categories and states.**
`health\_beauty` (R$ 1.23M), `watches\_gifts` (R$ 1.17M) and `bed\_bath\_table` (R$ 1.02M) lead the categories. São Paulo (SP) contributes about 39% of total revenue (R$ 13.22M overall).

**3. Only 3% of customers ever reorder.**
Of 93,358 customers, just 2,801 placed more than one order (3.00%). The business depends heavily on acquiring new customers.

**4. Late delivery sharply lowers review scores.**

|Delivery|Orders|Avg review score|
|-|-|-|
|On time / early|89,443|4.29|
|1-7 days late|3,600|2.72|
|8+ days late|2,781|1.70|

About 6.7% of orders arrive late, but those orders score 1.7 to 2.7 stars against 4.29 for on-time orders (a 60% drop for 8+ days late).

> Note: the dashboard shows 2.71 for the 1-7 days late bucket. The 0.01 difference comes from how duplicate review rows are averaged (SQL averages per order first, Power BI averages all review rows).

## Dashboard

**Page 1: Sales Overview** (KPI cards, monthly revenue trend, top 10 categories, top 10 states)

!\[Sales Overview](screenshots/page1\_sales\_overview.png)

**Page 2: Delivery and Customer Experience** (late delivery %, repeat rate, review score by delivery time)

!\[Delivery and Customers](screenshots/page2\_delivery\_customers.png)

A PDF export is available in [`olist\_dashboard.pdf`](olist_dashboard.pdf).

## Recommendations

1. **Fix delivery reliability first.** Late orders are only about 7% of volume but cause the worst reviews. Warn customers early about orders likely to run 8+ days late, and set more realistic delivery estimates.
2. **Improve retention.** With a 3% repeat rate, a follow-up offer or loyalty programme within 30 days of the first order could add repeat revenue.
3. **Reduce dependence on one region and a few categories.** SP and the top categories carry a large share of revenue; growing other states and categories lowers that risk.
4. **Plan for seasonality.** Black Friday drove the biggest spike and the following December drop, so inventory and seller capacity should be planned ahead of November.

## Repository Structure

```
olist-ecommerce-analysis/
├── README.md
├── olist\_analysis.sql        # table creation, data checks, all queries
├── olist\_project.pbix        # Power BI dashboard
├── olist\_dashboard.pdf       # PDF export of the dashboard
├── outputs/                  # exported query results (CSV)
└── screenshots/              # dashboard screenshots
```

## How to Run

1. Download the dataset from Kaggle and extract the CSV files.
2. Create a PostgreSQL database named `olist`.
3. Run the `CREATE TABLE` section of `olist\_analysis.sql`, then import each CSV into its table (pgAdmin: Import/Export Data, header on, UTF8).
4. Run the data checks and queries in `olist\_analysis.sql`.
5. Open `olist\_project.pbix` in Power BI Desktop.

> The Power BI report loads the original CSV files directly. After opening the `.pbix`, you may need to repoint the data source to your local CSV folder (Home > Transform data > Data source settings).

## What I Learned

* Writing multi-step SQL with CTEs and window functions, and checking data quality before analysing (row counts, duplicate reviews, missing translations).
* Using `customer\_unique\_id` instead of `customer\_id` to measure repeat customers correctly.
* Rebuilding the same metrics in DAX and checking they match the SQL results.

## Author

**Arivazhagan R** | [LinkedIn](https://linkedin.com/in/arivazhagan-r-7a60312a3) | [GitHub](https://github.com/ARIVUCSE)

