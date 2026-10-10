# Sales Insights with Power BI and MySQL

A portfolio project exploring sales across markets, customers, and time, with SQL exploration and Power BI reporting.

## Start here

- [Updated Power BI report](updated_dump.pbix)
- [Original Power BI report](dump.pbix)
- [MySQL database dump](db_dump.sql)
- [Project document](%F0%9D%97%A6%F0%9D%97%AE%F0%9D%97%B9%F0%9D%97%B2%F0%9D%98%80%20%F0%9D%97%9C%F0%9D%97%BB%F0%9D%98%80%F0%9D%97%B6%F0%9D%97%B4%F0%9D%97%B5%F0%9D%98%81%F0%9D%98%80%20%E2%80%93%20%F0%9D%97%A3%F0%9D%97%BF%F0%9D%97%BC%F0%9D%97%B7%F0%9D%97%B2%F0%9D%97%B0%F0%9D%98%81.pdf)

## Business questions

How do sales vary by market and month? Which products sell in a particular market? How should transactions in different currencies be made comparable?

## Explore locally

1. Import `db_dump.sql` into a local MySQL instance.
2. Open a PBIX file in Power BI Desktop.
3. Update the data-source connection to your local database and refresh.
4. Review currency labels and conversion assumptions before interpreting revenue totals.

The Power Query expression below uses a fixed project assumption of **1 USD = 75 INR**. It is not a live or historical exchange-rate series. The SQL examples keep raw amounts separated by currency; do not add INR and USD amounts directly.

## Data Analysis Using SQL

### Show all customer records

```sql
SELECT * FROM customers;
```

### Show total number of customers

```sql
SELECT COUNT(*) FROM customers;
```

### Show transactions for Chennai market (market code for Chennai is Mark001)

```sql
SELECT * 
FROM transactions 
WHERE market_code = 'Mark001';
```

### Show distinct product codes that were sold in Chennai

```sql
SELECT DISTINCT product_code 
FROM transactions 
WHERE market_code = 'Mark001';
```

### Show transactions where currency is US Dollars

```sql
SELECT * 
FROM transactions 
WHERE currency = 'USD';
```

### Show transactions in 2020 (joined with date table)

```sql
SELECT t.*, d.*
FROM transactions t
INNER JOIN date d 
    ON t.order_date = d.date
WHERE d.year = 2020;
```

### Show 2020 sales amounts by currency

```sql
SELECT t.currency, SUM(t.sales_amount) AS sales_amount
FROM transactions t
INNER JOIN date d 
    ON t.order_date = d.date
WHERE d.year = 2020
  AND (t.currency = 'INR' OR t.currency = 'USD')
GROUP BY t.currency;
```

### Show January 2020 sales amounts by currency

```sql
SELECT t.currency, SUM(t.sales_amount) AS sales_amount
FROM transactions t
INNER JOIN date d 
    ON t.order_date = d.date
WHERE d.year = 2020
  AND d.month_name = 'January'
  AND (t.currency = 'INR' OR t.currency = 'USD')
GROUP BY t.currency;
```

### Show 2020 Chennai sales amounts by currency

```sql
SELECT t.currency, SUM(t.sales_amount) AS sales_amount
FROM transactions t
INNER JOIN date d 
    ON t.order_date = d.date
WHERE d.year = 2020
  AND t.market_code = 'Mark001'
GROUP BY t.currency;
```

---

## Data Analysis Using Power BI

### Formula to create norm_amount column

```m
= Table.AddColumn(
    #"Filtered Rows",
    "norm_amount",
    each if [currency] = "USD" or [currency] = "USD#(cr)" 
         then [sales_amount] * 75 
         else [sales_amount],
    type any
)
```



