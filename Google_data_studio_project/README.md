# Creating a sales dashboard with returns

**Objective of the project:**

The product team has a new quarterly task: working with returns. So that you can display returns analytics in dashboards, the IT team rolled out a new field in the table with orders (invoices) - order type (type).

Now there is not only income, but also returns. At the same time, return invoices are not the status of an old order, but new orders, which are manually added by the sales department twice a week, and now in dashboards, if you simply display the amount of revenue without a filter by order type, then each order for which a return was made is duplicated return invoice and each invoice has its own invoice_id.
 
**Initial data:**

Sales data is stored in a Google Cloud database. We have access to connect to the database via BigQuery.

Database documentation - <https://docs.google.com/spreadsheets/d/1P4z7XRBUaK_1okHLudHi9YKmmThAqlOOMkGW9PYrDZg/edit?usp=share_link>

**Project Objective:**

Collect a dashboard that will show products the dynamics and current situation regarding returns in the context of the general situation for products.

The refund amount is not always equal to the payment amount, since in initial payments we retain the commission of payment systems, and in renewals we most often return the full amount.

**SQL query**

Query to obtain the necessary data in BigQuery for a project:

```sql
WITH Income_Refund AS (
  SELECT
    product
    paid_date,
    country,
    payment_system,
    SUM(CASE WHEN type = 'income' THEN paid_amount ELSE 0 END) AS total_income,
    SUM(CASE WHEN type = 'refund' THEN paid_amount ELSE 0 END) AS total_refund
  FROM `takeclass-test.test.ff_sales_test`
  GROUP BY product, paid_date, country, payment_system
)
SELECT
  product
  paid_date,
  country,
  payment_system,
  total_income,
  total_refund,
  total_income - total_refund AS net_income
FROM Income_Refund;
```

**Project completed in Google Data Studio:**

Link to the project - <https://lookerstudio.google.com/reporting/793631d5-0da9-4246-9f09-fd7fcc2d0b2c>

**Components:**

Filters: by currency, product and payment dates.
Amounts of returns, revenue and net income for the period.
Time Series Line Graph: Displays the dynamics of earnings and returns over time.
Pie chart: Shows the ratio of reimbursement amounts by product.
Histogram: Explores the performance of various payment systems in terms of returns and revenue.
Geographic heat map: Shows the countries from which the most returns originate.
Table: Displays information such as revenue, returns, and net income by product.

**Control panel logic**

How to view the data:

- Select payment currency from the filter.
- Compare revenue and reimbursement trends to detect anomalies.
- Identify products that have a high return-to-income ratio that require study.

Answers on questions:

- What are the revenue and reimbursement trends?
- Which products have the highest number of returns in relation to income?
- What is the geographic distribution of reimbursements?

Data Limitations:

Returns are manually added twice a week, which can result in potential delays in real-time analytics.

**Data improvement request:**

To further improve the analysis, ask the IT team to add:

- return_processing_date: date of actual processing of the return.
- return_initiated_by: indicates who initiated the refund: the customer or the sales department.

Using additional data, it will be possible to effectively analyze the dynamics of both income and returns, which is important for understanding the financial condition of the product.