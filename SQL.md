# SQL Important Notes

## 1. Get the Recent 5 Rows

```sql
SELECT * FROM payment ORDER BY payment_date DESC LIMIT 5;
```

Returns the 5 most recent payments.

---

## 2. BETWEEN Operator

Equivalent to:

```sql
value >= low AND value <= high
-- OR
value BETWEEN low AND high
```

**Examples:**

```sql
SELECT * FROM public.payment WHERE amount BETWEEN 8 AND 9;
SELECT * FROM payment WHERE amount >= 8 AND amount <= 9;
```

---

## 3. NOT BETWEEN Operator

Equivalent to:

```sql
value < low OR value > high
-- OR
value NOT BETWEEN low AND high
```

**Examples:**

```sql
SELECT * FROM public.payment WHERE amount NOT BETWEEN 8 AND 9;
SELECT * FROM payment WHERE amount <= 8 OR amount >= 9;
```

---

## 4. LIKE & ILIKE

Pattern matching with wildcard characters:

### Wildcards:

* `%` : Matches any sequence of characters
* `_` : Matches any single character

### Examples:

```sql
-- Begins with 'A'
WHERE name LIKE 'A%';

-- Ends with 'A'
WHERE name LIKE '%A';

-- Case-insensitive match
WHERE name ILIKE 'a%';

-- Matches pattern with single character
WHERE title LIKE 'Mission Impossible_';
```

---

## 5. GROUP BY

Used to aggregate data and apply functions per category.

**Sample Table:**

| Data | Value |
| ---- | ----- |
| A    | 10    |
| A    | 5     |
| B    | 2     |
| B    | 4     |
| C    | 12    |
| C    | 6     |

**Query:**

```sql
SELECT data, SUM(value) AS result FROM Category GROUP BY data;
```

**Output:**

| Data | Result |
| ---- | ------ |
| A    | 15     |
| B    | 6      |
| C    | 18     |

---

## 6. HAVING Clause

Used to filter results after aggregation.

**Invalid (aggregates used in WHERE):**

```sql
SELECT company, SUM(sales)
FROM Finance
WHERE company != 'Google' AND SUM(sales) > 1000
GROUP BY company;
```

**Correct (use HAVING):**

```sql
SELECT company, SUM(sales)
FROM Finance
WHERE company != 'Google'
GROUP BY company
HAVING SUM(sales) > 1000;
```

---

## 7. JOINs

Used to combine rows from two or more tables.

### A) INNER JOIN

```sql
SELECT * FROM TableA INNER JOIN TableB ON TableA.col_match = TableB.col_match;
```

Returns records that have matching values in both tables.

### Example:

**Registrations**

| reg\_id | name    |
| ------- | ------- |
| 1       | Andrew  |
| 2       | Bob     |
| 3       | Charlie |
| 4       | David   |

**Logins**

| log\_id | name    |
| ------- | ------- |
| 1       | Xavier  |
| 2       | Andrew  |
| 3       | Yolanda |
| 4       | Bob     |

```sql
SELECT * FROM Registrations
INNER JOIN Logins ON Registrations.name = Logins.name;
```

**Output:**

| reg\_id | name   | log\_id | name   |
| ------- | ------ | ------- | ------ |
| 1       | Andrew | 2       | Andrew |
| 2       | Bob    | 4       | Bob    |

### B) FULL OUTER JOIN

```sql
SELECT * FROM TableA FULL OUTER JOIN TableB ON TableA.col_match = TableB.col_match;
```

Returns all rows when there is a match in one of the tables.

**Output:**

| reg\_id | name    | log\_id | name    |
| ------- | ------- | ------- | ------- |
| 1       | Andrew  | 2       | Andrew  |
| 2       | Bob     | 4       | Bob     |
| 3       | Charlie | null    | null    |
| 4       | David   | null    | null    |
| null    | null    | 1       | Xavier  |
| null    | null    | 3       | Yolanda |

### C) LEFT OUTER JOIN

