# Demo Guide - Protecting Routes & Role-Based Access
**Module 4, Topic 8 | Estimated duration: 40–45 minutes**

---

## What This Demo Teaches

- Adding `require_admin()` to auth.py using dependency chaining
- Applying `Depends(require_admin)` to the DELETE /products endpoint
- Creating an admin user via MySQL UPDATE or create_admin.py
- Demonstrating 401 (no token), 403 (wrong role), and 204 (admin success) on the same endpoint
- Adding GET /admin/users as an admin-only endpoint
- Using `current_user` inside an endpoint to personalise a /dashboard response

---

## Setup — Before the Demo Starts

1. Project from Topic 7 is open with JWT auth fully working: `main.py`, `auth.py`, `database.py`, `models.py`, `schemas.py`
2. At least one regular user registered (`amara@naijaapp.com`, role: user)
3. Uvicorn is stopped
4. MySQL terminal is open and connected to `naija_backend`

> **Tell students:** "Topics 5–7 built authentication — proving who the user is. Today we build authorisation — controlling what they can do. By the end you will have three classes of access: public, authenticated, and admin. This is how real Nigerian SaaS apps work."

---

## Demo Steps

### Part 1 — Add require_admin() to auth.py (5 min)

**Open `auth.py`. Add to the bottom:**
```python
def require_admin(current_user: dict = Depends(get_current_user)):
    if current_user.get("role") != "admin":
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Admin access required"
        )
    return current_user
```

> "Five lines. require_admin() calls get_current_user() first via Depends — authentication always runs before authorisation. If the role is not 'admin', we raise 403 Forbidden. If it is, we return the current_user so the endpoint can use it."

**Draw the chain on the whiteboard:**
```
Request → get_current_user() (401 gate) → require_admin() (403 gate) → endpoint
```

---

### Part 2 — Protect the DELETE Endpoint (5 min)

**Open `main.py`. Update the import:**
```python
from auth import hash_password, verify_password, create_access_token, get_current_user, require_admin
```

**Update DELETE /products to require admin:**
```python
@app.delete("/products/{product_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_product(
    product_id: int,
    db: Session = Depends(get_db),
    current_user: dict = Depends(require_admin)
):
    product = db.query(Product).filter(Product.id == product_id).first()
    if product is None:
        raise HTTPException(status_code=404, detail="Product not found")
    db.delete(product)
    db.commit()
    return Response(status_code=status.HTTP_204_NO_CONTENT)
```

> "One line added to the function signature: `current_user: dict = Depends(require_admin)`. That is the entire change. FastAPI does the rest."

**Start the server:**
```bash
uvicorn main:app --reload
```

---

### Part 3 — Create an Admin User (5 min)

**In the MySQL terminal:**
```sql
-- Check current users
SELECT id, name, email, role FROM users;
```

If Chidi is registered, promote them:
```sql
UPDATE users SET role = 'admin' WHERE email = 'chidi@naijaapp.com';
```

If no second user exists, register one via /docs first, then promote them.

Verify:
```sql
SELECT id, name, email, role FROM users;
```

> "Two users — one with role 'user', one with role 'admin'. We directly updated the database. In production this would go through a protected admin API endpoint. For today, direct SQL is fine."

---

### Part 4 — Test All Three Access Levels (15 min)

