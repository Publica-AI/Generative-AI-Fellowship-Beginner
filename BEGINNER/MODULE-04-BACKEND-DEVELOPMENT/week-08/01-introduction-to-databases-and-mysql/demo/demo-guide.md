# Demo Guide - Introduction to Databases & MySQL
**Module 4, Topic 1 | Estimated duration: 35–40 minutes**

---

## What This Demo Teaches

- Connecting to MySQL from the terminal
- Creating a database and table with correct MySQL syntax
- Inserting Nigerian customer records
- Querying with SELECT, WHERE, ORDER BY, LIMIT
- Updating records safely with WHERE
- Deleting records safely with WHERE

---

## Setup — Before the Demo Starts

1. MySQL must be installed — run `mysql --version` to confirm
2. Start MySQL: `mysql -u root -p` (enter your root password)
3. Terminal is open and connected to MySQL

> **Tell students:** "This demo runs entirely inside the MySQL command line — no Python, no FastAPI. We are talking directly to the database using SQL. This is how backend developers and DBAs diagnose and manage data in production."

---

## Demo Steps

### Part 1 — Connect and Create Database (5 min)

**Connect to MySQL:**
```bash
mysql -u root -p
```

**Useful MySQL commands:**
```sql
SHOW DATABASES;          -- list all databases
USE naija_demo;          -- switch to a database
SHOW TABLES;             -- list tables in current database
EXIT;                    -- quit MySQL
```

**Create and select the demo database:**
```sql
CREATE DATABASE naija_demo;
USE naija_demo;
```

> "MySQL is running. We created a new database called naija_demo and switched into it. Now we create our first table."

---

### Part 2 — Create the Customers Table (5 min)

```sql
CREATE TABLE customers (
    id         INT AUTO_INCREMENT PRIMARY KEY,
    name       VARCHAR(100) NOT NULL,
    email      VARCHAR(255) UNIQUE NOT NULL,
    phone      VARCHAR(20),
    city       VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Confirm it was created:**
```sql
SHOW TABLES;
DESCRIBE customers;
```

> "AUTO_INCREMENT means MySQL assigns the id automatically. NOT NULL means required. UNIQUE on email means no two customers can have the same one. DEFAULT CURRENT_TIMESTAMP records when the row was created."

---

### Part 3 — Insert Nigerian Customers (5 min)

```sql
INSERT INTO customers (name, email, phone, city)
VALUES ('Amara Nwosu', 'amara@email.com', '08012345678', 'Lagos');

INSERT INTO customers (name, email, phone, city)
VALUES ('Chidi Okafor', 'chidi@email.com', '08023456789', 'Abuja');

INSERT INTO customers (name, email, phone, city)
VALUES ('Fatima Bello', 'fatima@email.com', '08034567890', 'Kano');
```

> "We do not specify id or created_at — MySQL fills those in automatically."

---

### Part 4 — SELECT Queries (8 min)

```sql
-- Get all records
SELECT * FROM customers;
```
> "Three rows — our three Nigerian customers. id=1, 2, 3 assigned automatically."

```sql
-- Specific columns only
SELECT name, email FROM customers;

-- Filter by city
SELECT * FROM customers WHERE city = 'Lagos';

-- Sort alphabetically
SELECT * FROM customers ORDER BY name ASC;

-- Most recent first, limit to 2
SELECT * FROM customers ORDER BY created_at DESC LIMIT 2;
```

> "ORDER BY + LIMIT is how you build 'show the 10 latest registrations' features — used in every admin dashboard."

---

### Part 5 — UPDATE Safely (5 min)

**First — verify what we are updating:**
```sql
SELECT * FROM customers WHERE id = 2;
```

> "This is Chidi. Now update his city."

```sql
UPDATE customers SET city = 'Port Harcourt' WHERE id = 2;
SELECT * FROM customers WHERE id = 2;
```

> "Always SELECT first, then UPDATE. Now show the danger."

```sql
-- DO NOT RUN — shown as a warning only
-- UPDATE customers SET city = 'Port Harcourt';
-- This would update ALL customers
```

> "Without WHERE, every customer's city becomes Port Harcourt. I am not running this — just showing you why WHERE is non-negotiable."

---

### Part 6 — DELETE Safely (5 min)

**First — verify what we are deleting:**
```sql
SELECT * FROM customers WHERE id = 3;
```

> "Fatima Bello. Confirm this is the right record."

```sql
DELETE FROM customers WHERE id = 3;
SELECT * FROM customers;
```

> "Two rows remain. Fatima is gone — permanently. No undo."

**Try to delete her again:**
```sql
DELETE FROM customers WHERE id = 3;
```

> "Query OK, 0 rows affected. Nothing was deleted — she no longer exists. The operation succeeded but affected nothing."

---

### Part 7 — Confirm Persistence (2 min)

```sql
EXIT;
```

```bash
mysql -u root -p
```

```sql
USE naija_demo;
SELECT * FROM customers;
```

> "We disconnected and reconnected. The data is still there. That is the fundamental difference from a Python list."

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `mysql: command not found` | "MySQL is not in your PATH. On Windows, find the mysql.exe in the MySQL installation folder (usually C:\Program Files\MySQL\MySQL Server 8.x\bin) and add it to PATH, or use MySQL Workbench instead." |
| `ERROR 1045 (28000): Access denied` | "Check your root password. If you do not remember it, on Windows you can reset it through MySQL Installer." |
| `ERROR 1062 (23000): Duplicate entry for key 'email'` | "You are inserting a customer with an email that already exists. Use a different email or run `DELETE FROM customers;` to reset the table." |
| `ERROR 1064: syntax error` | "MySQL strings use single quotes. Check for any double quotes around string values." |
| `SHOW TABLES` returns empty | "Run `USE naija_demo;` first to select the database before listing tables." |

---

## Up Next

Topic 2 — MySQL Setup & Database Design. We install MySQL properly, design the full naija_backend schema using an ERD, and create all tables for our Nigerian backend application.
