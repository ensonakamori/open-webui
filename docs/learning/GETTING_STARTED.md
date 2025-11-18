# Getting Started with Open WebUI Development

**Welcome to Open WebUI!** This guide will help you set up your development environment and make your first changes.

**Estimated time:** 30-60 minutes
**Prerequisites:** Basic terminal/command line knowledge
**Documented:** November 18, 2025

---

## 📋 Table of Contents

1. [What is Open WebUI?](#what-is-open-webui)
2. [Prerequisites](#prerequisites)
3. [Quick Start (Development)](#quick-start-development)
4. [Detailed Setup](#detailed-setup)
5. [First Run](#first-run)
6. [Making Your First Change](#making-your-first-change)
7. [Troubleshooting](#troubleshooting)
8. [Next Steps](#next-steps)

---

## What is Open WebUI?

Open WebUI is a **self-hosted AI chat platform** that provides:

🤖 **Multi-LLM Support:** Works with Ollama, OpenAI, Anthropic, Google AI, and more
📚 **RAG (Retrieval Augmented Generation):** Chat with your documents using vector search
🎨 **Rich UI:** Advanced chat interface with code editing, image generation, and more
🔒 **Self-hosted:** Full control over your data and privacy
🧩 **Extensible:** Functions, tools, and pipelines for custom logic

**Tech Stack:**
- **Frontend:** Svelte 5 + SvelteKit 2 + TypeScript + Tailwind CSS 4
- **Backend:** Python FastAPI + SQLAlchemy + Redis
- **AI/ML:** LangChain, ChromaDB, Transformers
- **Special:** Pyodide (Python in browser!)

For detailed architecture, see [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md)

---

## Prerequisites

### Required Software

| Software | Version | Purpose | Install Link |
|----------|---------|---------|-------------|
| **Node.js** | 18.13.0 - 22.x | Frontend build tool | [nodejs.org](https://nodejs.org/) |
| **npm** | 6.0.0+ | Package manager | Included with Node.js |
| **Python** | 3.11 - 3.12 | Backend runtime | [python.org](https://www.python.org/) |
| **Git** | Latest | Version control | [git-scm.com](https://git-scm.com/) |

### Optional (Recommended)

| Software | Purpose | Install Link |
|----------|---------|-------------|
| **Docker** | Container deployment | [docker.com](https://www.docker.com/) |
| **Redis** | Caching (optional for dev) | [redis.io](https://redis.io/) |
| **Ollama** | Local LLM runtime | [ollama.ai](https://ollama.ai/) |

### Verify Installation

```bash
# Check Node.js version (should be 18-22)
node --version

# Check npm version
npm --version

# Check Python version (should be 3.11 or 3.12)
python --version
# OR
python3 --version

# Check Git
git --version
```

### 🧠 Mental Model: Two Separate Servers

Open WebUI runs **two servers** simultaneously:

```
┌─────────────────┐         ┌─────────────────┐
│  Vite Dev Server│  HTTP   │ FastAPI Server  │
│  (Frontend)     │ ──────> │  (Backend)      │
│  Port: 5173     │         │  Port: 8080     │
└─────────────────┘         └─────────────────┘
      Svelte 5                   Python
    TypeScript                   FastAPI
```

- **Frontend** (Vite): Hot-reloading UI development
- **Backend** (FastAPI): API, database, LLM integrations

🌉 **React Developers:** Similar to Create React App + Express setup
🌉 **Next.js Developers:** Similar to Next.js but frontend/backend are separate processes

---

## Quick Start (Development)

**TL;DR:** Get it running in 5 minutes:

```bash
# 1. Clone the repository
git clone https://github.com/open-webui/open-webui.git
cd open-webui

# 2. Install frontend dependencies
npm install

# 3. Install backend dependencies (create virtual environment first)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -e .

# 4. Start both servers (in separate terminals)
# Terminal 1: Backend
source venv/bin/activate
python backend/open_webui/main.py

# Terminal 2: Frontend
npm run dev

# 5. Open browser
# Visit: http://localhost:5173
```

**✅ Quick Check:** If you see the Open WebUI login page, you're good!

---

## Detailed Setup

### Step 1: Clone the Repository

```bash
# Clone the repo
git clone https://github.com/open-webui/open-webui.git

# Navigate into the project
cd open-webui

# Check the current branch
git branch
```

You should be on the `main` branch.

### Step 2: Frontend Setup (Node.js)

```bash
# Install all frontend dependencies
npm install

# This will install ~300+ packages including:
# - Svelte 5
# - SvelteKit 2
# - Vite 5
# - TypeScript
# - Tailwind CSS 4
# - TipTap (rich text editor)
# - And many more...
```

**Expected time:** 2-5 minutes

**Troubleshooting:**
- ❌ **"npm not found"** → Install Node.js first
- ❌ **Permission errors** → Don't use `sudo npm install`
- ❌ **"EACCES" errors** → Fix npm permissions: [npm docs](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)

### Step 3: Backend Setup (Python)

#### Create a Virtual Environment

**Why?** Isolates project dependencies from system Python.

```bash
# Create virtual environment
python -m venv venv

# Activate it
# On macOS/Linux:
source venv/bin/activate

# On Windows (Command Prompt):
venv\Scripts\activate.bat

# On Windows (PowerShell):
venv\Scripts\Activate.ps1
```

**✅ Verification:** Your terminal prompt should now show `(venv)` at the beginning.

#### Install Dependencies

```bash
# Install Open WebUI in editable mode (development)
pip install -e .

# This installs:
# - FastAPI
# - SQLAlchemy 2.0
# - LangChain 0.3 (⚠️ deprecated, v1.0 available)
# - ChromaDB
# - Transformers
# - And 100+ other packages...
```

**Expected time:** 3-10 minutes (depending on internet speed)

**Troubleshooting:**
- ❌ **"python: command not found"** → Try `python3` instead
- ❌ **Wrong Python version** → Use `python3.11` or `python3.12` explicitly
- ❌ **Compilation errors** → May need build tools:
  - **macOS:** Install Xcode Command Line Tools: `xcode-select --install`
  - **Linux:** Install build essentials: `sudo apt-get install build-essential python3-dev`
  - **Windows:** Install Visual Studio Build Tools

### Step 4: Optional Dependencies

#### Redis (Recommended for Sessions)

Redis is used for session storage and caching.

**macOS:**
```bash
brew install redis
brew services start redis
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt-get install redis-server
sudo systemctl start redis
```

**Windows:**
- Use Docker: `docker run -d -p 6379:6379 redis`
- Or install from [GitHub releases](https://github.com/tporadowski/redis/releases)

#### Ollama (Recommended for Local LLMs)

Ollama provides local LLM inference.

```bash
# Install Ollama (see https://ollama.ai)
# Then pull a model
ollama pull llama2
```

---

## First Run

### Start the Backend Server

**Terminal 1:**
```bash
# Activate virtual environment
source venv/bin/activate  # Windows: venv\Scripts\activate

# Run the backend
python backend/open_webui/main.py

# Alternative: Use uvicorn directly
cd backend
uvicorn open_webui.main:app --reload --port 8080
```

**Expected output:**
```
INFO:     Will watch for changes in these directories: ['/path/to/open-webui/backend']
INFO:     Uvicorn running on http://127.0.0.1:8080 (Press CTRL+C to quit)
INFO:     Started reloader process [12345] using WatchFiles
INFO:     Started server process [12346]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

**✅ Quick Check:** Visit http://localhost:8080/health
- Should show: `{"status": "ok"}`

### Start the Frontend Server

**Terminal 2:**
```bash
# Start Vite dev server
npm run dev

# OR specify a different port:
npm run dev:5050
```

**Expected output:**
```
  VITE v5.4.14  ready in 1234 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```

**✅ Quick Check:** Visit http://localhost:5173
- Should show: Open WebUI login/signup page

### Create Your First Account

1. Visit http://localhost:5173
2. Click "Sign Up"
3. Create an account (email + password)
4. **Important:** The first account created becomes the admin!

🎯 **Remember This:** First user = Admin. Subsequent users are regular users.

---

## Making Your First Change

Let's make a simple change to verify your setup works!

### Frontend Change (Svelte)

**File:** `src/routes/(app)/+page.svelte`

**Find:**
```svelte
<div class="welcome-message">
  Welcome to Open WebUI
</div>
```

**Change to:**
```svelte
<div class="welcome-message">
  Welcome to Open WebUI - I'm developing! 🚀
</div>
```

**Save the file.** The browser should automatically reload (hot module replacement)!

**✅ Quick Check:** Do you see your change instantly in the browser?

### Backend Change (Python)

**File:** `backend/open_webui/main.py`

**Find the health check endpoint** (around line 200):
```python
@app.get("/health")
async def healthcheck():
    return {"status": "ok"}
```

**Change to:**
```python
@app.get("/health")
async def healthcheck():
    return {"status": "ok", "message": "I'm developing!"}
```

**Save the file.** Uvicorn should automatically reload!

**✅ Quick Check:** Visit http://localhost:8080/health
- Should show: `{"status": "ok", "message": "I'm developing!"}`

### 💡 Aha Moment

**Both servers have hot-reloading!**
- **Frontend:** Vite watches `.svelte`, `.ts`, `.js` files
- **Backend:** Uvicorn watches `.py` files with `--reload` flag

No need to manually restart servers during development! 🎉

---

## Understanding the Development Workflow

### File Changes → See Results

```
1. Edit .svelte file
   └─> Vite detects change
       └─> Browser hot-reloads (instant!)

2. Edit .py file
   └─> Uvicorn detects change
       └─> Server restarts (2-3 seconds)
```

### Frontend → Backend Communication

When developing, the frontend proxies API requests to the backend:

```
Browser Request: http://localhost:5173/api/chats
        ↓
Vite Proxy (vite.config.ts)
        ↓
Backend API: http://localhost:8080/api/chats
```

**📍 Configuration:** See [vite.config.ts](../../vite.config.ts) for proxy settings

---

## Project Scripts

### Frontend (npm)

```bash
# Development server
npm run dev                # Start on default port (5173)
npm run dev:5050          # Start on port 5050

# Build for production
npm run build             # Build static files
npm run build:watch       # Build and watch for changes

# Code quality
npm run lint              # Lint all code (frontend + backend)
npm run lint:frontend     # Lint only frontend
npm run lint:types        # Type checking
npm run format            # Format code with Prettier
npm run check             # Svelte type checking

# Testing
npm run test:frontend     # Run Vitest tests
npm run cy:open          # Open Cypress E2E tests

# Internationalization
npm run i18n:parse        # Extract translation strings
```

### Backend (Python)

```bash
# Development server
python backend/open_webui/main.py

# Code quality
npm run lint:backend      # Lint Python code (pylint)
npm run format:backend    # Format with Black

# Database migrations (from project root)
alembic -c backend/open_webui/alembic.ini upgrade head      # Apply migrations
alembic -c backend/open_webui/alembic.ini revision -m "msg" # Create new migration
```

---

## Environment Variables

### Frontend (.env)

Create `.env` in project root:

```bash
# Public variables (exposed to browser)
PUBLIC_API_BASE_URL=http://localhost:8080

# Build-time variables
VITE_BUILD_SOURCEMAP=true
```

### Backend Environment

The backend reads from:
1. Environment variables
2. `.env` file in backend directory
3. Database config (once running)

**Common variables:**
```bash
# Database
DATABASE_URL=sqlite:///./webui.db

# Security
WEBUI_SECRET_KEY=your-secret-key-here

# Redis (optional)
REDIS_URL=redis://localhost:6379

# LLM Providers
OLLAMA_BASE_URL=http://localhost:11434
OPENAI_API_KEY=sk-...
```

**⚠️ Security Note:** Never commit real API keys to Git!

---

## Troubleshooting

### Common Issues

#### ❌ "Cannot connect to backend"

**Symptoms:** Frontend loads but API calls fail

**Solutions:**
1. Check backend is running on port 8080
2. Check [vite.config.ts](../../vite.config.ts) proxy configuration
3. Check browser console for CORS errors
4. Verify: http://localhost:8080/health returns JSON

#### ❌ "Module not found" errors

**Frontend:**
```bash
# Clear node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
```

**Backend:**
```bash
# Reinstall in virtual environment
source venv/bin/activate
pip uninstall open-webui
pip install -e .
```

#### ❌ Database issues

```bash
# Reset database (WARNING: Deletes all data!)
rm backend/webui.db
python backend/open_webui/main.py  # Recreates database
```

#### ❌ Port already in use

```bash
# Frontend (5173)
lsof -ti:5173 | xargs kill -9

# Backend (8080)
lsof -ti:8080 | xargs kill -9

# Windows:
netstat -ano | findstr :5173
taskkill /PID <PID> /F
```

#### ❌ Python version issues

```bash
# Use specific Python version
python3.11 -m venv venv
# OR
python3.12 -m venv venv
```

### Getting Help

- 📖 **Documentation:** [docs.openwebui.com](https://docs.openwebui.com/)
- 💬 **Discord:** [discord.gg/5rJgQTnV4s](https://discord.gg/5rJgQTnV4s)
- 🐛 **Issues:** [GitHub Issues](https://github.com/open-webui/open-webui/issues)
- 📚 **More Guides:** [TROUBLESHOOTING.md](../../TROUBLESHOOTING.md)
- ❓ **FAQ:** [FAQ.md](./FAQ.md)

---

## Next Steps

Congratulations! You have Open WebUI running locally. 🎉

**Recommended next steps:**

1. 📖 **Read:** [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md)
   - Understand how the pieces fit together

2. 🗺️ **Explore:** [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md)
   - Learn where everything lives

3. 🧠 **Learn:** [TECH_STACK_GUIDE.md](./TECH_STACK_GUIDE.md)
   - Deep dive into Svelte 5, FastAPI, and more

4. 🎯 **Choose your path:**
   - **Frontend:** [FRONTEND_ARCHITECTURE.md](./FRONTEND_ARCHITECTURE.md)
   - **Backend:** [BACKEND_ARCHITECTURE.md](./BACKEND_ARCHITECTURE.md)
   - **AI/ML:** [INTEGRATION_GUIDE.md](./INTEGRATION_GUIDE.md)

5. 🛠️ **Build:** [HOW_TO_GUIDE.md](./HOW_TO_GUIDE.md)
   - Add your first feature

6. ✅ **Test:** [TESTING_GUIDE.md](./TESTING_GUIDE.md)
   - Write tests for your changes

---

## Development Tips

### ⚡ Speed up Development

**Use hot-reload effectively:**
- Keep both terminal windows visible
- Watch for errors in terminal output
- Use browser DevTools for frontend debugging

**Faster rebuilds:**
```bash
# Frontend: Vite's HMR is already fast
# Backend: Use --reload-dir to watch specific directories
uvicorn open_webui.main:app --reload --reload-dir open_webui/routers
```

### 🔍 Debugging

**Frontend:**
- Use Chrome DevTools
- Svelte DevTools extension
- Console.log is your friend

**Backend:**
- Use Python debugger: `import pdb; pdb.set_trace()`
- Or modern debugpy for VS Code
- Check logs in terminal

**See:** [DEBUGGING_GUIDE.md](./DEBUGGING_GUIDE.md) for detailed debugging strategies

### 📝 Code Style

**Frontend:**
```bash
# Auto-format before committing
npm run format
npm run lint:frontend
```

**Backend:**
```bash
# Auto-format Python code
npm run format:backend
npm run lint:backend
```

**See:** [PATTERNS_AND_CONVENTIONS.md](./PATTERNS_AND_CONVENTIONS.md) for code style guide

---

## Summary Checklist

Before moving on, ensure you can:

- [ ] ✅ Run backend server (`python backend/open_webui/main.py`)
- [ ] ✅ Run frontend server (`npm run dev`)
- [ ] ✅ Access http://localhost:5173 in browser
- [ ] ✅ Create an account and log in
- [ ] ✅ Make a change to frontend and see it hot-reload
- [ ] ✅ Make a change to backend and see it reload
- [ ] ✅ Understand the two-server architecture
- [ ] ✅ Know where to get help (Discord, docs, GitHub)

---

**Need help?** Join our [Discord](https://discord.gg/5rJgQTnV4s) or check the [FAQ](./FAQ.md)!

**Ready to learn more?** Continue to [ARCHITECTURE_OVERVIEW.md](./ARCHITECTURE_OVERVIEW.md) →

---

**Last updated:** November 18, 2025
**Tech stack research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)
