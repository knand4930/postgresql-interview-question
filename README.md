# SQL Keywords Complete Reference

[![SQL](https://img.shields.io/badge/SQL-Standard-blue.svg)](https://www.iso.org/standard/63555.html)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-336791.svg)](https://www.postgresql.org/)
[![MySQL](https://img.shields.io/badge/MySQL-8.0+-4479A1.svg)](https://www.mysql.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

> A comprehensive guide to SQL keywords with examples, properties, and relationships. Perfect for developers, database administrators, and data analysts.

## 📚 Table of Contents

- [Core SQL Language Keywords](#-core-sql-language-keywords)
- [Logical Operators & Conditions](#-logical-operators--conditions)
- [JOIN Operations](#-join-operations)
- [Conditional Logic](#-conditional-logic)
- [Data Definition Language (DDL)](#-data-definition-language-ddl)
- [Constraints & Keys](#-constraints--keys)
- [Functions](#-functions)
- [Window Functions](#-window-functions)
- [Transaction Control](#-transaction-control)
- [Advanced SQL Features](#-advanced-sql-features)
- [Views and Performance](#-views-and-performance)
- [PL/pgSQL Special Variables](#-plpgsql-special-variables)
- [Environment Variables](#-environment-variables)
- [System Catalogs & Information Schema](#-system-catalogs--information-schema)
- [Advanced Data Types](#-advanced-data-types)
- [Text Search and Full-Text Search](#-text-search-and-full-text-search)
- [Stored Procedures and Functions](#-stored-procedures-and-functions)
- [Triggers and Rules](#-triggers-and-rules)
- [Security and Permissions](#-security-and-permissions)
- [Advanced Query Techniques](#-advanced-query-techniques)
- [Keywords Relationships Summary](#-keywords-relationships-summary)

---

## 🔹 Core SQL Language Keywords

### SELECT
**Purpose**: Retrieves data from one or more tables  
**Properties**: 
- Most fundamental SQL command
- Can be combined with almost all other keywords
- Supports subqueries and complex expressions

```sql
-- Basic SELECT
SELECT first_name, last_name FROM employees;

-- SELECT with calculations
SELECT product_name, price, price * 0.1 AS tax FROM products;

-- SELECT with subquery
SELECT name FROM customers WHERE id IN (SELECT customer_id FROM orders);
```

**Relations**: Works with FROM, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT, JOINS

### INSERT
**Purpose**: Adds new rows to a table  
**Properties**:
- Creates new data records
- Can insert single or multiple rows
- Can use subqueries for data source

```sql
-- Single row insert
INSERT INTO employees (first_name, last_name, salary) 
VALUES ('John', 'Doe', 50000);

-- Multiple rows insert
INSERT INTO employees (first_name, last_name, salary) 
VALUES 
    ('Jane', 'Smith', 55000),
    ('Bob', 'Johnson', 48000);

-- Insert from SELECT
INSERT INTO archived_orders 
SELECT * FROM orders WHERE order_date < '2023-01-01';
```

**Relations**: Works with VALUES, SELECT (for subqueries), ON CONFLICT

### UPDATE
**Purpose**: Modifies existing data in tables  
**Properties**:
- Changes existing records
- Must use WHERE to avoid updating all rows
- Can update multiple columns simultaneously

```sql
-- Basic update
UPDATE employees SET salary = 55000 WHERE employee_id = 1;

-- Update multiple columns
UPDATE products 
SET price = price * 1.1, last_updated = NOW() 
WHERE category = 'Electronics';

-- Update with JOIN
UPDATE employees e 
SET salary = salary * 1.05 
FROM departments d 
WHERE e.dept_id = d.id AND d.name = 'Engineering';
```

**Relations**: Works with SET, WHERE, FROM, JOIN

### DELETE
**Purpose**: Removes rows from tables  
**Properties**:
- Permanently removes data
- Can be conditional with WHERE
- Can cascade with foreign key constraints

```sql
-- Conditional delete
DELETE FROM employees WHERE last_login < '2023-01-01';

-- Delete with subquery
DELETE FROM orders 
WHERE customer_id IN (SELECT id FROM customers WHERE status = 'inactive');

-- Delete all records (dangerous!)
DELETE FROM temp_table;
```

**Relations**: Works with WHERE, FROM (in some databases for joins)

### FROM
**Purpose**: Specifies the source table(s) for data retrieval  
**Properties**:
- Essential for SELECT, UPDATE, DELETE operations
- Can reference tables, views, subqueries
- Supports table aliases

```sql
-- Basic FROM
SELECT * FROM employees;

-- FROM with alias
SELECT e.name, d.department_name 
FROM employees e, departments d;

-- FROM with subquery
SELECT * FROM (
    SELECT name, salary FROM employees WHERE salary > 50000
) AS high_earners;
```

**Relations**: Works with SELECT, JOIN, WHERE, aliases (AS)

---

## 🔹 Logical Operators & Conditions

### AND / OR / NOT
**Purpose**: Combine multiple conditions in WHERE clauses  
**Properties**:
- AND: All conditions must be true
- OR: At least one condition must be true
- NOT: Negates a condition
- Precedence: NOT > AND > OR (use parentheses for clarity)

```sql
-- AND operator
SELECT * FROM employees 
WHERE salary > 50000 AND department = 'Engineering';

-- OR operator
SELECT * FROM employees 
WHERE department = 'Sales' OR department = 'Marketing';

-- NOT operator
SELECT * FROM customers 
WHERE NOT country = 'USA';

-- Complex combinations with parentheses
SELECT * FROM products 
WHERE (category = 'Electronics' OR category = 'Books') 
  AND price BETWEEN 10 AND 100 
  AND NOT discontinued;
```

**Relations**: Used with WHERE, HAVING, CASE statements

### IN / NOT IN
**Purpose**: Check if value exists in a list or subquery result  
**Properties**:
- More readable than multiple OR conditions
- Can use with subqueries
- NULL values require special handling

```sql
-- IN with list
SELECT * FROM employees 
WHERE department IN ('Sales', 'Marketing', 'HR');

-- IN with subquery
SELECT * FROM products 
WHERE category_id IN (
    SELECT id FROM categories WHERE active = true
);

-- NOT IN
SELECT * FROM customers 
WHERE country NOT IN ('USA', 'Canada', 'Mexico');
```

**Relations**: Alternative to multiple OR conditions, works with subqueries

### EXISTS / NOT EXISTS
**Purpose**: Check if subquery returns any rows  
**Properties**:
- More efficient than IN for large datasets
- Returns TRUE/FALSE based on subquery results
- Handles NULL values better than IN

```sql
-- EXISTS
SELECT * FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o 
    WHERE o.customer_id = c.id
);

-- NOT EXISTS
SELECT * FROM products p
WHERE NOT EXISTS (
    SELECT 1 FROM order_items oi 
    WHERE oi.product_id = p.id
);
```

**Relations**: Used with correlated subqueries, alternative to IN/NOT IN

### BETWEEN / NOT BETWEEN
**Purpose**: Check if value falls within a range (inclusive)  
**Properties**:
- Inclusive of boundary values
- Works with numbers, dates, strings
- More readable than >= AND <=

```sql
-- Numeric range
SELECT * FROM employees WHERE salary BETWEEN 40000 AND 80000;

-- Date range
SELECT * FROM orders 
WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31';

-- NOT BETWEEN
SELECT * FROM products WHERE price NOT BETWEEN 10 AND 50;
```

**Relations**: Shorthand for range conditions with AND

### LIKE / ILIKE / NOT LIKE
**Purpose**: Pattern matching in text  
**Properties**:
- % matches any sequence of characters
- _ matches any single character
- ILIKE is case-insensitive (PostgreSQL)
- Can use ESCAPE for literal % or _

```sql
-- Basic pattern matching
SELECT * FROM customers WHERE name LIKE 'John%';

-- Single character wildcard
SELECT * FROM products WHERE code LIKE 'A_B_';

-- Case-insensitive (PostgreSQL)
SELECT * FROM customers WHERE name ILIKE '%smith%';

-- Escape special characters
SELECT * FROM products WHERE description LIKE '%50\% off%' ESCAPE '\';
```

**Relations**: Text pattern matching, used in WHERE clauses

---

## 🔹 JOIN Operations

### JOIN / INNER JOIN
**Purpose**: Combines rows from tables based on related columns  
**Properties**:
- Returns only matching rows from both tables
- Most common join type
- Requires ON or USING clause

```sql
-- Basic INNER JOIN
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.id;

-- Multiple JOINs
SELECT c.name, o.order_date, p.product_name
FROM customers c
JOIN orders o ON c.id = o.customer_id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id;
```

### LEFT JOIN
**Purpose**: Returns all rows from left table, matching rows from right table  
**Properties**:
- NULL values for non-matching right table columns
- Preserves all left table data

```sql
-- All employees with their departments (even if no department)
SELECT e.name, d.department_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.id;
```

### USING (in JOINs)
**Purpose**: Simplified JOIN syntax when column names are identical  
**Properties**:
- Only works when join columns have same names
- Eliminates duplicate columns in result
- Cleaner syntax for natural joins

```sql
-- Traditional JOIN
SELECT * FROM employees e
JOIN departments d ON e.department_id = d.department_id;

-- Using USING
SELECT * FROM employees e
JOIN departments d USING (department_id);
```

---

## 🔹 Advanced SQL Features

### FETCH
**Purpose**: SQL standard alternative to LIMIT  
**Properties**:
- Part of SQL standard (more portable)
- Used with OFFSET for pagination
- More verbose but standardized syntax

```sql
-- Fetch first 10 rows
SELECT * FROM employees 
ORDER BY hire_date
FETCH FIRST 10 ROWS ONLY;

-- With offset
SELECT * FROM employees 
ORDER BY hire_date
OFFSET 20 ROWS
FETCH NEXT 10 ROWS ONLY;
```

### ON CONFLICT / UPSERT
**Purpose**: Handle INSERT conflicts (PostgreSQL specific)  
**Properties**:
- Alternative to INSERT failures
- Can update or ignore conflicts
- Useful for data synchronization

```sql
-- Insert or update
INSERT INTO products (id, name, price) 
VALUES (1, 'Widget', 19.99)
ON CONFLICT (id) 
DO UPDATE SET 
    name = EXCLUDED.name,
    price = EXCLUDED.price,
    updated_at = NOW();

-- Insert or ignore
INSERT INTO categories (name) 
VALUES ('Electronics')
ON CONFLICT (name) DO NOTHING;
```

### RETURNING
**Purpose**: Return data from INSERT, UPDATE, DELETE operations  
**Properties**:
- Get generated IDs from INSERT
- See changed values from UPDATE
- Confirm deleted records

```sql
-- Get generated ID
INSERT INTO customers (name, email) 
VALUES ('John Doe', 'john@example.com')
RETURNING id, created_at;

-- See updated values
UPDATE employees 
SET salary = salary * 1.1 
WHERE department = 'Sales'
RETURNING name, salary;
```

---

## 🔹 Data Definition Language (DDL)

### CREATE TABLE
**Purpose**: Creates new database tables  
**Properties**:
- Defines column names, types, and constraints
- Can specify primary keys, foreign keys
- Various data types available

```sql
-- Basic table creation
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    hire_date DATE DEFAULT CURRENT_DATE,
    salary NUMERIC(10,2) CHECK (salary > 0)
);

-- Table with foreign key
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    customer_id INTEGER REFERENCES customers(id),
    order_date TIMESTAMP DEFAULT NOW(),
    total DECIMAL(10,2)
);
```

### ALTER TABLE
**Purpose**: Modifies existing table structure  
**Properties**:
- Add, drop, or modify columns
- Add or drop constraints
- Rename table or columns

```sql
-- Add column
ALTER TABLE employees ADD COLUMN phone VARCHAR(20);

-- Modify column
ALTER TABLE employees ALTER COLUMN salary TYPE DECIMAL(12,2);

-- Add constraint
ALTER TABLE employees ADD CONSTRAINT chk_salary 
    CHECK (salary BETWEEN 20000 AND 200000);
```

### CREATE INDEX
**Purpose**: Create database indexes for faster queries  
**Properties**:
- Improves SELECT performance
- Slows down INSERT/UPDATE/DELETE
- Various index types available

```sql
-- Basic index
CREATE INDEX idx_employees_department ON employees(department);

-- Composite index
CREATE INDEX idx_orders_customer_date ON orders(customer_id, order_date);

-- Partial index
CREATE INDEX idx_active_employees ON employees(name) 
WHERE status = 'active';

-- Functional index
CREATE INDEX idx_lower_email ON customers(LOWER(email));
```

---

## 🔹 Constraints & Keys

### PRIMARY KEY
**Purpose**: Uniquely identifies each row in a table  
**Properties**:
- Cannot be NULL
- Must be unique
- Only one per table
- Automatically creates index

```sql
-- Single column primary key
CREATE TABLE customers (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100)
);

-- Composite primary key
CREATE TABLE order_items (
    order_id INTEGER,
    product_id INTEGER,
    quantity INTEGER,
    PRIMARY KEY (order_id, product_id)
);
```

### FOREIGN KEY
**Purpose**: Links to primary key in another table  
**Properties**:
- Enforces referential integrity
- Can have cascade options
- Can reference multiple columns

```sql
-- Basic foreign key
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    customer_id INTEGER REFERENCES customers(id)
);

-- With cascade options
CREATE TABLE order_items (
    order_id INTEGER REFERENCES orders(id) ON DELETE CASCADE,
    product_id INTEGER REFERENCES products(id) ON UPDATE CASCADE
);
```

### CHECK
**Purpose**: Enforces domain integrity with custom conditions  
**Properties**:
- Boolean expression that must be true
- Can reference multiple columns
- Evaluated on INSERT/UPDATE

```sql
-- Simple check constraint
CREATE TABLE employees (
    salary DECIMAL(10,2) CHECK (salary > 0)
);

-- Complex check constraint
CREATE TABLE products (
    price DECIMAL(10,2),
    discount_price DECIMAL(10,2),
    CHECK (discount_price < price)
);
```

---

## 🔹 Functions

### String Functions

#### LENGTH()
```sql
SELECT name, LENGTH(name) as name_length FROM customers;
```

#### TRIM()
```sql
SELECT TRIM('  hello world  '); -- Returns 'hello world'
SELECT TRIM(BOTH 'x' FROM 'xxxhelloxxx'); -- Returns 'hello'
```

#### UPPER() / LOWER()
```sql
SELECT UPPER(first_name), LOWER(last_name) FROM employees;
```

#### CONCAT()
```sql
SELECT CONCAT(first_name, ' ', last_name) as full_name FROM employees;
-- PostgreSQL also supports: first_name || ' ' || last_name
```

### Date/Time Functions

#### NOW()
```sql
SELECT NOW(); -- Current timestamp
INSERT INTO logs (created_at) VALUES (NOW());
```

#### DATE_PART() / EXTRACT()
```sql
SELECT EXTRACT(YEAR FROM birth_date) as birth_year FROM employees;
SELECT DATE_PART('month', order_date) as order_month FROM orders;
```

### Mathematical Functions

#### ROUND()
```sql
SELECT product_name, ROUND(price, 2) FROM products;
SELECT ROUND(AVG(salary)) as avg_salary FROM employees;
```

#### ABS()
```sql
SELECT ABS(-42); -- Returns 42
SELECT ABS(budget - actual) as variance FROM projects;
```

### Aggregate Functions

#### COUNT()
```sql
SELECT COUNT(*) FROM employees; -- All rows
SELECT COUNT(email) FROM customers; -- Non-NULL emails
SELECT COUNT(DISTINCT department) FROM employees; -- Unique departments
```

#### SUM() / AVG()
```sql
SELECT department, SUM(salary), AVG(salary) 
FROM employees 
GROUP BY department;
```

### Advanced Functions

#### COALESCE / NULLIF
**Purpose**: Handle NULL values elegantly

```sql
-- COALESCE for default values
SELECT name, COALESCE(phone, email, 'No contact') as contact
FROM customers;

-- NULLIF to convert empty strings to NULL
SELECT NULLIF(TRIM(description), '') as clean_description
FROM products;
```

---

## 🔹 Window Functions

### ROW_NUMBER()
**Purpose**: Assigns sequential numbers to rows
```sql
SELECT name, salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) as salary_rank
FROM employees;
```

### RANK() / DENSE_RANK()
**Purpose**: Assigns ranks with gaps (RANK) or without gaps (DENSE_RANK)
```sql
SELECT name, salary,
    RANK() OVER (ORDER BY salary DESC) as rank,
    DENSE_RANK() OVER (ORDER BY salary DESC) as dense_rank
FROM employees;
```

### LEAD() / LAG()
**Purpose**: Access values from following (LEAD) or preceding (LAG) rows
```sql
SELECT order_date, total,
    LAG(total) OVER (ORDER BY order_date) as previous_total,
    LEAD(total) OVER (ORDER BY order_date) as next_total
FROM orders;
```

### PARTITION BY
**Purpose**: Divides result set into partitions for window functions
```sql
SELECT name, department, salary,
    AVG(salary) OVER (PARTITION BY department) as dept_avg_salary
FROM employees;
```

---

## 🔹 Transaction Control

### BEGIN / COMMIT / ROLLBACK
**Purpose**: Control transaction boundaries
```sql
-- Transaction example
BEGIN;
    UPDATE accounts SET balance = balance - 100 WHERE id = 1;
    UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;

-- With error handling
BEGIN;
    INSERT INTO orders (customer_id, total) VALUES (1, 500);
    -- If error occurs:
ROLLBACK;
```

### SAVEPOINT
**Purpose**: Create intermediate transaction points
```sql
BEGIN;
    INSERT INTO table1 VALUES (1, 'data');
    SAVEPOINT sp1;
    INSERT INTO table2 VALUES (2, 'more data');
    -- If needed:
    ROLLBACK TO sp1; -- Only rollback to savepoint
COMMIT;
```

---

## 🔹 Views and Performance

### CREATE VIEW
**Purpose**: Create virtual tables based on queries  
**Properties**:
- Stored query definition, not data
- Always shows current data
- Can simplify complex queries

```sql
-- Simple view
CREATE VIEW active_employees AS
SELECT id, name, department, salary
FROM employees
WHERE status = 'active';

-- Complex view with joins
CREATE VIEW employee_details AS
SELECT 
    e.name,
    d.department_name,
    p.project_name,
    e.salary
FROM employees e
JOIN departments d ON e.dept_id = d.id
LEFT JOIN projects p ON e.project_id = p.id;
```

### CREATE MATERIALIZED VIEW
**Purpose**: Pre-computed and stored view results  
**Properties**:
- Stores actual data (not just query)
- Better performance for complex queries
- Needs periodic refresh

```sql
-- Create materialized view
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT 
    DATE_TRUNC('month', order_date) as month,
    COUNT(*) as order_count,
    SUM(total) as total_sales
FROM orders
GROUP BY DATE_TRUNC('month', order_date);

-- Refresh materialized view
REFRESH MATERIALIZED VIEW monthly_sales;
```

### EXPLAIN / EXPLAIN ANALYZE
**Purpose**: Show query execution plans  
**Properties**:
- EXPLAIN shows planned execution
- EXPLAIN ANALYZE shows actual execution
- Helps optimize query performance

```sql
-- Show execution plan
EXPLAIN SELECT * FROM employees WHERE department = 'Sales';

-- Show actual execution statistics
EXPLAIN ANALYZE 
SELECT e.name, d.department_name
FROM employees e
JOIN departments d ON e.dept_id = d.id
WHERE e.salary > 50000;
```

---

## 🔹 PL/pgSQL Special Variables

### FOUND
**Purpose**: Boolean flag indicating if previous SQL statement affected rows
```sql
CREATE OR REPLACE FUNCTION update_employee_salary(emp_id INTEGER, new_salary NUMERIC)
RETURNS TEXT AS $$
DECLARE
    result TEXT;
BEGIN
    UPDATE employees SET salary = new_salary WHERE id = emp_id;
    
    IF FOUND THEN
        result := 'Employee salary updated successfully';
    ELSE
        result := 'Employee not found';
    END IF;
    
    RETURN result;
END;
$$ LANGUAGE plpgsql;
```

### ROW_COUNT
**Purpose**: Number of rows affected by last SQL statement
```sql
CREATE OR REPLACE FUNCTION bulk_update_salaries(dept_name TEXT, increase_pct NUMERIC)
RETURNS INTEGER AS $$
DECLARE
    affected_rows INTEGER;
BEGIN
    UPDATE employees 
    SET salary = salary * (1 + increase_pct / 100)
    WHERE department = dept_name;
    
    GET DIAGNOSTICS affected_rows = ROW_COUNT;
    
    RETURN affected_rows;
END;
$$ LANGUAGE plpgsql;
```

### Trigger Variables (TG_*)
**Purpose**: Special variables available in trigger functions
```sql
CREATE OR REPLACE FUNCTION audit_trigger()
RETURNS trigger AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        INSERT INTO audit_log (table_name, operation, new_data, timestamp)
        VALUES (TG_TABLE_NAME, TG_OP, row_to_json(NEW), NOW());
        RETURN NEW;
    ELSIF TG_OP = 'UPDATE' THEN
        INSERT INTO audit_log (table_name, operation, old_data, new_data, timestamp)
        VALUES (TG_TABLE_NAME, TG_OP, row_to_json(OLD), row_to_json(NEW), NOW());
        RETURN NEW;
    END IF;
END;
$$ LANGUAGE plpgsql;
```

---

## 🔹 Environment Variables

### PostgreSQL Environment Variables
**Purpose**: Configure client connection and behavior

```bash
# Connection variables
export PGHOST=localhost
export PGPORT=5432
export PGUSER=myuser
export PGPASSWORD=mypassword
export PGDATABASE=mydb

# SSL configuration
export PGSSLMODE=require
export PGSSLCERT=/path/to/client-cert.pem

# Now psql uses these settings automatically
psql
```

---

## 🔹 System Catalogs & Information Schema

### pg_stat_activity
**Purpose**: Current database activity and connections
```sql
-- Current active connections
SELECT 
    pid,
    usename,
    application_name,
    client_addr,
    state,
    query_start,
    LEFT(query, 50) as current_query
FROM pg_stat_activity 
WHERE state = 'active' AND pid <> pg_backend_pid();

-- Long-running queries
SELECT 
    pid,
    now() - query_start as duration,
    usename,
    LEFT(query, 100) as query
FROM pg_stat_activity 
WHERE state = 'active' 
    AND now() - query_start > interval '5 minutes';
```

### Information Schema (Standard SQL)
```sql
-- Table information
SELECT 
    table_schema,
    table_name,
    table_type
FROM information_schema.tables 
WHERE table_schema = 'public';

-- Column information
SELECT 
    column_name,
    data_type,
    is_nullable,
    column_default
FROM information_schema.columns 
WHERE table_name = 'employees'
ORDER BY ordinal_position;
```

---

## 🔹 Advanced Data Types

### ENUM Types
**Purpose**: Create custom enumerated types
```sql
-- Create enum type
CREATE TYPE status_type AS ENUM ('pending', 'active', 'inactive', 'deleted');

-- Use in table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    status status_type DEFAULT 'pending'
);

-- Query with enum
SELECT * FROM users WHERE status = 'active';
```

### Array Types
**Purpose**: Store multiple values in single column
```sql
-- Create table with array columns
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    title VARCHAR(200),
    tags TEXT[],
    ratings INTEGER[]
);

-- Insert array data
INSERT INTO articles (title, tags, ratings) VALUES
('PostgreSQL Guide', ARRAY['database', 'sql', 'postgresql'], ARRAY[5, 4, 5, 4]);

-- Array queries
SELECT * FROM articles WHERE 'postgresql' = ANY(tags);
SELECT * FROM articles WHERE tags && ARRAY['database', 'web'];
```

### UUID Type
**Purpose**: Store universally unique identifiers
```sql
-- Enable UUID extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Create table with UUID
CREATE TABLE sessions (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id INTEGER,
    created_at TIMESTAMP DEFAULT NOW()
);
```

---

## 🔹 Text Search and Full-Text Search

### TSVECTOR and TSQUERY
**Purpose**: Full-text search capabilities
```sql
-- Create table with text search
CREATE TABLE documents (
    id SERIAL PRIMARY KEY,
    title VARCHAR(200),
    content TEXT,
    search_vector tsvector
);

-- Update search vector
UPDATE documents SET search_vector = 
    to_tsvector('english', title || ' ' || content);

-- Create index for fast searching
CREATE INDEX idx_documents_search ON documents USING gin(search_vector);

-- Full-text search
SELECT *, ts_rank(search_vector, query) as rank
FROM documents, to_tsquery('english', 'postgresql & database') query
WHERE search_vector @@ query
ORDER BY rank DESC;
```

### Regular Expressions
**Purpose**: Advanced pattern matching
```sql
-- Basic regex matching
SELECT * FROM products WHERE code ~ '^[A-Z]{2}[0-9]{4}$';

-- Case-insensitive matching
SELECT * FROM customers WHERE name ~* '^john.*smith$';

-- Extract patterns
SELECT 
    email,
    substring(email from '([^@]+)@(.+)') as username
FROM users;
```

---

## 🔹 Stored Procedures and Functions

### Function Types
```sql
-- Simple SQL function
CREATE OR REPLACE FUNCTION get_employee_count(dept_name TEXT)
RETURNS INTEGER AS $$
    SELECT COUNT(*) FROM employees WHERE department = dept_name;
$$ LANGUAGE SQL IMMUTABLE;

-- PL/pgSQL function with logic
CREATE OR REPLACE FUNCTION calculate_bonus(emp_id INTEGER)
RETURNS NUMERIC AS $$
DECLARE
    emp_salary NUMERIC;
    years_service INTEGER;
    bonus_amount NUMERIC := 0;
BEGIN
    SELECT salary, EXTRACT(YEAR FROM AGE(NOW(), hire_date))
    INTO emp_salary, years_service
    FROM employees WHERE id = emp_id;
    
    IF years_service >= 10 THEN
        bonus_amount := emp_salary * 0.15;
    ELSIF years_service >= 5 THEN
        bonus_amount := emp_salary * 0.10;
    END IF;
    
    RETURN bonus_amount;
END;
$$ LANGUAGE plpgsql;
```

---

## 🔹 Triggers and Rules

### Triggers
**Purpose**: Automatically execute code in response to events
```sql
-- Audit trigger function
CREATE OR REPLACE FUNCTION employee_audit_func()
RETURNS trigger AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        INSERT INTO employee_audit (action, employee_id, new_data, timestamp)
        VALUES ('INSERT', NEW.id, row_to_json(NEW), NOW());
        RETURN NEW;
    ELSIF TG_OP = 'UPDATE' THEN
        INSERT INTO employee_audit (action, employee_id, old_data, new_data, timestamp)
        VALUES ('UPDATE', NEW.id, row_to_json(OLD), row_to_json(NEW), NOW());
        RETURN NEW;
    END IF;
END;
$$ LANGUAGE plpgsql;

-- Create triggers
CREATE TRIGGER employee_audit_trigger
    AFTER INSERT OR UPDATE OR DELETE ON employees
    FOR EACH ROW EXECUTE FUNCTION employee_audit_func();
```

---

## 🔹 Security and Permissions

### Roles and Users
```sql
-- Create roles
CREATE ROLE app_user LOGIN PASSWORD 'secure_password';
CREATE ROLE read_only_user;

-- Grant permissions
GRANT SELECT ON employees TO read_only_user;
GRANT SELECT, INSERT, UPDATE ON customers TO app_user;

-- Role inheritance
CREATE ROLE reporting_team;
GRANT read_only_user TO reporting_team;
```

### Row Level Security (RLS)
```sql
-- Enable RLS on table
ALTER TABLE employees ENABLE ROW LEVEL SECURITY;

-- Create policies
CREATE POLICY employee_policy ON employees
    FOR ALL TO app_user
    USING (department = current_setting('app.user_department'));
```

---

## 🔹 Advanced Query Techniques

### Common Table Expressions (CTE)
```sql
WITH department_stats AS (
    SELECT department, COUNT(*) as emp_count, AVG(salary) as avg_salary
    FROM employees
    GROUP BY department
)
SELECT * FROM department_stats WHERE emp_count > 5;
```

### Recursive CTE
```sql
WITH RECURSIVE employee_hierarchy AS (
    -- Base case: top-level managers
    SELECT id, name, manager_id, 1 as level
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case: employees with managers
    SELECT e.id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.id
)
SELECT * FROM employee_hierarchy ORDER BY level, name;
```

### Pivot Operations (Simulated)
```sql
-- Pivot sales data by quarter
SELECT 
    product_name,
    SUM(CASE WHEN quarter = 'Q1' THEN sales ELSE 0 END) as q1_sales,
    SUM(CASE WHEN quarter = 'Q2' THEN sales ELSE 0 END) as q2_sales,
    SUM(CASE WHEN quarter = 'Q3' THEN sales ELSE 0 END) as q3_sales,
    SUM(CASE WHEN quarter = 'Q4' THEN sales ELSE 0 END) as q4_sales
FROM quarterly_sales
GROUP BY product_name;
```

---

## 🔹 Keywords Relationships Summary

### Query Execution Order
```sql
-- SQL Execution Order
1. FROM / JOIN       -- Source tables and relationships
2. WHERE            -- Row filtering
3. GROUP BY         -- Row grouping
4. HAVING           -- Group filtering
5. SELECT           -- Column selection and calculations
6. DISTINCT         -- Remove duplicates
7. ORDER BY         -- Sort results
8. LIMIT/OFFSET     -- Limit results
```

### Logical Operator Precedence
```sql
1. Parentheses ()   -- Highest precedence
2. NOT              -- Negation
3. AND              -- Logical AND
4. OR               -- Logical OR (lowest precedence)
```

### Common Pattern Relationships
```sql
-- Data Query Pattern
SELECT columns FROM tables WHERE conditions;

-- Aggregation Pattern  
SELECT columns, AGG_FUNC(column) FROM tables 
GROUP BY columns HAVING conditions;

-- Join Pattern
SELECT columns FROM table1 alias1
JOIN table2 alias2 ON join_condition
WHERE filter_conditions;

-- Subquery Pattern
SELECT columns FROM table1 
WHERE column IN (SELECT column FROM table2 WHERE conditions);

-- Window Function Pattern
SELECT columns, 
       WINDOW_FUNC() OVER (PARTITION BY columns ORDER BY columns)
FROM tables;
```

### Transaction Control Flow
```sql
BEGIN;              -- Start transaction
  SQL operations;   -- INSERT, UPDATE, DELETE
  SAVEPOINT name;   -- Optional checkpoint
  More operations;  -- Additional work
COMMIT;             -- Make changes permanent
-- OR --
ROLLBACK;           -- Undo all changes
-- OR --
ROLLBACK TO name;   -- Undo to savepoint
```

### Performance Optimization Patterns
```sql
-- Index Usage Pattern
CREATE INDEX idx_table_columns ON table_name (column1, column2);
EXPLAIN ANALYZE SELECT * FROM table_name WHERE column1 = 'value';

-- Materialized View Pattern
CREATE MATERIALIZED VIEW summary_view AS
SELECT department, COUNT(*), AVG(salary) FROM employees GROUP BY department;
REFRESH MATERIALIZED VIEW summary_view;

-- Partition Pattern (PostgreSQL 10+)
CREATE TABLE sales (
    id SERIAL,
    sale_date DATE,
    amount DECIMAL
) PARTITION BY RANGE (sale_date);

CREATE TABLE sales_2024 PARTITION OF sales
FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');
```

---

## 📊 Quick Reference Tables

### SQL Keywords by Category

| Category | Keywords |
|----------|----------|
| **Core DML** | SELECT, INSERT, UPDATE, DELETE, FROM, WHERE |
| **Logical Operators** | AND, OR, NOT, IN, EXISTS, BETWEEN, LIKE |
| **Joins** | JOIN, LEFT JOIN, RIGHT JOIN, FULL JOIN, CROSS JOIN |
| **Aggregation** | GROUP BY, HAVING, COUNT, SUM, AVG, MIN, MAX |
| **Sorting & Limiting** | ORDER BY, LIMIT, OFFSET, FETCH, DISTINCT |
| **Set Operations** | UNION, UNION ALL, INTERSECT, EXCEPT |
| **Conditional** | CASE, WHEN, THEN, ELSE, END, COALESCE, NULLIF |
| **DDL** | CREATE, ALTER, DROP, TABLE, INDEX, VIEW |
| **Constraints** | PRIMARY KEY, FOREIGN KEY, CHECK, UNIQUE, NOT NULL |
| **Transactions** | BEGIN, COMMIT, ROLLBACK, SAVEPOINT |
| **Window Functions** | ROW_NUMBER, RANK, DENSE_RANK, LEAD, LAG, OVER |

### Data Types by Database

| PostgreSQL | MySQL | SQL Server | Oracle |
|------------|-------|------------|---------|
| SERIAL | AUTO_INCREMENT | IDENTITY | SEQUENCE |
| TEXT | TEXT | NVARCHAR(MAX) | CLOB |
| BOOLEAN | BOOLEAN | BIT | NUMBER(1) |
| TIMESTAMP | DATETIME | DATETIME2 | TIMESTAMP |
| JSONB | JSON | NVARCHAR(MAX) | JSON |
| ARRAY[] | JSON | - | VARRAY |
| UUID | CHAR(36) | UNIQUEIDENTIFIER | RAW(16) |

### Function Categories

| Category | PostgreSQL | MySQL | SQL Server |
|----------|------------|-------|------------|
| **String** | CONCAT, LENGTH, TRIM, UPPER, LOWER | CONCAT, CHAR_LENGTH, TRIM, UPPER, LOWER | CONCAT, LEN, LTRIM/RTRIM, UPPER, LOWER |
| **Date** | NOW(), DATE_PART, AGE | NOW(), DATE_FORMAT, DATEDIFF | GETDATE(), DATEPART, DATEDIFF |
| **Math** | ROUND, CEIL, FLOOR, ABS | ROUND, CEIL, FLOOR, ABS | ROUND, CEILING, FLOOR, ABS |
| **Aggregate** | COUNT, SUM, AVG, STRING_AGG | COUNT, SUM, AVG, GROUP_CONCAT | COUNT, SUM, AVG, STRING_AGG |

---

## 🚀 Best Practices

### Query Optimization
```sql
-- ✅ Good: Use specific columns
SELECT id, name FROM employees WHERE department = 'Sales';

-- ❌ Bad: Select all columns when not needed
SELECT * FROM employees WHERE department = 'Sales';

-- ✅ Good: Use indexes effectively
CREATE INDEX idx_emp_dept ON employees(department);
SELECT * FROM employees WHERE department = 'Sales';

-- ✅ Good: Use EXISTS instead of IN for large datasets
SELECT * FROM customers c 
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id);

-- ❌ Bad: Use IN with large subqueries
SELECT * FROM customers 
WHERE id IN (SELECT customer_id FROM orders);
```

### Security Best Practices
```sql
-- ✅ Good: Use parameterized queries (application code)
-- PREPARE stmt FROM 'SELECT * FROM users WHERE id = ?';

-- ✅ Good: Grant minimal necessary permissions
GRANT SELECT ON employees TO read_only_user;

-- ✅ Good: Use Row Level Security
ALTER TABLE sensitive_data ENABLE ROW LEVEL SECURITY;
CREATE POLICY user_policy ON sensitive_data 
USING (user_id = current_setting('app.current_user_id')::INTEGER);

-- ❌ Bad: Using string concatenation for queries (SQL injection risk)
-- SELECT * FROM users WHERE name = '" + user_input + "'";
```

### Performance Monitoring
```sql
-- Monitor query performance
EXPLAIN ANALYZE SELECT * FROM large_table WHERE indexed_column = 'value';

-- Check index usage
SELECT schemaname, tablename, indexname, idx_tup_read, idx_tup_fetch
FROM pg_stat_user_indexes
ORDER BY idx_tup_read DESC;

-- Monitor slow queries
SELECT query, mean_exec_time, calls, total_exec_time
FROM pg_stat_statements
WHERE mean_exec_time > 1000
ORDER BY mean_exec_time DESC;
```

---

## 🔧 Common Patterns and Examples

### Data Analysis Patterns
```sql
-- Running totals
SELECT 
    order_date,
    daily_sales,
    SUM(daily_sales) OVER (ORDER BY order_date) as running_total
FROM daily_sales_summary;

-- Top N per group
SELECT *
FROM (
    SELECT 
        product_name,
        category,
        sales,
        ROW_NUMBER() OVER (PARTITION BY category ORDER BY sales DESC) as rn
    FROM product_sales
) ranked
WHERE rn <= 3;

-- Moving averages
SELECT 
    date,
    value,
    AVG(value) OVER (
        ORDER BY date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) as seven_day_avg
FROM metrics;
```

### Data Cleaning Patterns
```sql
-- Remove duplicates
DELETE FROM employees e1
WHERE EXISTS (
    SELECT 1 FROM employees e2
    WHERE e2.email = e1.email 
    AND e2.id > e1.id
);

-- Update with COALESCE for NULL handling
UPDATE customers 
SET phone = COALESCE(NULLIF(TRIM(phone), ''), mobile_phone, 'No phone');

-- Standardize data format
UPDATE addresses 
SET state = UPPER(TRIM(state))
WHERE LENGTH(TRIM(state)) = 2;
```

### ETL (Extract, Transform, Load) Patterns
```sql
-- Bulk insert with conflict resolution
INSERT INTO target_table (id, name, updated_at)
SELECT id, name, NOW()
FROM source_table
ON CONFLICT (id) 
DO UPDATE SET 
    name = EXCLUDED.name,
    updated_at = EXCLUDED.updated_at;

-- Data transformation with CTE
WITH cleaned_data AS (
    SELECT 
        id,
        TRIM(UPPER(name)) as name,
        CASE 
            WHEN age < 0 THEN NULL 
            WHEN age > 150 THEN NULL 
            ELSE age 
        END as age
    FROM raw_data
    WHERE name IS NOT NULL
)
INSERT INTO processed_data 
SELECT * FROM cleaned_data;
```

---

## 📚 Learning Resources

### Official Documentation
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)

---

## 🤝 Contributing

This guide is a living document. Contributions are welcome!

### How to Contribute
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-examples`)
3. Add your examples or improvements
4. Commit your changes (`git commit -am 'Add new SQL examples'`)
5. Push to the branch (`git push origin feature/new-examples`)
6. Create a Pull Request

### Contribution Guidelines
- Provide clear, working examples
- Include both PostgreSQL and MySQL variations when applicable
- Add explanations for complex queries
- Follow the existing formatting style
- Test all code examples before submitting

---

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- PostgreSQL Global Development Group
- MySQL AB/Oracle Corporation
- Microsoft SQL Server Team
- The SQL standards committee (ISO/IEC)
- All contributors to open-source database projects

---

## 📞 Support

If you have questions or need help:

- Open an issue on GitHub
- Check the [Discussions](https://github.com/knand4930/postgresql-interview-question/discussions) section
- Consult the official database documentation
- Join database-specific communities (Reddit, Stack Overflow, Discord)

---

**Made with ❤️ by the Database Community**

*Last updated: 2025*
