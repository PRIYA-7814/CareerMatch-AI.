# CareerMatch AI

**A local-first resume and job matching workspace with explainable skill evidence.** Save a resume, compare it with a job description, and review the matching phrases and suggested next steps—all without creating an account.

> **Current implementation:** the matcher is a deterministic phrase-matching baseline, not a generative AI model, semantic similarity system, or hiring decision tool. The optional Supabase foundation in this repository is not required by the current browser workflow.

## What it does

- Saves PDF and DOCX resume files in the current browser (maximum 10 MB).
- Saves job descriptions, analysis history, and profile preferences in browser storage.
- Extracts text through a local FastAPI service and compares supported skill phrases.
- Returns an overall coverage percentage plus matched/missing skills and evidence snippets.
- Shows history, curated learning links, starter projects, and interview practice prompts.
- Requires no account, hosted database, API key, or cloud service for the local workflow.

## Run locally

Requirements: Node.js 20.19+ or 22.12+, npm, and Python 3.11+.

Open the project in VS Code and start two integrated terminals.

**Terminal 1 — API**

```powershell
cd backend
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8001
```

**Terminal 2 — web app**

```powershell
cd frontend
npm ci
npm run dev
```

Open the Vite URL printed in the terminal, normally [http://localhost:5173](http://localhost:5173). Keep both processes running while using the app. The API health check is [http://localhost:8001/health](http://localhost:8001/health), and its interactive API docs are at [http://localhost:8001/docs](http://localhost:8001/docs).

## How analysis works

1. Choose a saved resume and add a job title and job description (40–30,000 characters).
2. The browser sends the selected resume to the local API when analysis is requested.
3. The API extracts PDF text with `pypdf` or DOCX text from its XML document body.
4. A curated skill vocabulary detects phrases in the job description and checks for those phrases in the resume.
5. The API returns a score and evidence; the browser saves the result locally.

The score is `matched detected skills / total detected job skills × 100`, with equal weight for each detected skill. The analyzer can miss synonyms and context. Scanned image PDFs are not supported because OCR is not implemented. See [Architecture](docs/ARCHITECTURE.md) for the data flow and [API reference](docs/API.md) for endpoint details.

## Data and privacy

Resume files are stored in IndexedDB. Jobs, results, and profile preferences are stored in localStorage. Data stays with the current browser profile and device; it is not backed up or synchronized. Clearing the browser’s site data removes the local workspace. During analysis, the resume is sent to the API running on the same device; the guest endpoint does not persist it on the server.

## Project structure

```text
backend/       FastAPI app, document extraction, matcher, and tests
frontend/      React + Vite browser application
supabase/      Optional SQL schema, policies, and curated seed data
docs/          Architecture, API, and development documentation
```

## Validate changes

```powershell
cd frontend
npm run build
npm run lint
cd ..\backend
.\.venv\Scripts\python.exe -m pytest
```

The backend tests cover the health/status routes, protected profile boundary, guest DOCX analysis, and unsupported file rejection. There is no separate frontend test script configured.

## Optional Supabase foundation

The repository retains Supabase Auth verification, protected profile/analysis endpoints, SQL migrations, row-level security policies, a private resume bucket, and seed data for possible future cloud sync. These are not required for the current no-login local app. Details and feature status are documented in [Architecture](docs/ARCHITECTURE.md).

## Current boundaries

- Phrase matching is transparent but less capable than semantic or model-based matching.
- The skill vocabulary and curated resource/project catalogs are intentionally small.
- No OCR, cloud sync, remote backup, or cross-device history is implemented in the local flow.
- Results are informational and should be checked against the original resume and job description.

## Documentation

- [Project architecture and feature status](docs/ARCHITECTURE.md)
- [API reference](docs/API.md)
- [Development and validation guide](docs/DEVELOPMENT.md)
