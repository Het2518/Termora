# 🚀 Termora — Setup Guide (For Beginners)

> This guide will help you run the **Termora** project on your local machine from scratch.  
> Read everything carefully. Follow each step one by one.

---

## 📋 What is this project?

Termora is a full-stack web app with:

| Part | Technology | What it does |
|------|-----------|--------------|
| **Frontend** | Next.js (React) | The UI you see in the browser |
| **Backend** | Python (FastAPI) | The API / logic / AI |
| **Database** | PostgreSQL | Stores all data |
| **Cache** | Redis | Background jobs, scheduling |
| **AI** | Groq API (Free) | LLM / AI features |

---

## ✅ Step 1 — Install Required Software

Install these **4 things** before starting:

### 1. Docker Desktop
> Docker will run PostgreSQL and Redis automatically. No manual database setup needed.

- Download: https://www.docker.com/products/docker-desktop/
- Install it, open it, and make sure the **whale icon** appears in your system tray (bottom-right)
- Docker must be **running** before you proceed

### 2. Node.js (LTS)
> Required to run the frontend.

- Download: https://nodejs.org/ → click **"LTS"** (left button)
- Install with default settings

### 3. Python 3.11+
> Required to run the backend.

- Download: https://www.python.org/downloads/
- ⚠️ During installation, **check the box** that says **"Add Python to PATH"**

### 4. Git (optional, only if you cloned via git)
- Download: https://git-scm.com/download/win

---

## ✅ Step 2 — Get the Project Files

If you received a **ZIP file**:
1. Right-click the zip → **Extract All**
2. Extract to somewhere simple like `C:\Termora`

---

## ✅ Step 3 — Get a Free Groq API Key

The AI features need a **free** API key from Groq.

1. Go to: https://console.groq.com/
2. Sign up (free account)
3. Go to **API Keys** → click **Create API Key**
4. Copy the key — it looks like: `gsk_xxxxxxxxxxxxxxxxxxxxxxxxx`

> 💡 This is completely free. No credit card needed.

---

## ✅ Step 4 — Setup Backend Environment File

1. Go to the `backend` folder inside the project
2. Find the file named **`.env.example`**
3. **Copy** it and **rename the copy** to **`.env`** (remove the `.example` part)
4. Open `.env` with Notepad or VS Code
5. Fill in these values:

```env
# --- Database (leave as-is, Docker handles this) ---
DATABASE_URL=postgresql://Termora:Termora_secret@localhost:5432/Termora_db

# --- Redis (leave as-is, Docker handles this) ---
REDIS_URL=redis://localhost:6379/0

# --- Groq AI (PASTE YOUR KEY HERE) ---
GROQ_API_KEY=gsk_paste_your_key_here
GROQ_MODEL=llama-3.1-70b-versatile

# --- Security (put any random 32+ character string) ---
SECRET_KEY=thisismyrandomsecretkey1234567890abcdef

# --- Encryption Key (run the command below to generate this) ---
ENCRYPTION_KEY=paste_generated_key_here

# --- MCP (leave as true — no extra credentials needed!) ---
MOCK_MCP=true

# --- Frontend URL (leave as-is) ---
FRONTEND_URL=http://localhost:3000
```

### 🔑 How to generate ENCRYPTION_KEY:

Open **PowerShell** and run:
```powershell
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
```
Copy the output and paste it as your `ENCRYPTION_KEY`.

---

## ✅ Step 5 — Setup Frontend Environment File

1. Go to the `frontend` folder
2. Create a new file named **`.env.local`**
3. Add this single line inside it:

```
NEXT_PUBLIC_API_URL=http://localhost:8000
```

---

## ✅ Step 6 — Start the Database & Backend (Docker)

Open **PowerShell** in the **root project folder** (e.g., `C:\Termora`) and run:

```powershell
# Start PostgreSQL + Redis in background
docker-compose up db redis -d
```

Wait about **30 seconds** for the database to start up fully.

Then verify it's running:
```powershell
docker ps
```
You should see `Termora_postgres` and `Termora_redis` in the list.

---

## ✅ Step 7 — Setup Python Backend

Open **PowerShell** in the **`backend`** folder:

```powershell
cd backend
```