**First, make sure there are products to test with:**
Insert a test product via POST /products in /docs (authorize with Amara's token first).

**Test 1 — Unauthenticated:**
Logout in /docs (click Authorize → Logout). Call DELETE /products/1.

> "401. 'Could not validate credentials.' The server doesn't know who we are. get_current_user() ran and failed."

**Test 2 — Regular user (Amara):**
Log in as `amara@naijaapp.com`. Copy the token. Authorize in /docs. Call DELETE /products/1.

Decode Amara's token at jwt.io — show `"role": "user"` in the payload.

> "403. 'Admin access required.' get_current_user() passed — Amara is authenticated. But require_admin() checked the role and rejected her. The endpoint never ran."

**Test 3 — Admin user (Chidi):**
Log in as `chidi@naijaapp.com` (role: admin). Copy the token. Authorize in /docs.

Decode Chidi's token at jwt.io — show `"role": "admin"` in the payload.

Call DELETE /products/1.

> "204 No Content. Product deleted. The whole chain passed."

Confirm deletion: GET /products/1 → 404.

> "Three users. Same endpoint. Three different responses: 401, 403, 204. This is role-based access control."

---

### Part 5 — Add GET /admin/users (8 min)

**Add to `main.py`:**
```python
@app.get("/admin/users", response_model=List[UserOut])
def get_all_users(
    db: Session = Depends(get_db),
    current_user: dict = Depends(require_admin)
):
    return db.query(User).all()
```

**Test with Amara's token (role: user):**
> "403. Admin access required."

**Test with Chidi's admin token:**
> "200. All users returned. No password hashes in the response — UserOut excludes them."

> "The `/admin/` prefix is a convention. Any developer reading the route list immediately knows this endpoint requires admin access. It is not enforced by FastAPI — it is self-documenting naming."

---

### Part 6 — Dashboard Endpoint With Personalised Response (7 min)

**Add to `main.py`:**
```python
@app.get("/dashboard")
def get_dashboard(current_user: dict = Depends(get_current_user)):
    role = current_user.get("role")

    if role == "admin":
        return {
            "message": f"Welcome, Admin {current_user['email']}",
            "access": "full",
            "features": ["manage_products", "manage_users", "view_reports"]
        }
    return {
        "message": f"Welcome, {current_user['email']}",
        "access": "standard",
        "features": ["browse_products", "place_orders", "view_my_orders"]
    }
```

**Test with Amara's token:**
> "Standard access. Her features list is the regular user set."

**Test with Chidi's admin token:**
> "Full access. Different message, different features list. Same endpoint, same code — different output based on role."

> "current_user is just a dict — you can read any field from it inside the endpoint. This is how your frontend knows what menu items to show."

---

## Final Project State After Topic 8

**Complete naija-backend project:**
```
naija-backend/
├── main.py
│   ├── GET  /products         (public)
│   ├── POST /products         (authenticated)
│   ├── GET  /products/{id}    (public)
│   ├── PUT  /products/{id}    (authenticated)
│   ├── DELETE /products/{id}  (admin only)
│   ├── POST /register         (public)
│   ├── POST /login            (public)
│   ├── GET  /me               (authenticated)
│   ├── GET  /admin/users      (admin only)
│   └── GET  /dashboard        (authenticated)
├── database.py
├── models.py    (Product, User)
├── schemas.py   (all schemas)
├── auth.py      (hashing + JWT + get_current_user + require_admin)
└── .env
```

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| Regular user gets 401 instead of 403 | "Check that your token is still valid — not expired. Log in again and test. If it is a fresh token and you still get 401, check that `get_current_user()` is running correctly." |
| Admin gets 403 | "Log in as the admin user again and copy the new token. Decode it at jwt.io — confirm the role is 'admin', not 'user'. If it shows 'user', the MySQL UPDATE did not commit. Run `SELECT role FROM users WHERE email='chidi@naijaapp.com'` to verify." |
| require_admin not found in auth.py import | "Update the import line in main.py to include require_admin: `from auth import ..., require_admin`." |
| GET /admin/users shows password_hash in response | "Check that UserOut in schemas.py does not have a password_hash field, and that `from_attributes = True` is in the Config class." |
| Students ask how to revoke tokens on logout | "Good question — JWTs cannot be revoked before expiry without a token blacklist (a table of revoked tokens the server checks). Short expiry (15–30 minutes) limits the damage. For production apps, implement a refresh token + blacklist pattern. For this module, logout on the client side (discard the token) is sufficient." |

---

## Module 4 — Week 8 Complete

Students have now built a complete, production-quality backend with:
- MySQL database + SQLAlchemy ORM
- Full CRUD for products
- Secure user registration (bcrypt hashing)
- JWT-based login and token verification
- Protected routes with role-based access control (401 / 403 / 204)

This is the technical foundation for Module 5 — where they connect LLMs and AI models to this backend to build AI-powered applications.
