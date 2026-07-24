# Demo Guide - Request & Response Models with Pydantic
**Module 4, Topic 5 | Estimated duration: 35â€“40 minutes**

---

## What This Demo Teaches

- Defining Pydantic models with required and optional fields
- Using a model in a POST endpoint to replace dict
- Seeing automatic 422 validation when invalid data is sent
- Seeing the improved /docs schema with Pydantic
- Using response_model to filter what data is returned
- Adding Field validators for value constraints

---

## Setup - Before the Demo Starts

1. Continue from the `naija-backend/` folder from Topic 4
2. Server should be stopped - `CTRL+C`
3. We will update `main.py` progressively during the demo

---

## Demo Steps

### Part 1 - Show the Problem (5 min)

Start the server from Topic 4 as-is.

**In /docs - POST /products with this invalid body:**
```json
{"gibberish": "hello"}
```

> "Look - our API accepted this. It stored a 'product' with no name, no price, nothing. Status 201 - we told the client it was a success. This is wrong."

**GET /products - show the garbage stored in the list.**

Stop the server.

> "Pydantic fixes this. Let's update main.py."

---

### Part 2 - Define the Models (7 min)

**Update the top of main.py:**
```python
from fastapi import FastAPI, HTTPException, Response
from pydantic import BaseModel, Field
from typing import Optional

app = FastAPI()

class ProductCreate(BaseModel):
    name: str = Field(min_length=2, max_length=100)
    price: float = Field(gt=0)
    category: str
    stock: int = Field(ge=0, default=0)

class ProductUpdate(BaseModel):
    name: Optional[str] = None
    price: Optional[float] = None
    category: Optional[str] = None
    stock: Optional[int] = None

products = [
    {"id": 1, "name": "Indomie Noodles", "price": 150.0, "category": "food", "stock": 500},
    {"id": 2, "name": "MTN Data SIM", "price": 1000.0, "category": "telecoms", "stock": 200},
]
```

> "Two models: ProductCreate for POST requests - all fields required except stock. ProductUpdate for PUT requests - all fields optional so we can do partial updates."

---

### Part 3 - Update the POST Endpoint (5 min)

**Update the create_product function:**
```python
@app.post("/products", status_code=201)
def create_product(product: ProductCreate):
    new_product = product.model_dump()
    new_product["id"] = len(products) + 1
    products.append(new_product)
    return new_product
```

**Start the server and open /docs.**

> "Look at POST /products now. The request body section shows the full schema - name, price, category, stock - with their types and which are required."

---

### Part 4 - Test Validation (8 min)

**Test 1 - Valid request:**
```json
{"name": "Dangote Cement 50kg", "price": 7500, "category": "construction", "stock": 1000}
```
> "201. Works perfectly."

**Test 2 - Wrong type:**
```json
{"name": "Dangote Cement 50kg", "price": "expensive", "category": "construction"}
```
> "422. Read the error message - it tells us exactly: body â†’ price â†’ 'Input should be a valid number'."

**Test 3 - Missing required field:**
```json
{"price": 7500, "category": "construction"}
```
> "422. Missing 'name'. The error tells us exactly which field is missing."

**Test 4 - Invalid value (negative price):**
```json
{"name": "Test", "price": -500, "category": "food"}
```
> "422. Price must be greater than 0."

**Test 5 - Missing stock (should use default):**
```json
{"name": "Test Product", "price": 200, "category": "food"}
```
> "201. Stock was not provided - defaults to 0. The stored product shows stock: 0."

---

### Part 5 - Update the PUT Endpoint (5 min)

**Update the update_product function:**
```python
@app.put("/products/{product_id}")
def update_product(product_id: int, updated: ProductUpdate):
    for i, product in enumerate(products):
        if product["id"] == product_id:
            update_data = updated.model_dump(exclude_none=True)
            products[i].update(update_data)
            return products[i]
    raise HTTPException(status_code=404, detail="Product not found")
```

> "exclude_none=True means only fields the client actually sent are updated. Send just `{\"price\": 8500}` - only the price changes."

**Test - partial update:**
```json
{"price": 8500}
```

> "GET /products/1 - only the price changed. Name, category, stock are untouched. This is much better than the Topic 4 version that replaced the entire product."

---

### Part 6 - Response Model (5 min)

**Add a response model and use it on GET one:**
```python
class ProductResponse(BaseModel):
    id: int
    name: str
    price: float
    category: str
    # stock intentionally excluded

@app.get("/products/{product_id}", response_model=ProductResponse)
def get_product(product_id: int):
    for product in products:
        if product["id"] == product_id:
            return product
    raise HTTPException(status_code=404, detail="Product not found")
```

**Test GET /products/1 - notice stock is not in the response.**

> "The product has a stock field. We chose not to show it to customers. FastAPI filters it out automatically because stock is not in ProductResponse."

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `AttributeError: 'dict' object has no attribute 'model_dump'` | "You are still passing a dict to the function, not a Pydantic model. Check the type annotation is `ProductCreate`, not `dict`." |
| 422 on valid-looking request | "Check your Field validators - price must be gt=0, not gte=0. A price of 0 would fail gt=0." |
| `ImportError: cannot import name 'Field'` | "Change the import to `from pydantic import BaseModel, Field`." |
| Response model not filtering fields | "Check that `response_model=ProductResponse` is in the decorator, not the function body." |

---

## Week 7 Complete

Students now have a complete, validated FastAPI Products API with:
- 6 CRUD endpoints
- Pydantic validation on all input
- Correct status codes
- Response model filtering
- Automatic /docs schema

**Up Next:** Week 8 - Databases + Authentication. We connect to MySQL so data persists, and add user registration and JWT login.