### Create a Python virtual environment:
```powershell
python -m venv venv
```

### Activate it:
```powershell
venv\Scripts\activate
```
> You should see `(venv)` at the start of your terminal line.

### Install all Python packages:
```powershell
pip install -r requirements.txt
```
> ⏳ This will take 2–5 minutes. Let it finish.

### Create database tables:
```powershell
alembic upgrade head
```
> This creates all the tables in PostgreSQL automatically.

### Start the backend server:
```powershell
uvicorn app.main:app --reload --port 8000
```

You should see:
```
INFO:     Uvicorn running on http://127.0.0.1:8000
```

✅ **Backend is running!** You can visit http://localhost:8000/docs to see all API endpoints.

---

## ✅ Step 8 — Start the Frontend

Open a **new PowerShell window** in the **`frontend`** folder:

```powershell
cd frontend
npm install
npm run dev
```

You should see:
```
▲ Next.js 16.x
- Local: http://localhost:3000
```

✅ **Frontend is running!**

---

## 🎉 Final Step — Open the App

Open your browser and go to:
```
http://localhost:3000
```

You should see the Termora app! 🎉

---

## 🔄 Every Time You Want to Run the Project Again

You don't need to repeat all steps. Next time, just:

**Terminal 1 (Root folder):**
```powershell
docker-compose up db redis -d
```

**Terminal 2 (backend folder):**
```powershell
venv\Scripts\activate
uvicorn app.main:app --reload --port 8000
```

**Terminal 3 (frontend folder):**
```powershell
npm run dev
```

---

## ❓ Common Errors & Fixes

### ❌ `docker: command not found`
→ Docker Desktop is not running. Open Docker Desktop from your Start Menu and wait for it to start fully.

### ❌ `connection refused` on port 5432
→ PostgreSQL (Docker) hasn't started yet. Wait 30 seconds and try again.
```powershell
docker-compose up db redis -d
```

### ❌ `alembic: command not found`
→ Your virtual environment is not activated. Run:
```powershell
venv\Scripts\activate
```

### ❌ `ModuleNotFoundError` for any Python package
→ Make sure `(venv)` is shown in your terminal, then re-run:
```powershell
pip install -r requirements.txt
```

### ❌ `ENCRYPTION_KEY` error on startup
→ You forgot to generate and set the key. Run:
```powershell
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
```
Copy result into `backend/.env` as `ENCRYPTION_KEY=...`

### ❌ Frontend shows blank page or API error
→ Make sure backend is running on port 8000 and `frontend/.env.local` has:
```
NEXT_PUBLIC_API_URL=http://localhost:8000
```

### ❌ MCP / Integrations not working
→ Check that `backend/.env` has `MOCK_MCP=true`. With this setting, no real Google Drive / Slack / Okta credentials are needed. Everything uses dummy/mock data.

---

## 📦 What each part does (Quick Reference)

```
Termora/
├── frontend/        → Next.js app (UI) — runs on port 3000
├── backend/
│   ├── app/         → Main FastAPI backend — runs on port 8000
│   ├── services/    → Agent + Ingestion microservices
│   ├── alembic/     → Database migration files
│   └── .env         → ⭐ Your config file (YOU create this!)
├── mcp_servers/     → Mock MCP servers (used when MOCK_MCP=true)
├── docker-compose.yml → Starts PostgreSQL + Redis via Docker
└── SETUP_GUIDE.md   → This file!
```

---

## 🧠 What is MCP? (FAQ)

**Q: What is MCP?**  
A: MCP (Model Context Protocol) is how the AI agent connects to external tools like Google Drive, Slack, and Okta to read contracts and send notifications.

**Q: Do I need real Google / Slack accounts?**  
A: **No!** As long as `MOCK_MCP=true` is set in `.env`, the app uses fake/demo data. Real credentials are only needed for production deployment.

**Q: Where does mock data come from?**  
A: The `mcp_servers/` folder has mock servers (`mock_drive.py`, `mock_slack.py`) that return fake contract data so you can test the full flow.

---

## 📞 Need Help?

If you're stuck, share:
1. The exact error message you see
2. Which step you were on

> Made with ❤️ — Happy building!
