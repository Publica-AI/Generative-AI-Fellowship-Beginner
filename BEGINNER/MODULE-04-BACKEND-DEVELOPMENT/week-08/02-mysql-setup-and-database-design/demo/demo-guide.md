# Demo Guide - MySQL Setup & Database Design
**Module 4, Topic 2 | Estimated duration: 35–40 minutes**

---

## What This Demo Teaches

- Creating a MySQL database with the correct character set
- Designing a 4-table Nigerian backend schema
- Creating tables with foreign keys and constraints
- Verifying the schema with DESCRIBE and SHOW TABLES
- Testing foreign key enforcement
- Using ALTER TABLE to modify an existing table

---

## Setup — Before the Demo Starts

1. MySQL must be installed and running
2. Connect: `mysql -u root -p`
3. Whiteboard or diagram tool ready for drawing the ERD

> **Draw the ERD on the board before writing any SQL:**
> users → orders → order_items → products

---

## Demo Steps

### Part 1 — Create the Database (3 min)

```sql
CREATE DATABASE naija_backend CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE naija_backend;
SHOW DATABASES;
```

> "utf8mb4 supports all Unicode characters — including Nigerian language characters. Always use this character set."

---

### Part 2 — Create the users Table (5 min)

```sql
CREATE TABLE users (
    id            INT AUTO_INCREMENT PRIMARY KEY,
    name          VARCHAR(100) NOT NULL,
    email         VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role          VARCHAR(20) DEFAULT 'user',
    created_at    TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

DESCRIBE users;
```

> "password_hash — never plain text. role defaults to 'user' — we will add admin role in Topic 8."

---

### Part 3 — Create the products Table (5 min)

```sql
CREATE TABLE products (
    id          INT AUTO_INCREMENT PRIMARY KEY,
    name        VARCHAR(150) NOT NULL,
    description TEXT,
    price       DECIMAL(10, 2) NOT NULL,
    category    VARCHAR(100),
    stock       INT DEFAULT 0,
    created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Insert sample data:**
```sql
INSERT INTO products (name, price, category, stock)
VALUES
    ('Indomie Noodles (Pack of 40)', 5500.00, 'food', 200),
    ('MTN 10GB Data Bundle', 3000.00, 'telecoms', 999),
    ('Dangote Cement 50kg', 7500.00, 'construction', 500);

SELECT * FROM products;
```

> "DECIMAL(10,2) for money — never FLOAT. Three Nigerian products inserted."

---

### Part 4 — Create the orders Table (5 min)

```sql
CREATE TABLE orders (
    id           INT AUTO_INCREMENT PRIMARY KEY,
    user_id      INT NOT NULL,
    total_amount DECIMAL(10, 2) NOT NULL,
    status       VARCHAR(20) DEFAULT 'pending',
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**Test the foreign key constraint — try to insert an order for a user that does not exist:**
```sql
INSERT INTO orders (user_id, total_amount) VALUES (999, 5000.00);
```

**Expected error:**
```
ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails
```

> "MySQL rejected this. user_id=999 does not exist in the users table. This is referential integrity — the database protects itself."

**Now insert a valid user and then a valid order:**
```sql
INSERT INTO users (name, email, password_hash)
VALUES ('Amara Nwosu', 'amara@email.com', 'hashed_placeholder');

INSERT INTO orders (user_id, total_amount)
VALUES (1, 8500.00);
```

---

### Part 5 — Create the order_items Table (5 min)

```sql
CREATE TABLE order_items (
    id          INT AUTO_INCREMENT PRIMARY KEY,
    order_id    INT NOT NULL,
    product_id  INT NOT NULL,
    quantity    INT NOT NULL DEFAULT 1,
    unit_price  DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (order_id)   REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);
```

**Insert a sample order item:**
```sql
INSERT INTO order_items (order_id, product_id, quantity, unit_price)
VALUES (1, 1, 2, 5500.00);

SELECT * FROM order_items;
```

> "unit_price is stored at purchase time — if the product price changes later, the order history stays accurate."

---

### Part 6 — Verify the Full Schema (5 min)

```sql
SHOW TABLES;

DESCRIBE users;
DESCRIBE products;
DESCRIBE orders;
DESCRIBE order_items;
```

> "Four tables. All columns, types, and constraints are exactly as designed. Verify before building on top of this."

---

### Part 7 — ALTER TABLE Demo (5 min)

```sql
-- Add a phone column to users
ALTER TABLE users ADD COLUMN phone VARCHAR(20) AFTER email;

DESCRIBE users;

-- Remove it
ALTER TABLE users DROP COLUMN phone;

DESCRIBE users;
```

> "ALTER TABLE lets you change the schema without recreating the table and losing data. In production, always test on a development copy first."

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `ERROR 1005: Can't create table (errno: 150)` on foreign key | "The referenced table must exist first. Create users before orders, and orders before order_items." |
| `ERROR 1452` when inserting with foreign key | "The referenced id does not exist in the parent table. Insert the parent record first." |
| `DESCRIBE` shows wrong column type | "Drop the table and recreate it — it is faster than trying to alter multiple columns." |
| `ERROR 1064` syntax error on CREATE TABLE | "Check for missing commas between column definitions, and that the FOREIGN KEY line has no comma after the last one." |

---

## Up Next

Topic 3 — Database Integration with FastAPI. We connect our FastAPI application to the naija_backend MySQL database using SQLAlchemy and PyMySQL.
