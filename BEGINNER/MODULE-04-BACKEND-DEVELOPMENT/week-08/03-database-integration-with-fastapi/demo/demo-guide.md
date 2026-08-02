# Demo Guide - Database Integration with FastAPI
**Module 4, Topic 3 | Estimated duration: 40–45 minutes**

---

## What This Demo Teaches

- Installing SQLAlchemy and PyMySQL
- Creating `database.py` with the engine, session factory, and Base
- Defining a SQLAlchemy model in `models.py`
- Creating the table from the model using `Base.metadata.create_all()`
- Writing the `get_db()` dependency function
- Injecting the session with `Depends(get_db)`
- Reading all records and reading a single record from the database
- Adding a Pydantic `response_model` to convert SQLAlchemy objects to JSON

---

## Setup — Before the Demo Starts

1. MySQL must be running and the `naija_backend` database must exist (from Topic 2)
2. The `products` table must exist and have at least 3 rows of test data
3. Students should have the `naija-backend/` project folder from Week 7 open in VS Code
4. A terminal is open inside VS Code, pointed at `naija-backend/`
5. Run `pip install sqlalchemy pymysql` — confirm no errors before starting

> **Tell students:** "By the end of this demo, your FastAPI app will be reading real data from MySQL. The products you insert in the MySQL terminal will appear in the /docs response. We are connecting the two halves of this week."

---

## Demo Steps

### Part 1 — Install and Test the Connection (5 min)

**In the terminal:**
```bash
pip install sqlalchemy pymysql
```

Verify both installed:
```bash
python -c "import sqlalchemy; print(sqlalchemy.__version__)"
python -c "import pymysql; print(pymysql.__version__)"
```

> "Two packages. SQLAlchemy is the ORM — it translates Python into SQL. PyMySQL is the driver — it opens the actual connection to MySQL."

**Quick connection test — run in Python interactively:**
```python
from sqlalchemy import create_engine
engine = create_engine("mysql+pymysql://root:yourpassword@localhost:3306/naija_backend")
connection = engine.connect()
print("Connected!")
connection.close()
```

> "If this prints 'Connected!' your database URL is correct and MySQL is reachable. If it errors, check your password and that MySQL is running."

---

### Part 2 — Create database.py (8 min)

**Create a new file called `database.py` in the project folder.**

Type it out — do not copy-paste:
```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "mysql+pymysql://root:yourpassword@localhost:3306/naija_backend"

engine = create_engine(DATABASE_URL)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```

> "This file is the foundation. Three objects — engine, SessionLocal, Base. Everything else in the project imports from here."

Walk through each one:
- `engine` — the connection pool. Created once. All requests share it.
- `SessionLocal` — calling `SessionLocal()` gives you a new session. Think of it as the recipe; each session is one freshly made connection.
- `Base` — all your model classes inherit from this. SQLAlchemy uses it to find your tables.

> "Notice I am hardcoding the password for now. In production this would come from .env. For today's demo this is fine — just never commit this to Git."

---

### Part 3 — Create models.py (8 min)

**Create a new file called `models.py`.**

```python
from sqlalchemy import Column, Integer, String, DECIMAL, Text, TIMESTAMP
from sqlalchemy.sql import func
from database import Base

class Product(Base):
    __tablename__ = "products"

    id          = Column(Integer, primary_key=True, index=True)
    name        = Column(String(150), nullable=False)
    description = Column(Text, nullable=True)
    price       = Column(DECIMAL(10, 2), nullable=False)
    category    = Column(String(100), nullable=True)
    stock       = Column(Integer, default=0)
    created_at  = Column(TIMESTAMP, server_default=func.now())
```

> "This Python class maps to the products table in MySQL. Every column in the table becomes an attribute on the class. The `__tablename__` tells SQLAlchemy which table this class represents."

Open MySQL Workbench or the MySQL terminal and show the actual `products` table structure side by side.

> "They match exactly. The class is the Python representation of the table. When we query `Product`, SQLAlchemy queries the products table."

---

### Part 4 — Create the Tables (3 min)

**Add a quick script at the bottom of models.py temporarily, then run it:**
```python
# Temporary — run once to create tables, then remove
if __name__ == "__main__":
    from database import engine
    Base.metadata.create_all(bind=engine)
    print("Tables created.")
```

**In the terminal:**
```bash
python models.py
```

> "Tables created. Since the products table already exists, SQLAlchemy skipped it. If you added a new model class, it would create that new table."

Remove the if-block after running it.

> "In a real project you would call create_all() at startup inside main.py, or use Alembic for migrations. For now, this is enough."

---

### Part 5 — The get_db() Dependency (5 min)

**Open main.py. At the top, add the imports and the get_db function:**
```python
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
from database import SessionLocal
from models import Product

app = FastAPI()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

> "This function is called for every request that needs the database. It opens a session, hands it to the endpoint function, then closes it when the request is done — even if there was an error. The `finally` block guarantees the session is always closed."

**Ask students:** "Why not just create one global db session at the top of main.py?"

> "SQLAlchemy sessions are not thread-safe. If two requests come in at the same time and share one session, they can corrupt each other's data. One session per request is the safe pattern."

---

### Part 6 — Read All Products (5 min)

**Add the first database endpoint:**
```python
@app.get("/products")
def get_all_products(db: Session = Depends(get_db)):
    products = db.query(Product).all()
    return products
