# Demo Guide - Implementing User Registration & Login
**Module 4, Topic 6 | Estimated duration: 40–45 minutes**

---

## What This Demo Teaches

- Creating `auth.py` with `hash_password()` and `verify_password()`
- Adding `UserCreate`, `LoginRequest`, and `TokenResponse` schemas
- Building `POST /register` — duplicate check, hash, save, return UserOut
- Verifying the hash in MySQL directly
- Building `POST /login` — email lookup, password verify, return placeholder token
- Testing all three login scenarios (success, wrong password, unknown email)

---

## Setup — Before the Demo Starts

1. The `naija-backend/` project from Topics 3–4 is open in VS Code
2. MySQL is running and the `naija_backend` database has the users table (created in Topic 4)
3. Uvicorn is stopped
4. The `passlib[bcrypt]` package should already be installed (from Topic 5 demo) — verify: `pip show passlib`

> **Tell students:** "Today we turn authentication concepts into working code. By the end you will register a user, log in, and get back a token — using real password hashing. The token is still a placeholder today; Topic 7 makes it a proper JWT."

---

## Demo Steps

### Part 1 — Create auth.py (5 min)

**Create a new file called `auth.py` in the project root:**
```python
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def hash_password(plain_password: str) -> str:
    return pwd_context.hash(plain_password)

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)
```

> "Nine lines. This is all the hashing logic our application needs. hash_password() is called at registration. verify_password() is called at login. Everything else in auth.py — the JWT functions — comes in Topic 7."

**Test it quickly in the terminal:**
```python
python -c "from auth import hash_password; print(hash_password('test123'))"
```

> "Runs without errors — auth.py is working."

---

### Part 2 — Add Schemas (5 min)

**Open `schemas.py`. Add:**
```python
class UserCreate(BaseModel):
    name: str
    email: str
    password: str

class LoginRequest(BaseModel):
    email: str
    password: str

class TokenResponse(BaseModel):
    access_token: str
    token_type: str = "bearer"
```

> "UserCreate is what the client sends to register — name, email, and the plain text password. LoginRequest is what they send to log in — email and password. TokenResponse is what login returns — the token string and the type 'bearer'."

Point back to the existing UserOut:

> "UserOut stays as is — it has no password field. That is what the register endpoint returns."

---

### Part 3 — POST /register (12 min)

**Open `main.py`. Add imports at the top:**
```python
from auth import hash_password, verify_password
from schemas import UserCreate, UserOut, LoginRequest, TokenResponse
from models import User
```

**Add the register endpoint:**
```python
@app.post("/register", response_model=UserOut, status_code=status.HTTP_201_CREATED)
def register(user_data: UserCreate, db: Session = Depends(get_db)):
    existing_user = db.query(User).filter(User.email == user_data.email).first()
    if existing_user:
        raise HTTPException(status_code=400, detail="Email already registered")

    hashed = hash_password(user_data.password)

    new_user = User(
        name=user_data.name,
        email=user_data.email,
        password_hash=hashed
    )

    db.add(new_user)
    db.commit()
    db.refresh(new_user)
    return new_user
```

**Start the server:**
```bash
uvicorn main:app --reload
```

**Test in /docs — register Amara:**
```json
{
  "name": "Amara Nwosu",
  "email": "amara@naijaapp.com",
  "password": "Abuja2024!"
}
```

Expected response — status 201, UserOut (no password):
```json
{
  "id": 1,
  "name": "Amara Nwosu",
  "email": "amara@naijaapp.com",
  "role": "user"
}
```

> "201 Created. Notice there is no password in the response. The response_model=UserOut filtered it out."

**Verify in MySQL — switch to MySQL terminal:**
```sql
SELECT id, name, email, password_hash, role FROM users;
```

> "There is the row. password_hash starts with $2b$12$ — that is a bcrypt hash. The original password 'Abuja2024!' is not stored anywhere in the database."

**Test duplicate email — try registering Amara again:**

> "Status 400. 'Email already registered.' The check runs before any hashing, so we save the compute time of hashing a password we are going to reject anyway."

**Register a second user — Chidi:**
```json
{
  "name": "Chidi Okafor",
  "email": "chidi@naijaapp.com",
  "password": "Lagos@123"
}
```

Run the MySQL SELECT again — two rows, two different hashes.

---

### Part 4 — POST /login (12 min)

