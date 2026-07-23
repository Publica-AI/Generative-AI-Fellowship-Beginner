# Demo Guide - Introduction to FastAPI
**Module 4, Topic 3 | Estimated duration: 35–40 minutes**

---

## What This Demo Teaches

- Installing FastAPI and uvicorn
- Creating a FastAPI app in main.py
- Writing GET endpoints with decorators
- Running the development server with uvicorn
- Exploring the automatic /docs interface
- Adding path parameters and returning status codes

---

## Setup — Before the Demo Starts

1. Every student must have Python installed (3.8+)
2. Run `pip install fastapi uvicorn` — confirm no errors
3. Create a project folder called `naija-backend/`
4. Create an empty `main.py` inside it
5. Open VS Code with `naija-backend/` as the workspace
6. Open a terminal inside VS Code pointed at `naija-backend/`

> **Tell students:** "Keep your terminal and browser open side by side with VS Code. You will use all three throughout this demo."

---

## Demo Steps

### Part 1 — Install and Verify (3 min)

**In the terminal:**
```bash
pip install fastapi uvicorn
python -c "import fastapi; print(fastapi.__version__)"
```

> "Both packages install in seconds. fastapi is the framework. uvicorn is the server that runs it."

---

### Part 2 — Your First FastAPI App (8 min)

**Type this in main.py — do not copy-paste:**
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Welcome to NaijaBiz API", "status": "running"}
```

> "Seven lines. That is a complete, running API. Let me explain each one."

Walk through each line:
- `from fastapi import FastAPI` — import the class
- `app = FastAPI()` — create the app instance — this is what uvicorn runs
- `@app.get("/")` — decorator: register this function as a GET endpoint at "/"
- `def read_root():` — the function that runs when someone calls GET /
- `return {...}` — FastAPI converts this dict to JSON automatically

---

### Part 3 — Run the Server (5 min)

**In the terminal:**
```bash
uvicorn main:app --reload
```

**Expected output:**
```
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process
INFO:     Application startup complete.
```

> "The server is running. `main` is the filename — main.py. `app` is the FastAPI instance inside it. `--reload` means uvicorn watches for file changes and restarts automatically."

**Open browser → http://127.0.0.1:8000**

> "There is your API. A real HTTP server running on your laptop. That JSON response came from the function you just wrote."

---

### Part 4 — Explore /docs (7 min)

**Open browser → http://127.0.0.1:8000/docs**

> "FastAPI generated this documentation automatically from your code. No extra work from you."

Walk through the interface:
1. Show the GET / endpoint in the list
2. Click on it to expand
3. Click "Try it out"
4. Click "Execute"
5. Show the response — status 200, the JSON body

> "We just tested our API from the browser. This is your development testing tool. Every endpoint you add will appear here automatically."

Show the `/redoc` alternative:

> "There is also /redoc — a different visual style, same content. Use whichever you prefer."

---

### Part 5 — Add More Endpoints (7 min)

**Update main.py — add two more endpoints:**
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Welcome to NaijaBiz API", "status": "running"}

@app.get("/health")
def health_check():
    return {"status": "healthy", "version": "1.0"}

@app.get("/cities")
def get_nigerian_cities():
    return {
        "cities": ["Lagos", "Abuja", "Kano", "Port Harcourt", "Ibadan"]
    }
```

**Save the file.**

> "Watch the terminal — uvicorn detected the change and reloaded."

**Refresh /docs in the browser.**

> "Three endpoints now. Test /cities — you get the list back."

---

### Part 6 — Path Parameters (7 min)

**Add the city detail endpoint:**
```python
@app.get("/cities/{city_name}")
def get_city_info(city_name: str):
    cities = {
        "lagos": {"state": "Lagos", "population": "15 million"},
        "abuja": {"state": "FCT", "population": "3 million"},
        "kano": {"state": "Kano", "population": "4 million"},
    }

    city_data = cities.get(city_name.lower())

    if city_data:
        return city_data
    else:
        return {"error": f"City '{city_name}' not found"}
```

**Save. Refresh /docs. Test with `lagos`, then `abuja`, then `ibadan`.**

> "The path parameter `{city_name}` captures whatever is in the URL. GET /cities/lagos passes 'lagos' to the function. GET /cities/ibadan passes 'ibadan' — and since it is not in our dict, we get the error message."

**Ask students:** "What status code does the error response have?" — they should check in /docs — it is 200. "That is wrong. When something is not found, we should return 404. Let's fix that."

**Update the endpoint:**
```python
from fastapi import FastAPI, Response

app = FastAPI()

# ... previous endpoints ...

@app.get("/cities/{city_name}")
def get_city_info(city_name: str, response: Response):
    cities = {
        "lagos": {"state": "Lagos", "population": "15 million"},
        "abuja": {"state": "FCT", "population": "3 million"},
        "kano": {"state": "Kano", "population": "4 million"},
    }

    city_data = cities.get(city_name.lower())

    if city_data:
        return city_data
    else:
        response.status_code = 404
        return {"error": f"City '{city_name}' not found"}
```

**Save. Test `ibadan` in /docs — now shows 404.**

> "The status code is now correct. Topic 4 covers all the status codes you need for a complete API."

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `ModuleNotFoundError: No module named 'fastapi'` | "Run `pip install fastapi uvicorn` in the same terminal/environment you are using to run uvicorn." |
| `Address already in use` on port 8000 | "Another server is using port 8000. Run `uvicorn main:app --reload --port 8001` to use a different port." |
| Changes not reflected after saving | "Make sure --reload is in your uvicorn command. Check the terminal — uvicorn should print 'Reloading...'" |
| SyntaxError in terminal after saving | "Read the error message — it shows the line number. Fix the typo in main.py, save again." |
| /docs shows old endpoints after adding new ones | "Refresh the browser page — /docs is a regular web page, it does not auto-update." |

---

## Up Next

Topic 4 — Building API Endpoints with FastAPI. We add POST endpoints, query parameters, all HTTP status codes, and build a complete Nigerian API with multiple endpoints.