```

**Run the server:**
```bash
uvicorn main:app --reload
```

**Open /docs. Test GET /products.**

Students will see an error like: `Object of type Product is not JSON serializable`

> "FastAPI does not know how to convert a SQLAlchemy object to JSON. We need to tell it what the response should look like using a Pydantic schema. Let's fix this."

---

### Part 7 — Add a Pydantic Response Schema (7 min)

**Create schemas.py:**
```python
from pydantic import BaseModel
from typing import Optional

class ProductOut(BaseModel):
    id: int
    name: str
    price: float
    category: Optional[str] = None
    stock: int

    class Config:
        from_attributes = True
```

> "Two things to notice. First, `from_attributes = True` — this tells Pydantic to read from SQLAlchemy object attributes, not just from dicts. Without this, it would still fail. Second, I did not include `description` or `created_at` — response models let you control exactly what you return. Sensitive fields you leave out stay out."

**Update main.py to use the schema:**
```python
from schemas import ProductOut
from typing import List

@app.get("/products", response_model=List[ProductOut])
def get_all_products(db: Session = Depends(get_db)):
    products = db.query(Product).all()
    return products
```

**Refresh /docs. Test GET /products again.**

> "Now it returns clean JSON. And look at /docs — the response schema is documented automatically. FastAPI read our ProductOut class and generated the documentation."

---

### Part 8 — Read One Product (5 min)

**Add the single-product endpoint:**
```python
from fastapi import FastAPI, Depends, HTTPException

@app.get("/products/{product_id}", response_model=ProductOut)
def get_product(product_id: int, db: Session = Depends(get_db)):
    product = db.query(Product).filter(Product.id == product_id).first()
    if product is None:
        raise HTTPException(status_code=404, detail="Product not found")
    return product
```

**Test in /docs with an existing ID (e.g. 1), then with a non-existent ID (e.g. 999).**

> "Two things changed from the previous version. First, I'm using `HTTPException` instead of returning a dict — this is the proper FastAPI way to return an error response. It gives us a real 404 status code with a structured error body. Second, `.filter(Product.id == product_id).first()` — filter adds a WHERE clause, and first() returns one row or None."

Ask students what SQL this generates:

> "It generates: `SELECT * FROM products WHERE id = 1 LIMIT 1`. SQLAlchemy wrote that SQL — we never touched SQL in this endpoint."

---

## Final State of the Project

At the end of the demo, the project should have four files:

**database.py:**
```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "mysql+pymysql://root:yourpassword@localhost:3306/naija_backend"
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()
```

**models.py:**
```python
from sqlalchemy import Column, Integer, String, DECIMAL, Text, TIMESTAMP
from sqlalchemy.sql import func
from database import Base

class Product(Base):
    __tablename__ = "products"
    id          = Column(Integer, primary_key=True, index=True)
    name        = Column(String(150), nullable=False)
    description = Column(Text, nullable=True)
    price       = Column(DECIMAL(10, 2), nullable=False)
    category    = Column(String(100), nullable=True)
    stock       = Column(Integer, default=0)
    created_at  = Column(TIMESTAMP, server_default=func.now())
```

**schemas.py:**
```python
from pydantic import BaseModel
from typing import Optional

class ProductOut(BaseModel):
    id: int
    name: str
    price: float
    category: Optional[str] = None
    stock: int

    class Config:
        from_attributes = True
```

**main.py:**
```python
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session
from typing import List
from database import SessionLocal
from models import Product
from schemas import ProductOut

app = FastAPI()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/products", response_model=List[ProductOut])
def get_all_products(db: Session = Depends(get_db)):
    return db.query(Product).all()

@app.get("/products/{product_id}", response_model=ProductOut)
def get_product(product_id: int, db: Session = Depends(get_db)):
    product = db.query(Product).filter(Product.id == product_id).first()
    if product is None:
        raise HTTPException(status_code=404, detail="Product not found")
    return product
```

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `Access denied for user 'root'` | "Check your password in the DATABASE_URL string. Re-run the connection test from Part 1." |
| `Can't connect to MySQL server` | "MySQL is not running. On Windows: open Services and start MySQL80. On Mac: run `brew services start mysql`." |
| `Object of type Product is not JSON serializable` | "You need `response_model=ProductOut` on the endpoint and `from_attributes = True` in the schema Config. This is the Part 7 fix." |
| `ModuleNotFoundError: No module named 'database'` | "Make sure you are running uvicorn from inside the naija-backend/ folder, not a parent folder." |
| `Table 'products' already exists` error | "That's fine — SQLAlchemy's create_all() will not recreate existing tables. The error only appears if you run CREATE TABLE manually when it already exists." |
| `/products` returns an empty list | "Check that your MySQL products table actually has rows. Run `SELECT * FROM products;` in the MySQL terminal." |

---

## Up Next

Topic 4 — CRUD Operations with a Database. We add POST (create), PUT (update), and DELETE endpoints so the API can write to the database, not just read from it.
