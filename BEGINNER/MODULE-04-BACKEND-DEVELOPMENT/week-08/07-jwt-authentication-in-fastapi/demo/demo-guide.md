# Demo Guide - JWT Authentication in FastAPI
**Module 4, Topic 7 | Estimated duration: 45–50 minutes**

---

## What This Demo Teaches

- Installing python-jose and loading SECRET_KEY from .env
- Adding `create_access_token()` to auth.py
- Updating POST /login to return a real JWT
- Decoding the JWT at jwt.io to inspect the payload
- Adding `get_current_user()` and `oauth2_scheme` to auth.py
- Building GET /me and protecting it with `Depends(get_current_user)`
- Using the Authorize button in /docs
- Testing all failure cases: no token, wrong token, expired token

---

## Setup — Before the Demo Starts

1. Project from Topic 6 is open in VS Code: `main.py`, `database.py`, `models.py`, `schemas.py`, `auth.py`
2. `.env` exists with `DATABASE_URL` and a generated `SECRET_KEY`
3. Uvicorn is stopped
4. Browser tab open to jwt.io (for live JWT decoding)

> **Tell students:** "Everything we built in Topics 3–6 stays exactly as is. We are adding JWT creation and verification to auth.py, updating the one return line in login, and adding one new endpoint. Small changes, big impact."

---

## Demo Steps

### Part 1 — Install python-jose and Update .env (5 min)

**In the terminal:**
```bash
pip install python-jose[cryptography]
```

**Check .env has a SECRET_KEY.** If not, generate one:
```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

Paste the output into .env:
```
DATABASE_URL=mysql+pymysql://root:yourpassword@localhost:3306/naija_backend
SECRET_KEY=paste_your_64_character_key_here
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
```

> "SECRET_KEY is what makes our JWTs unforgeable. Every token we create is signed with this key. If you change this key, all existing tokens become invalid — every user would need to log in again."

---

### Part 2 — Update auth.py With JWT Functions (10 min)

**Open `auth.py`. The current file:**
```python
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def hash_password(plain_password: str) -> str:
    return pwd_context.hash(plain_password)

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)
```

**Add to auth.py — the JWT imports and config:**
```python
import os
from datetime import datetime, timedelta, timezone
from dotenv import load_dotenv
from jose import JWTError, jwt
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

load_dotenv()

SECRET_KEY = os.getenv("SECRET_KEY")
ALGORITHM = os.getenv("ALGORITHM", "HS256")
ACCESS_TOKEN_EXPIRE_MINUTES = int(os.getenv("ACCESS_TOKEN_EXPIRE_MINUTES", "30"))

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/login")
```

**Add `create_access_token()`:**
```python
def create_access_token(data: dict) -> str:
    to_encode = data.copy()
    expire = datetime.now(timezone.utc) + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode["exp"] = expire
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
```

**Test it immediately in the terminal:**
```python
python -c "
from auth import create_access_token
token = create_access_token({'sub': '1', 'email': 'amara@test.com', 'role': 'user'})
print(token)
"
```

> "Copy that JWT. Go to jwt.io. Paste it in."

**Show jwt.io with the decoded payload:**
- Header: `{"alg": "HS256", "typ": "JWT"}`
- Payload: `{"sub": "1", "email": "amara@test.com", "role": "user", "exp": <timestamp>}`

> "There is our payload. The exp is 30 minutes from now. The signature section shows 'Invalid Signature' on jwt.io because it does not have our SECRET_KEY — but our server does. Our server can verify this token."

---

### Part 3 — Update POST /login (5 min)

**Open `main.py`. Update the import at the top:**
```python
from auth import hash_password, verify_password, create_access_token, get_current_user
```

**Find the login endpoint and update the return:**
```python
@app.post("/login", response_model=TokenResponse)
def login(credentials: LoginRequest, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.email == credentials.email).first()
    if user is None:
        raise HTTPException(status_code=401, detail="Invalid email or password")

    if not verify_password(credentials.password, user.password_hash):
        raise HTTPException(status_code=401, detail="Invalid email or password")

    token = create_access_token(data={
        "sub": str(user.id),
        "email": user.email,
        "role": user.role
    })

    return {"access_token": token, "token_type": "bearer"}
