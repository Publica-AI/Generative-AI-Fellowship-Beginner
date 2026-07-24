# Demo Guide - Building API Endpoints with FastAPI
**Module 4, Topic 4 | Estimated duration: 40â€“45 minutes**

---

## What This Demo Teaches

- Setting up an in-memory data store
- GET all with optional query parameter filtering
- GET one with path parameter and 404 handling
- POST to create a new resource (201 status)
- PUT to update an existing resource
- DELETE to remove a resource (204 status)
- Testing the full CRUD workflow in /docs

---

## Setup - Before the Demo Starts

1. Continue from the `naija-backend/` folder from Topic 3
2. The server should be stopped - `CTRL+C` in the terminal
3. Open `main.py` - clear all content from Topic 3 (we start fresh)
4. Terminal is open in the `naija-backend/` folder

---

## Demo Steps

### Part 1 - Set Up the Data Store (5 min)

**Replace main.py with:**
```python
from fastapi import FastAPI, HTTPException, Response
from typing import Optional

app = FastAPI()

products = [
    {"id": 1, "name": "Indomie Noodles", "price": 150, "category": "food", "stock": 500},
    {"id": 2, "name": "MTN Data SIM", "price": 1000, "category": "telecoms", "stock": 200},
    {"id": 3, "name": "Power Bank 20000mAh", "price": 8500, "category": "electronics", "stock": 50},
]
```

**Start the server:**
```bash
uvicorn main:app --reload
```

> "We start with three Nigerian products in a Python list. This is our temporary database - data is lost when the server restarts. Week 8 replaces this with MySQL."

---

### Part 2 - GET All Products (5 min)

**Add to main.py:**
```python
@app.get("/products")
def get_products(category: Optional[str] = None):
    if category:
        return [p for p in products if p["category"] == category]
    return products
```

**Save. Open /docs. Test:**

1. `GET /products` - no category parameter â†’ returns all 3 products
2. `GET /products?category=food` â†’ returns only Indomie Noodles
3. `GET /products?category=electronics` â†’ returns only Power Bank

> "The `category` parameter is optional - defaults to None. If the caller provides it, we filter. If not, we return everything."

---

### Part 3 - GET One Product (7 min)

**Add to main.py:**
```python
@app.get("/products/{product_id}")
def get_product(product_id: int):
    for product in products:
        if product["id"] == product_id:
            return product
    raise HTTPException(status_code=404, detail=f"Product {product_id} not found")
```

**Save. Test in /docs:**

1. `GET /products/1` â†’ 200 with Indomie Noodles data
2. `GET /products/99` â†’ 404 with `{"detail": "Product 99 not found"}`

> "HTTPException is the FastAPI way to return errors. We raise it - FastAPI catches it and returns the proper HTTP error response. The `detail` becomes the message body."

---

### Part 4 - POST Create a Product (8 min)

**Add to main.py:**
```python
@app.post("/products", status_code=201)
def create_product(product: dict):
    product["id"] = max(p["id"] for p in products) + 1
    products.append(product)
    return product
```

**Save. Test in /docs - click POST /products, Try it out, paste this body:**
```json
{
    "name": "Dangote Cement 50kg",
    "price": 7500,
    "category": "construction",
    "stock": 1000
}
```

> "Status 201 - Created. The response shows the product with its new id=4."

**Now test GET /products - confirm 4 products are there.**

> "The data persists as long as the server is running."

---

### Part 5 - PUT Update a Product (7 min)

**Add to main.py:**
```python
@app.put("/products/{product_id}")
def update_product(product_id: int, updated_data: dict):
    for i, product in enumerate(products):
        if product["id"] == product_id:
            updated_data["id"] = product_id
            products[i] = updated_data
            return products[i]
    raise HTTPException(status_code=404, detail=f"Product {product_id} not found")
```

**Save. Test in /docs:**

First `GET /products/1` - note the current values.

Then `PUT /products/1` with:
```json
{
    "name": "Indomie Noodles Pack of 40",
    "price": 5500,
    "category": "food",
    "stock": 450
}
```

Then `GET /products/1` again - confirm the update.

> "The PUT replaces the entire product. The id is preserved - we always set it from the URL, not from the request body."

---

### Part 6 - DELETE a Product (5 min)

**Add to main.py:**
```python
@app.delete("/products/{product_id}", status_code=204)
def delete_product(product_id: int):
    for i, product in enumerate(products):
        if product["id"] == product_id:
            products.pop(i)
            return Response(status_code=204)
    raise HTTPException(status_code=404, detail=f"Product {product_id} not found")
```

**Save. Test in /docs:**

1. `DELETE /products/2` â†’ 204 with empty body
2. `GET /products` â†’ only 3 products remain (MTN SIM is gone)
3. `DELETE /products/2` again â†’ 404 (already deleted)

> "204 No Content - success with nothing to return. The product is gone, there is nothing to send back."

---

### Part 7 - Full CRUD Test Sequence (5 min)

Run the complete workflow to prove everything works together:

```
1. GET /products           â†’ 3 products
2. POST /products          â†’ create "Guinness Stout" â†’ 201
3. GET /products           â†’ 4 products
4. GET /products/4         â†’ the new product
5. PUT /products/4         â†’ update the price
6. GET /products/4         â†’ confirm update
7. DELETE /products/4      â†’ 204
8. GET /products           â†’ back to 3 products
9. GET /products/4         â†’ 404
```

> "This is the complete CRUD lifecycle. Create, read, update, delete. All working. All with correct status codes."

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `TypeError: argument of type 'NoneType' is not iterable` when products list is empty | "The `max()` call fails on an empty list. For now, always have at least one item in the starting list." |
| PUT returns the old data | "Check that you are reassigning `products[i] = updated_data` - not modifying `updated_data` in place." |
| 422 Unprocessable Entity on POST | "FastAPI is rejecting the request body. Check that you sent valid JSON in the /docs form." |
| DELETE returns 200 instead of 204 | "Check that `status_code=204` is in the decorator AND you are returning `Response(status_code=204)` - not a dict." |
| `ImportError: cannot import name 'Optional'` | "Change `from typing import Optional` to just use `str | None = None` if using Python 3.10+." |

---

## Up Next

Topic 5 - Request & Response Models with Pydantic. We replace `product: dict` with typed Pydantic models that validate incoming data automatically.