```sql
SELECT * FROM TableA LEFT JOIN TableB ON TableA.col_match = TableB.col_match;
```

Returns all records from the left table and matched records from the right table.

### D) RIGHT OUTER JOIN

```sql
SELECT * FROM TableA RIGHT JOIN TableB ON TableA.col_match = TableB.col_match;
```

Returns all records from the right table and matched records from the left table.

### E) SELF JOIN

Used when a table is joined with itself.

```sql
SELECT emp.name, report.name AS rep
FROM employees AS emp
JOIN employees AS report
ON emp.report_id = report.emp_id;
```

**Employees Table:**

| emp\_id | name    | report\_id |
| ------- | ------- | ---------- |
| 1       | Andrew  | 3          |
| 2       | Bob     | 3          |
| 3       | Charlie | 4          |
| 4       | David   | 1          |

**Output:**

| name    | rep     |
| ------- | ------- |
| Andrew  | Charlie |
| Bob     | Charlie |
| Charlie | David   |
| David   | Andrew  |

---

## 8. UNION

Combines results of two or more SELECT statements (removes duplicates).

```sql
SELECT column_name FROM table1
UNION
SELECT column_name FROM table2;
```

---

## 9. Constraints

Constraints enforce rules for column data.

### Column Constraints:

* **NOT NULL**: Column cannot have NULL values
* **UNIQUE**: All values must be unique
* **PRIMARY KEY**: Unique + NOT NULL
* **FOREIGN KEY**: References primary key in another table
* **CHECK**: Restrict values based on a condition
* **EXCLUSION**: Ensure values exclude each other by condition

### Table Constraints:

* **CHECK (condition)**
* **REFERENCES**
* **UNIQUE(column\_list)**
* **PRIMARY KEY(column\_list)**

---

## 10. CREATE TABLE

```sql
CREATE TABLE account (
    user_id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    password VARCHAR(50) NOT NULL,
    email VARCHAR(250) UNIQUE,
    created_at TIMESTAMP NOT NULL,
    last_login TIMESTAMP
);
```

---

## 11. CREATE INDEX

Used for faster data retrieval:

```sql
CREATE INDEX idx_user_email ON account(user_id, email);

SELECT * FROM account WHERE user_id = 12 AND email = 'test@gmail.com';
```

---

## 12. ALTER Clause

Used to modify table structure.

### Syntax:

```sql
ALTER TABLE table_name action;
```

### Examples:

```sql
-- Add column
ALTER TABLE table_name ADD COLUMN new_column TYPE;

-- Drop column
ALTER TABLE table_name DROP COLUMN column_name;
```

---

## 13. DROP Table

Removes the table or a column from the database.

```sql
-- Drop table
DROP TABLE account;

-- Drop column
ALTER TABLE account DROP COLUMN password;
```

---

## 14. How to Get the Second Highest Salary in SQL

There are multiple ways to get the **second highest salary** from a table (e.g., `employees`).

---

### 🔹 Option 1: Using `LIMIT` with `OFFSET` (MySQL / PostgreSQL)

```sql
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

### 🔹 Option 2: Using `MAX()` with Subquery
```sql
SELECT MAX(salary)
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

### 🔹 Option 3: Using `DENSE_RANK()`
```sql
SELECT salary
FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked_salaries
WHERE rnk = 2;
```

### 🔹 Option 4: Using `ROW_NUMBER()`
```sql
SELECT salary
FROM (
    SELECT salary, ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num
    FROM employees
) ranked_salaries
WHERE row_num = 2;
```

---

## 15. Bulk Insert

There are two ways to perform bulk inserts:

### a) Using Temp Tables
```sql
INSERT INTO Users#temp (fName, lName) VALUES ('A', 'B');
INSERT INTO Users#temp (fName, lName) VALUES ('C', 'D');

INSERT INTO Users (fName, lName) SELECT fName, lName FROM Users#temp;
```

### b) Multi-Value Insert
```sql
INSERT INTO Users (fName, lName) VALUES ('A', 'B'), ('C', 'D');
```