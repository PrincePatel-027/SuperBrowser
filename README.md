# SuperBrowser

An AI-powered desktop browser that combines multi-engine search, community insights, and a built-in AI assistant. The desktop app is an Electron shell wrapping a Vite + React frontend, backed by a FastAPI service.

Last updated: August 3, 2026

## Architecture

- *Frontend* — React 19 + Vite 8, rendered inside an Electron 28 window.
- *Backend* — FastAPI served by Uvicorn on http://127.0.0.1:8000.
- *Auto-start* — In development and in the packaged app, Electron launches the backend for you via startBackend() in frontend/electron/main.cjs (python -m uvicorn main:app). You do not need to start the backend separately.

Backend capabilities:
- *SuperSEO* search across Google, Bing, and DuckDuckGo (routers/seo.py).
- *Community* results and summarization from Reddit, Hacker News, Stack Exchange, and Dev.to (routers/community.py).
- *AI* assistant and personas powered by Groq (routers/ai.py, services/groq_service.py).
- *Context* session tracking (routers/context.py).

## Prerequisites

- Windows
- Python 3.13
- Node.js (with npm)

## Environment variables (backend)

Create a .env file inside backend/ with:


SERPAPI_API_KEY=your_serpapi_key
GROQ_API_KEY=your_groq_key


SERPAPI_API_KEY (or SERP_API_KEY) powers SuperSEO for Google, Bing, and DuckDuckGo. If any SerpAPI engine fails or returns no results, SuperSEO falls back to the matching built-in scraper immediately.

GROQ_API_KEY powers the AI assistant, personas, and community summarization.

## First-time setup

Install backend dependencies (once):

powershell
cd backend
pip install -r requirements.txt


Install frontend dependencies (once):

powershell
cd frontend
npm install


## Running the app

From the frontend/ folder, start everything with a single command:

powershell
cd frontend
npm run dev:electron


This runs the Vite dev server on http://localhost:5173, then launches the Electron window, which auto-starts the FastAPI backend on http://127.0.0.1:8000. Closing the window shuts the backend down as well.

### Running pieces individually

Frontend dev server only (browser, no Electron):

powershell
cd frontend
npm run dev


Backend only (manual):

powershell
cd backend
uvicorn main:app --reload --host 0.0.0.0 --port 8000


If port 8000 is already in use, find and stop the process:

powershell
Get-NetTCPConnection -LocalPort 8000 | Select-Object -ExpandProperty OwningProcess | ForEach-Object { Stop-Process -Id $_ -Force }


## Building the desktop app

powershell
cd frontend
npm run build       # build the frontend
npm run dist:win    # package a Windows NSIS installer into frontend/release


The packaged build bundles the backend/ folder as an extra resource so the app can run the backend on the user's machine.
