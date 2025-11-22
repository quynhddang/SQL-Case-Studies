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

**5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?**

### B. Customer Transactions

**1. What is the unique count and total amount for each transaction type?**

**2. What is the average total historical deposit counts and amounts for all customers?**

**3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?**

**4. What is the closing balance for each customer at the end of the month?**

**5. What is the percentage of customers who increase their closing balance by more than 5%?**
