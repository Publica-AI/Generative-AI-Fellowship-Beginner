# Module Demo Guide - Databases & Authentication (Week 8)
**Module 4, Week 8 | Estimated duration: 60–75 minutes**

---

## The Story

**NaijaJobs** — a Nigerian job board startup based in Lagos. Job seekers can browse listings. Registered users can apply for jobs. Only company recruiters (admin users) can post and manage job listings. The backend must be secure — user data and company listings are sensitive.

**What this demo builds:**
A complete FastAPI backend for NaijaJobs that ties together every topic from Week 8:
- MySQL database with `users` and `jobs` tables (Topics 1–2)
- SQLAlchemy ORM connecting FastAPI to MySQL (Topic 3)
- Full CRUD for job listings (Topic 4)
- Secure user registration with password hashing (Topics 5–6)
- JWT login and token verification (Topic 7)
- Role-based access: public browsing, authenticated applications, admin-only job management (Topic 8)

---

## Prerequisites

1. MySQL running — `naija_jobs` database created (or reuse `naija_backend`)
2. Python environment with all packages installed: `fastapi uvicorn sqlalchemy pymysql passlib[bcrypt] python-dotenv python-jose[cryptography]`
3. VS Code with a fresh project folder: `naijajobs-backend/`
4. `.env` file with DATABASE_URL and a generated SECRET_KEY ready

---

## Project Setup (before live coding)

**Create the project folder and .env:**
```bash
mkdir naijajobs-backend
cd naijajobs-backend
```

```
# .env
DATABASE_URL=mysql+pymysql://root:yourpassword@localhost:3306/naija_jobs
SECRET_KEY=your_64_char_hex_key
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
```

**Create the database in MySQL:**
```sql
CREATE DATABASE naija_jobs CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

---

## Demo Steps

### Part 1 — Database Setup (10 min)

> "NaijaJobs needs two tables: users (job seekers and recruiters) and jobs (the listings). Let me set these up in MySQL first, then we will connect FastAPI to them."

**In the MySQL terminal:**
```sql
USE naija_jobs;