```

> "One change: the placeholder string is replaced by create_access_token(). Everything else is identical."

**Start the server:**
```bash
uvicorn main:app --reload
```

**Test POST /login in /docs — log in as Amara.**

Copy the `access_token`. Go to jwt.io. Paste it.

> "The payload shows Amara's id, email, role, and a real expiry timestamp. This is what gets sent with every authenticated request."

---

### Part 4 — Add get_current_user() to auth.py (8 min)

**Add to the bottom of `auth.py`:**
```python
def get_current_user(token: str = Depends(oauth2_scheme)):
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        user_id: str = payload.get("sub")
        if user_id is None:
            raise credentials_exception
        return payload
    except JWTError:
        raise credentials_exception
```

> "This is the other half. create_access_token() creates the token. get_current_user() reads and verifies it. Every protected endpoint calls get_current_user() via Depends()."

Walk through the function:
1. `Depends(oauth2_scheme)` — reads the `Authorization: Bearer <token>` header
2. `jwt.decode()` — verifies signature + checks expiry in one call
3. `payload.get("sub")` — extract the user id
4. `return payload` — the endpoint receives the decoded user data
5. `except JWTError` — catches everything: expired, tampered, malformed → 401

---

### Part 5 — Build and Test GET /me (10 min)

**Add to `main.py`:**
```python
from auth import hash_password, verify_password, create_access_token, get_current_user

@app.get("/me", response_model=UserOut)
def get_my_profile(
    current_user: dict = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    user_id = int(current_user["sub"])
    user = db.query(User).filter(User.id == user_id).first()
    if user is None:
        raise HTTPException(status_code=404, detail="User not found")
    return user
```

**Test 1 — Try GET /me without any token:**

Click GET /me in /docs → Execute without authorizing.

> "401. FastAPI saw no Authorization header and returned Unauthorized immediately. The endpoint function never ran."

**Test 2 — Authorize in /docs:**

1. Click the padlock icon at the top of /docs
2. In the "Value" field, type: `Bearer ` then paste the token
   (or just paste the token — /docs adds "Bearer" automatically in newer versions)
3. Click Authorize → Close

Try GET /me again.

> "200. Your profile is back — id, name, email, role. The endpoint decoded the JWT, extracted the user id, queried MySQL for the full user record, and returned it through the UserOut schema."

**Test 3 — Test with a tampered token:**

Logout in /docs. Modify one character in the token. Authorize with the modified token. Try GET /me.

> "401. 'Could not validate credentials.' The signature check failed because we changed the payload without re-signing."

---

### Part 6 — Protect POST /products (5 min)

**Update POST /products in main.py:**
```python
@app.post("/products", response_model=ProductOut, status_code=status.HTTP_201_CREATED)
def create_product(
    product: ProductCreate,
    db: Session = Depends(get_db),
    current_user: dict = Depends(get_current_user)
):
    new_product = Product(**product.model_dump())
    db.add(new_product)
    db.commit()
    db.refresh(new_product)
    return new_product
```

**Test — try POST /products without a token:**

> "401. Authentication required. Now authorize with the token and try again."

**Test — try POST /products with a valid token:**

> "201 Created. Same as before — but now only authenticated users can create products."

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `AttributeError: 'NoneType' object has no attribute...` on SECRET_KEY | "Your .env file is not being loaded. Check that load_dotenv() is called before reading the env variables, and that .env is in the project root folder." |
| Login returns a short token — `user_id_1` instead of a JWT | "You are importing get_current_user but not create_access_token. Update the import line and check that the login endpoint calls create_access_token()." |
| /docs does not show the Authorize button | "Check that `oauth2_scheme = OAuth2PasswordBearer(tokenUrl='/login')` is declared in auth.py and that auth.py is imported in main.py." |
| GET /me returns 401 even after authorizing | "In the /docs Authorize dialog, make sure you pasted only the token value — not the full JSON response. The field expects just the token string." |
| `JWTError: Signature verification failed` | "The SECRET_KEY used to create the token and the one used to verify it must be the same. If you changed .env after creating the token, log in again to get a fresh token." |
| Token decodes fine at jwt.io but server returns 401 | "jwt.io does not know your SECRET_KEY — it cannot verify the signature. That is expected. The server can verify it because it has the key." |

---

## Up Next

Topic 8 — Protecting Routes & Role-Based Access. Authentication tells us who the user is. Authorisation tells us what they can do. We add a `require_admin` dependency that checks the role in the JWT payload, protect admin-only endpoints, and demonstrate the 401 vs 403 difference.
