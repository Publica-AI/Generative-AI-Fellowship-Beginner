# Demo Guide - Authentication Concepts
**Module 4, Topic 5 | Estimated duration: 25–30 minutes**

---

## What This Demo Teaches

- Installing passlib[bcrypt] and hashing passwords in Python
- Observing that the same password produces different hashes each time
- Verifying a correct and incorrect password against a hash
- Decoding a JWT at jwt.io to see the three parts
- Generating a secure random SECRET_KEY
- Understanding expiry by looking at the `exp` field in the JWT payload

---

## Setup — Before the Demo Starts

1. Python terminal open (can be inside VS Code or a standalone terminal)
2. A browser open and ready to visit jwt.io
3. No FastAPI server needs to be running — this is a standalone Python demo

> **Tell students:** "This demo has no FastAPI. We are working at the Python level — hashing passwords and inspecting JWTs — so that you understand what your auth code is doing before you write it."

---

## Demo Steps

### Part 1 — Install passlib and Hash a Password (8 min)

**In the terminal:**
```bash
pip install passlib[bcrypt]
```

**Open a Python interactive session or a scratch file. Type:**
```python
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

# Hash a password
hashed = pwd_context.hash("Abuja2024!")
print(hashed)
```

> "Run this once. Copy the output. Run it again."

Show that the two outputs are different:
```
$2b$12$abc... (first run)
$2b$12$xyz... (second run)
```

> "Same password. Different hash. That is the salt at work. MySQL stores the hash — never the original password. The original password is gone the moment we hash it."

**Now hash a different, short password:**
```python
hashed_weak = pwd_context.hash("qwerty")
print(hashed_weak)
```

> "Notice: 'qwerty' produces a hash that looks exactly as long and complex as 'Abuja2024!'. bcrypt does not reveal anything about the strength of the password through the hash."

---

### Part 2 — Verify a Password (5 min)

```python
stored_hash = pwd_context.hash("Abuja2024!")

# Correct password
print(pwd_context.verify("Abuja2024!", stored_hash))   # True

# Wrong password
print(pwd_context.verify("wrongpassword", stored_hash))  # False

# Empty string
print(pwd_context.verify("", stored_hash))  # False
```

> "Three calls. True once, False twice. This is the entire login check: user sends their password → we hash what they sent → compare with the stored hash → True means logged in, False means rejected."

**Ask students:** "Do we ever need to know what the original password was to do this check?"

> "No. We never see the original password again after registration. verify() does not reverse the hash — it re-hashes the input and compares the two hashes internally."

---

### Part 3 — Inspect a JWT at jwt.io (10 min)

**Open the browser → jwt.io**

Paste this sample JWT into the "Encoded" box on the left:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxIiwiZW1haWwiOiJhbWFyYUBlbWFpbC5jb20iLCJyb2xlIjoidXNlciIsImV4cCI6MTcwMDAwMDAwMH0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

> "Watch the right side update. Three sections."

**Walk through the decoded output:**

Header (red):
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
> "The header says which algorithm was used to sign this token — HS256. This is the algorithm we will use."

Payload (purple):
```json
{
  "sub": "1",
  "email": "amara@email.com",
  "role": "user",
  "exp": 1700000000
}
```
> "The payload. This is not encrypted — anyone with the token can read this. sub is the user's id. exp is the expiry timestamp. This token expired in November 2023."

**Ask students:** "What should you never put in this payload?"

> "Passwords. Card numbers. Anything sensitive. The payload is readable by anyone. The security comes from the signature — not hiding the payload."

Signature (blue):
> "The signature proves this token was created by our server. Our server has a secret key. If you tamper with the payload — change the role from 'user' to 'admin' for example — the signature no longer matches and the server rejects the token."

**Demonstrate tampering:** In the payload section, change `"role": "user"` to `"role": "admin"`.

> "The encoded token on the left changed. The signature is now invalid. Our server would reject this. The signature cannot be forged without the secret key."

---

### Part 4 — Generate a Secure SECRET_KEY (5 min)

**In the Python terminal:**
```python
import secrets
print(secrets.token_hex(32))
# b3f4e2a1c8d9... (64-character hex string)
```

> "This is what your SECRET_KEY should look like. Never use 'secret' or your name or a date. This 64-character random string is what protects all your tokens."

**Show the anti-pattern:**
```python
SECRET_KEY = "secret"           # ← never do this
SECRET_KEY = "naijashop2024"    # ← never do this
SECRET_KEY = secrets.token_hex(32)  # ← always do this
```

> "Store it in .env. Load it with python-dotenv. Never hardcode it in main.py or auth.py. Never commit it to Git."

**Create .env now if students do not have one:**
```
DATABASE_URL=mysql+pymysql://root:yourpassword@localhost:3306/naija_backend
SECRET_KEY=paste_the_generated_key_here
```

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `ModuleNotFoundError: No module named 'passlib'` | "Run `pip install passlib[bcrypt]` — note the square brackets, they install the bcrypt backend along with passlib." |
| Both hashes look the same to the eye | "They are not the same — look closely at the characters after `$2b$12$`. The strings are different. Copy both into a text editor and compare." |
| jwt.io shows "Invalid Signature" | "That is expected — the sample token uses a fake secret key. The important thing is that the payload decoded correctly. In Topics 6–7 we will create tokens with our own secret key." |
| Students ask if JWT is the same as a session cookie | "They serve the same purpose — keeping you logged in — but differently. A session cookie is an ID that the server looks up in a database. A JWT contains the user data itself and is verified by signature, no database needed." |

---

## Up Next

Topic 6 — Implementing User Registration & Login. Now that students understand hashing and tokens conceptually, they will build the `POST /register` and `POST /login` endpoints using everything from this topic.
