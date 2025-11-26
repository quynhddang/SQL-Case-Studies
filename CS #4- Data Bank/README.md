# 🏦💸 CS# 4: Data Bank

<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/c7013bb9-6857-4f33-8395-03f67a1df815" />

## 📂 Table of Contents

- [Business Task](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%234-%20Data%20Bank/README.md#business-task)
- [Entity Relationship Diagram](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%234-%20Data%20Bank/README.md#entity-relationship-diagram)
- [Questions and Solutions](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%234-%20Data%20Bank/README.md#questions-and-solutions)

All information regarding the case study has been sourced [here](https://8weeksqlchallenge.com/case-study-4/).

## Business Task

Danny decides to launch a new initiative called Data Bank, which runs as both a digital bank as well as a secure distributed data storage platform.

Customers are allocated cloud data storage limits which are directly linked to how much money they have in their accounts.

The management team at Data Bank wants to increase their total customer base and track how much data storage their customers will need.

This case study focuses on calculating metrics, growth, and helping the business analyze their data in a smart way to better forecast and plan for future developments.

## Entity Relationship Diagram

<img width="796" height="342" alt="image" src="https://github.com/user-attachments/assets/26cd52c2-a763-4125-87a5-df4ebc293414" />

## Questions and Solutions

- [A. Customer Nodes Exploration](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%234-%20Data%20Bank/README.md#a-customer-nodes-exploration)
- [B. Customer Transactions](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%234-%20Data%20Bank/README.md#b-customer-transactions)

### A. Customer Nodes Exploration

**1. How many unique nodes are there on the Data Bank system?**

```sql
SELECT COUNT(DISTINCT node_id) AS unique_nodes
FROM customer_nodes;
```

**Answer:**

| unique_nodes |
| ------------ |
| 5            |

- There are 5 unique nodes in the Data Bank system.

**2. What is the number of nodes per region?**

```sql
SELECT 
	r.region_id,
    r.region_name,
    COUNT(DISTINCT c.node_id) as node_count
FROM data_bank.regions AS r
INNER JOIN data_bank.customer_nodes AS c
	ON r.region_id = c.region_id
GROUP BY r.region_id, r.region_name;
```

**Answer:**

| region_id | region_name | node_count |
| --------- | ----------- | ---------- |
| 1         | Australia   | 5          |
| 2         | America     | 5          |
| 3         | Africa      | 5          |
| 4         | Asia        | 5          |
| 5         | Europe      | 5          |

**3. How many customers are allocated to each region?**

```sql
SELECT 
	r.region_id,
    r.region_name,
    COUNT(DISTINCT c.customer_id) as customer_count
FROM data_bank.regions AS r
INNER JOIN data_bank.customer_nodes AS c
	ON r.region_id = c.region_id
GROUP BY r.region_id, r.region_name;
```

**Answer**

| region_id | region_name | customer_count |
| --------- | ----------- | -------------- |
| 1         | Australia   | 110            |
| 2         | America     | 105            |
| 3         | Africa      | 102            |
| 4         | Asia        | 95             |
| 5         | Europe      | 88             |

**4. How many days on average are customers reallocated to a different node?**

```sql
WITH node_days AS (
  SELECT
  	customer_id,
  	node_id,
  	end_date - start_date AS days_in_node
  FROM data_bank.customer_nodes
  WHERE end_date != '9999-12-31'
  GROUP BY customer_id, node_id, start_date, end_date
)
, total_node_days AS (
  SELECT
  	customer_id,
  	node_id,
  	SUM(days_in_node) AS total_days_in_node
  FROM node_days
  GROUP BY customer_id, node_id
)

SELECT 
	ROUND(AVG(total_days_in_node)) AS avg_node_rellocation_days
FROM total_node_days;
```

**Answer:**

| avg_node_reallocation_days |
| -------------------------- |
| 24                         |

- Customers are reallocated every 24 days on average.
  
**5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?**

```sql
WITH reallocation_date AS (
  SELECT *,
  LEAD(start_date) OVER(
    PARTITION BY customer_id ORDER BY start_date) AS realloc_date
  FROM data_bank.customer_nodes
), day_diff AS (
  SELECT *,
  	(realloc_date - start_date) AS day_diff
  FROM reallocation_date
  WHERE realloc_date IS NOT NULL)

SELECT 
	r.region_id,
    r.region_name,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY day_diff) AS median,
    PERCENTILE_CONT(0.8) WITHIN GROUP (ORDER BY day_diff) AS percentile_80,
    PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY day_diff) AS percentile_95
FROM day_diff AS d
JOIN data_bank.regions AS r
	ON d.region_id = r.region_id
GROUP BY r.region_id, r.region_name    
ORDER BY r.region_id, r.region_name;
```

**Answer:**

| region_id | region_name | median | percentile_80 | percentile_90 |
| --------- | ----------- | ------ | ------------- | ------------- |
| 1         | Australia   | 16     | 24            | 29            |
| 2         | America     | 16     | 24            | 29            |
| 3         | Africa      | 16     | 25            | 29            |
| 4         | Asia        | 16     | 24            | 29            |
| 5         | Europe      | 16     | 25            | 29            |

### B. Customer Transactions

**1. What is the unique count and total amount for each transaction type?**

```sql
SELECT
	txn_type,
    COUNT(customer_id) as unique_count,
    SUM(txn_amount) AS total_amount
FROM data_bank.customer_transactions
GROUP BY txn_type;
```

**Answer:**

| txn_type   | unique_count | total_amount |
| ---------- | ------------ | ------------ |
| purchase   | 1617         | 806537       |
| deposit    | 2671         | 1359168      |
| withdrawal | 1580         | 793003       |

**2. What is the average total historical deposit counts and amounts for all customers?**

```sql
WITH deposit_summary AS (
  SELECT
	customer_id,
    txn_type,
    COUNT(customer_id) AS txn_count,
    SUM(txn_amount) AS avg_amount
  FROM data_bank.customer_transactions
  WHERE txn_type = 'deposit'
  GROUP BY customer_id, txn_type
)

SELECT
	ROUND(AVG(txn_count), 0) AS average_historical_deposit,
    ROUND(AVG(avg_amount), 2) AS average_deposit_amount
FROM deposit_summary;
```

**Answer:**

| average_historical_deposit | average_deposit_amount |
| -------------------------- | ---------------------- |
| 5                          | 2718.34                |

**3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?**

```sql
WITH monthly_transaction AS (
  SELECT 
  	customer_id, 
  	DATE_PART('month', txn_date) AS month_id,
  	TO_CHAR(txn_date, 'month') AS month_name,
  	SUM(CASE WHEN txn_type = 'deposit' THEN 0 ELSE 1 END) AS deposit_count,
  	SUM(CASE WHEN txn_type = 'purchase' THEN 0 ELSE 1 END) AS purchase_count,
  	SUM(CASE WHEN txn_type = 'withdrawal' THEN 1 ELSE 0 END) AS withdrawal_count
  FROM data_bank.customer_transactions
  GROUP BY customer_id, DATE_PART('month', txn_date), TO_CHAR(txn_date, 'month')
)
    
SELECT
	month_id,
    month_name,
    COUNT(DISTINCT customer_id) AS customer_count
FROM monthly_transaction
WHERE deposit_count > 1
	AND (purchase_count >= 1 OR withdrawal_count >= 1)
GROUP BY month_id, month_name;
```

| month_id | month_name | customer_count |
| -------- | ---------- | -------------- |
| 1        | january    | 170            |
| 2        | february   | 277            |
| 3        | march      | 292            |
| 4        | april      | 103            |

**4. What is the closing balance for each customer at the end of the month?**

```sql
WITH customer_balance AS (
  SELECT
  	customer_id,
  	DATE_PART('month', txn_date) AS month,
  	TO_CHAR(txn_date, 'month') AS month_name,
  	SUM(CASE
        WHEN txn_type = 'deposit' THEN txn_amount ELSE -txn_amount END) AS net_amount
  FROM customer_transactions
  GROUP BY customer_id, month, month_name
  ORDER BY customer_id
)
  
SELECT
	customer_id,
    month,
    month_name,
    SUM(net_amount) OVER (
      PARTITION BY customer_id ORDER BY month_name 
      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS closing_balance
FROM customer_balance
GROUP BY customer_id, month, month_name, net_amount
ORDER BY customer_id, month;
```

**Answer:**

| customer_id | month | month_name | closing_balance |
| ----------- | ----- | ---------- | --------------- |
| 1           | 1     | january    | 312             |
| 1           | 3     | march      | -640            |
| 2           | 1     | january    | 549             |
| 2           | 3     | march      | 610             |
| 3           | 1     | january    | -328            |
| 3           | 2     | february   | -472            |
| 3           | 3     | march      | -729            |
| 3           | 4     | april      | 493             |
| 4           | 1     | january    | 848             |
| 4           | 3     | march      | 655             |

- Since the result is too long, only the results for customers 1-4 are shown.

**5. What is the percentage of customers who increase their closing balance by more than 5%?**

```sql
WITH customer_balance AS (
  SELECT
  	customer_id,
  	DATE_PART('month', txn_date) AS month,
  	SUM(CASE
        WHEN txn_type = 'deposit' THEN txn_amount ELSE -txn_amount END) AS net_amount
  FROM customer_transactions
  GROUP BY customer_id, month 
  ORDER BY customer_id
), closing_balance AS(
  SELECT
	customer_id,
    month,
    SUM(net_amount) OVER (PARTITION BY customer_id, month ORDER BY month
      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS closing_balance
FROM customer_balance
GROUP BY customer_id, month, net_amount
ORDER BY customer_id
), percentage_inc AS (
  SELECT
  	customer_id,
  	month,
  	closing_balance,
  	100 *(closing_balance - LAG(closing_balance) OVER(PARTITION BY customer_id ORDER BY	month))
   		 / NULLIF( LAG(closing_balance) OVER(PARTITION BY customer_id ORDER BY	month), 2) AS percentage_increase
  FROM closing_balance
)

SELECT
	100 * COUNT(DISTINCT customer_id)/ (SELECT COUNT(DISTINCT customer_id) FROM customer_transactions)::float AS percentage_customer
FROM percentage_inc
WHERE percentage_increase > 5;
```

**Answer:** 

| percentage_customer |
| ------------------- |
| 53.8                |

- 53.8% of customer have a closing balance exceeding 5%. 