CREATE TABLE users (
    id            INT AUTO_INCREMENT PRIMARY KEY,
    name          VARCHAR(100) NOT NULL,
    email         VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role          VARCHAR(20) DEFAULT 'user',
    created_at    TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE jobs (
    id           INT AUTO_INCREMENT PRIMARY KEY,
    title        VARCHAR(200) NOT NULL,
    company      VARCHAR(150) NOT NULL,
    location     VARCHAR(100) NOT NULL,
    description  TEXT,
    salary_range VARCHAR(100),
    is_active    BOOLEAN DEFAULT TRUE,
    posted_by    INT NOT NULL,
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (posted_by) REFERENCES users(id)
);
```

> "Two tables. jobs has a foreign key — posted_by links each job to the user who posted it. In a real company like Jobberman Nigeria, every job listing is linked to the recruiter's account."

Verify:
```sql
SHOW TABLES;
DESCRIBE jobs;
```

---

### Part 2 — database.py and models.py (8 min)

**Create `database.py`:**
```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
import os
from dotenv import load_dotenv

load_dotenv()

DATABASE_URL = os.getenv("DATABASE_URL")
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()
```

**Create `models.py`:**
```python
from sqlalchemy import Column, Integer, String, Text, Boolean, TIMESTAMP, ForeignKey
from sqlalchemy.sql import func
from database import Base

class User(Base):
    __tablename__ = "users"

    id            = Column(Integer, primary_key=True, index=True)
    name          = Column(String(100), nullable=False)
    email         = Column(String(255), unique=True, nullable=False)
    password_hash = Column(String(255), nullable=False)
    role          = Column(String(20), default="user")
    created_at    = Column(TIMESTAMP, server_default=func.now())

class Job(Base):
    __tablename__ = "jobs"

    id           = Column(Integer, primary_key=True, index=True)
    title        = Column(String(200), nullable=False)
    company      = Column(String(150), nullable=False)
    location     = Column(String(100), nullable=False)
    description  = Column(Text, nullable=True)
    salary_range = Column(String(100), nullable=True)
    is_active    = Column(Boolean, default=True)
    posted_by    = Column(Integer, ForeignKey("users.id"), nullable=False)
    created_at   = Column(TIMESTAMP, server_default=func.now())
```

> "Two models — one per table. The Job model has a ForeignKey on posted_by that links to users.id. SQLAlchemy will use this for integrity enforcement."

---

### Part 3 — schemas.py and auth.py (8 min)

**Create `schemas.py`:**
```python
from pydantic import BaseModel
from typing import Optional

class UserCreate(BaseModel):
    name: str
    email: str
    password: str

class UserOut(BaseModel):
    id: int
    name: str
    email: str
    role: str
    class Config:
        from_attributes = True

class LoginRequest(BaseModel):
    email: str
    password: str

class TokenResponse(BaseModel):
    access_token: str
    token_type: str = "bearer"

class JobCreate(BaseModel):
    title: str
    company: str
    location: str
    description: Optional[str] = None
    salary_range: Optional[str] = None

class JobOut(BaseModel):
    id: int
    title: str
    company: str
    location: str
    description: Optional[str] = None
    salary_range: Optional[str] = None
    is_active: bool
    posted_by: int
    class Config:
        from_attributes = True

class JobUpdate(BaseModel):
    title: Optional[str] = None
    company: Optional[str] = None
    location: Optional[str] = None
    description: Optional[str] = None
    salary_range: Optional[str] = None
    is_active: Optional[bool] = None
```

**Create `auth.py`:**
```python
import os
from datetime import datetime, timedelta, timezone
from dotenv import load_dotenv
from passlib.context import CryptContext
from jose import JWTError, jwt
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

load_dotenv()

SECRET_KEY = os.getenv("SECRET_KEY")
ALGORITHM = os.getenv("ALGORITHM", "HS256")
ACCESS_TOKEN_EXPIRE_MINUTES = int(os.getenv("ACCESS_TOKEN_EXPIRE_MINUTES", "30"))

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/login")

def hash_password(plain: str) -> str:
    return pwd_context.hash(plain)

def verify_password(plain: str, hashed: str) -> bool:
    return pwd_context.verify(plain, hashed)

def create_access_token(data: dict) -> str:
    to_encode = data.copy()
    to_encode["exp"] = datetime.now(timezone.utc) + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

def get_current_user(token: str = Depends(oauth2_scheme)):
    exc = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        if payload.get("sub") is None:
            raise exc
        return payload
    except JWTError:
        raise exc

def require_admin(current_user: dict = Depends(get_current_user)):
    if current_user.get("role") != "admin":
        raise HTTPException(status_code=status.HTTP_403_FORBIDDEN, detail="Admin access required")
    return current_user
```

> "The complete auth module in one file. All five functions: hash, verify, create token, verify token, require admin."

---

### Part 4 — main.py — Registration and Login (10 min)

**Create `main.py`:**
```python
from fastapi import FastAPI, Depends, HTTPException, status, Response
from sqlalchemy.orm import Session
from typing import List, Optional
from database import SessionLocal, engine, Base
from models import User, Job
from schemas import (UserCreate, UserOut, LoginRequest, TokenResponse,
                     JobCreate, JobOut, JobUpdate)
from auth import (hash_password, verify_password, create_access_token,
                  get_current_user, require_admin)

Base.metadata.create_all(bind=engine)

app = FastAPI(title="NaijaJobs API", description="Job board backend for Nigeria")

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.post("/register", response_model=UserOut, status_code=status.HTTP_201_CREATED)
def register(user_data: UserCreate, db: Session = Depends(get_db)):
    if db.query(User).filter(User.email == user_data.email).first():
        raise HTTPException(status_code=400, detail="Email already registered")
    new_user = User(
        name=user_data.name,
        email=user_data.email,
        password_hash=hash_password(user_data.password)
    )
    db.add(new_user)
    db.commit()
    db.refresh(new_user)
    return new_user

@app.post("/login", response_model=TokenResponse)
def login(credentials: LoginRequest, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.email == credentials.email).first()
    if not user or not verify_password(credentials.password, user.password_hash):
        raise HTTPException(status_code=401, detail="Invalid email or password")
    token = create_access_token({"sub": str(user.id), "email": user.email, "role": user.role})
    return {"access_token": token, "token_type": "bearer"}
```

**Start the server:**
```bash
uvicorn main:app --reload
```

**Test registration and login in /docs:**
- Register Fatima: `{"name": "Fatima Bello", "email": "fatima@naijaapp.com", "password": "Kano@2024!"}`
- Log in as Fatima — copy the JWT

> "Registration and login working. Fatima is a regular user — role: user."

---

### Part 5 — Job Listings CRUD (12 min)

**Add job endpoints to `main.py`:**
```python
@app.get("/jobs", response_model=List[JobOut])
def get_active_jobs(
    location: Optional[str] = None,
    db: Session = Depends(get_db)
):
    query = db.query(Job).filter(Job.is_active == True)
    if location:
        query = query.filter(Job.location == location)
    return query.all()

@app.get("/jobs/{job_id}", response_model=JobOut)
def get_job(job_id: int, db: Session = Depends(get_db)):
    job = db.query(Job).filter(Job.id == job_id).first()
    if not job:
        raise HTTPException(status_code=404, detail="Job not found")
    return job

@app.post("/jobs", response_model=JobOut, status_code=status.HTTP_201_CREATED)
def post_job(
    job_data: JobCreate,
    db: Session = Depends(get_db),
    current_user: dict = Depends(require_admin)
):
    new_job = Job(
        **job_data.model_dump(),
        posted_by=int(current_user["sub"])
    )
    db.add(new_job)
    db.commit()
    db.refresh(new_job)
    return new_job

@app.put("/jobs/{job_id}", response_model=JobOut)
def update_job(
    job_id: int,
    job_data: JobUpdate,
    db: Session = Depends(get_db),
    current_user: dict = Depends(require_admin)
):
    job = db.query(Job).filter(Job.id == job_id).first()
    if not job:
        raise HTTPException(status_code=404, detail="Job not found")
    for field, value in job_data.model_dump(exclude_unset=True).items():
        setattr(job, field, value)
    db.commit()
    db.refresh(job)
    return job

@app.delete("/jobs/{job_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_job(
    job_id: int,
    db: Session = Depends(get_db),
    current_user: dict = Depends(require_admin)
):
    job = db.query(Job).filter(Job.id == job_id).first()
    if not job:
        raise HTTPException(status_code=404, detail="Job not found")
    db.delete(job)
    db.commit()
    return Response(status_code=status.HTTP_204_NO_CONTENT)
```

> "Notice the access pattern: GET /jobs and GET /jobs/{id} are public — anyone can browse. POST, PUT, DELETE all require admin — only recruiters manage listings."

---

### Part 6 — Create an Admin Recruiter and Test Full Flow (10 min)

**In MySQL — create a recruiter admin:**
```sql
-- Register Chidi via /docs first, then promote:
UPDATE users SET role = 'admin' WHERE email = 'chidi@naijaapp.com';
```

Or register and promote via the API then MySQL.

**Test the complete NaijaJobs flow:**

**Step 1 — Browse jobs as anonymous (public):**
GET /jobs — returns empty list (no jobs yet)

**Step 2 — Log in as admin (Chidi the recruiter):**
POST /login with Chidi's credentials → copy JWT → Authorize in /docs

**Step 3 — Post a job listing (admin only):**
```json
{
  "title": "Senior Python Developer",
  "company": "Flutterwave",
  "location": "Lagos",
  "description": "Join our payments infrastructure team. 3+ years FastAPI experience required.",
  "salary_range": "₦800,000 - ₦1,200,000 / month"
}
```
→ 201 Created with job id

**Step 4 — Post two more jobs:**
```json
{
  "title": "Data Analyst",
  "company": "MTN Nigeria",
  "location": "Lagos",
  "description": "Analyse subscriber data to improve product offerings.",
  "salary_range": "₦450,000 - ₦650,000 / month"
}
```
```json
{
  "title": "Backend Engineer",
  "company": "Paystack",
  "location": "Abuja",
  "description": "Build secure payment processing systems.",
  "salary_range": "₦700,000 - ₦1,000,000 / month"
}
```

**Step 5 — Browse as anonymous user:**
GET /jobs → all three listings visible

GET /jobs?location=Lagos → only Lagos listings

> "Public browsing works. Anyone can search by location. No login required."

**Step 6 — Try to post a job as regular user:**
Log out of Chidi's session. Log in as Fatima (role: user). Try POST /jobs.
→ 403 Forbidden

> "Fatima is logged in but she is not a recruiter. Authentication passed, authorisation failed."

**Step 7 — Update a job (admin only):**
Log back in as Chidi. Update the Paystack job to add more detail:
```json
{ "salary_range": "₦750,000 - ₦1,100,000 / month", "is_active": true }
```

**Step 8 — Admin views all users:**
```python
# Add this endpoint to main.py
@app.get("/admin/users", response_model=List[UserOut])
def get_all_users(
    db: Session = Depends(get_db),
    current_user: dict = Depends(require_admin)
):
    return db.query(User).all()
```

GET /admin/users as Chidi → both users visible, no password hashes.

> "Admin endpoint — shows all registered job seekers. In a real Jobberman-style app, this powers the admin dashboard."

**Step 9 — Deactivate a listing:**
PUT /jobs/{id} with `{"is_active": false}` on one job.
GET /jobs → only active jobs returned (because of the `is_active == True` filter).

> "Soft delete — mark a job inactive rather than deleting it. The listing is hidden from job seekers but still in the database. Useful for audit trails."

---

## Demo Wrap-Up

Summarise the full system that was just demonstrated:

| Endpoint | Access | Purpose |
|----------|--------|---------|
| POST /register | Public | Any job seeker can create an account |
| POST /login | Public | Get a JWT token |
| GET /jobs | Public | Browse active listings |
| GET /jobs/{id} | Public | View one listing |
| GET /me | Authenticated | See your own profile |
| POST /jobs | Admin | Recruiter posts a new listing |
| PUT /jobs/{id} | Admin | Recruiter updates a listing |
| DELETE /jobs/{id} | Admin | Recruiter removes a listing |
| GET /admin/users | Admin | View all registered users |

> "This is a real backend API. If you connected a React frontend from Intermediate AI Developer, users could browse jobs, register, log in, and apply — while recruiters could manage listings through a protected admin panel. That is an actual product."

---

## Common Student Issues During the Module Demo

| Issue | What to say |
|-------|-------------|
| Jobs table not created | "`Base.metadata.create_all(bind=engine)` runs at startup. Check that both `User` and `Job` are imported in main.py before this line." |
| POST /jobs returns 401 even with admin token | "Check the token — log in again as the admin user. Confirm the role field shows 'admin' in jwt.io." |
| GET /jobs returns jobs that were marked inactive | "Confirm the filter is `Job.is_active == True` not `Job.is_active` (which is always truthy). SQLAlchemy Boolean filters need `== True`." |
| ForeignKey violation on POST /jobs | "The `posted_by` field uses `int(current_user['sub'])` — if sub is missing from the token payload, this fails. Check that `create_access_token` includes 'sub' in the data dict." |
| /admin/users returns password hashes | "UserOut in schemas.py must not have a password_hash field. Check the schema definition." |