**Add the login endpoint:**
```python
@app.post("/login", response_model=TokenResponse)
def login(credentials: LoginRequest, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.email == credentials.email).first()
    if user is None:
        raise HTTPException(status_code=401, detail="Invalid email or password")

    if not verify_password(credentials.password, user.password_hash):
        raise HTTPException(status_code=401, detail="Invalid email or password")

    return {
        "access_token": f"user_id_{user.id}",
        "token_type": "bearer"
    }
```

**Test — successful login:**
```json
{ "email": "amara@naijaapp.com", "password": "Abuja2024!" }
```

> "Status 200. The token is 'user_id_1'. This is a placeholder — we can see it means user 1, but anyone could forge it by just sending 'user_id_1'. Topic 7 fixes this with a real JWT."

**Test — wrong password:**
```json
{ "email": "amara@naijaapp.com", "password": "wrongpassword" }
```

> "Status 401. 'Invalid email or password.' Same message as wrong email."

**Test — email not registered:**
```json
{ "email": "nobody@naijaapp.com", "password": "anything" }
```

> "Status 401. Same message. The client cannot tell the difference between a wrong password and an unregistered email — intentional."

**Ask students:** "Why not say 'email not found' when the email does not exist?"

Let them answer before explaining account enumeration.

---

### Part 5 — Quick Security Verification (5 min)

**In the MySQL terminal, show the full users table:**
```sql
SELECT * FROM users;
```

Point to each column:
- `id` — auto-generated
- `name`, `email` — plain text (acceptable — not sensitive like a password)
- `password_hash` — `$2b$12$...` — bcrypt hash, not the original password
- `role` — "user" — default value from the model
- `created_at` — auto-set timestamp

> "This is what a safely built users table looks like. If someone stole this database right now, they would have hashed passwords — not the originals. They cannot log in with a hash. They would need to brute-force it, which bcrypt makes very slow."

---

## Final State of auth.py, schemas.py, and main.py additions

**auth.py (new file):**
```python
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def hash_password(plain_password: str) -> str:
    return pwd_context.hash(plain_password)

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)
```

**schemas.py additions:**
```python
class UserCreate(BaseModel):
    name: str
    email: str
    password: str

class LoginRequest(BaseModel):
    email: str
    password: str

class TokenResponse(BaseModel):
    access_token: str
    token_type: str = "bearer"
```

**main.py additions:**
```python
@app.post("/register", response_model=UserOut, status_code=status.HTTP_201_CREATED)
def register(user_data: UserCreate, db: Session = Depends(get_db)):
    existing_user = db.query(User).filter(User.email == user_data.email).first()
    if existing_user:
        raise HTTPException(status_code=400, detail="Email already registered")
    hashed = hash_password(user_data.password)
    new_user = User(name=user_data.name, email=user_data.email, password_hash=hashed)
    db.add(new_user)
    db.commit()
    db.refresh(new_user)
    return new_user

@app.post("/login", response_model=TokenResponse)
def login(credentials: LoginRequest, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.email == credentials.email).first()
    if user is None:
        raise HTTPException(status_code=401, detail="Invalid email or password")
    if not verify_password(credentials.password, user.password_hash):
        raise HTTPException(status_code=401, detail="Invalid email or password")
    return {"access_token": f"user_id_{user.id}", "token_type": "bearer"}
```

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `ModuleNotFoundError: No module named 'passlib'` | "Run `pip install passlib[bcrypt]` — the square brackets install the bcrypt backend." |
| `AttributeError: 'User' object has no attribute 'password_hash'` | "Check your User model in models.py — the column must be named `password_hash`. If you named it differently, update either the model or the endpoint to match." |
| Registration returns 500 with IntegrityError for duplicate email | "You are checking for duplicates correctly, but make sure the check runs BEFORE the db.add(). The duplicate check should be the first line in the function." |
| Login always returns 401 for the correct password | "Print both the stored hash and the result of verify_password() to debug. Most likely the password was hashed differently on registration — check that hash_password() is being called at registration." |
| `response_model=UserOut` returns password_hash in the response | "Check that UserOut in schemas.py does NOT have a password_hash field, and that `from_attributes = True` is in the Config class." |

---

## Up Next

Topic 7 — JWT Authentication in FastAPI. The placeholder token becomes a real signed JWT. We add `create_access_token()` and `get_current_user()` to auth.py, protect the first endpoint, and test authentication from /docs using the Authorize button.
