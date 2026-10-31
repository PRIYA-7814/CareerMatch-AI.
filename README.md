# CareerMatch AI

![CareerMatch AI banner](docs/assets/careermatch-banner.svg)

[![React](https://img.shields.io/badge/React-19-149eca?logo=react&logoColor=white)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-8-646CFF?logo=vite&logoColor=white)](https://vite.dev/)
[![FastAPI](https://img.shields.io/badge/FastAPI-API-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Python](https://img.shields.io/badge/Python-3.11%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
![Local-first](https://img.shields.io/badge/Mode-Local--first-3978a8)

**CareerMatch AI helps job seekers compare a resume with a job description and see which skill phrases match.** It runs locally, requires no account, and provides evidence to help users review the result.

> **How matching works:** The current analyzer uses deterministic phrase matching and a curated skill vocabulary. It is not a generative AI or semantic matching system, and its results are not a hiring decision.

## Features

- Add and manage PDF or DOCX resumes in the browser.
- Compare a resume with a job description.
- Review matched and missing skill phrases with text evidence.
- See a coverage score based on detected job skill phrases.
- Keep resumes in browser IndexedDB and jobs, results, and preferences in localStorage.
- Explore analysis history, learning resources, project ideas, and interview prompts.
- Use the local workflow without an account, API key, or hosted database.

## Technology

| Area | Technologies |
| --- | --- |
| Frontend | React 19, Vite 8, React Router, Tailwind CSS, Lucide React |
| Backend | Python 3.11+, FastAPI, Uvicorn |
| Resume extraction | pypdf for PDF; Python ZIP/XML processing for DOCX |
| Browser storage | IndexedDB for resume files; localStorage for jobs, results, and preferences |
| Optional cloud foundation | Supabase Auth, SQL schema, and row-level security |

The current browser workflow uses the local API. Supabase components are included as optional groundwork and are not required to run the app.

## Run locally

### Requirements

- Node.js 20.19+ or 22.12+
- npm
- Python 3.11+
- VS Code (recommended) or another editor

### 1. Start the backend

Open a terminal in the project folder:

```powershell
cd backend
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8001
```

### 2. Start the frontend

Open a second terminal in the project folder:

```powershell
cd frontend
npm ci
npm run dev
```

Open the URL printed by Vite, usually [http://localhost:5173](http://localhost:5173). Keep both terminals running while using the app.

- API health check: [http://localhost:8001/health](http://localhost:8001/health)
- Interactive API documentation: [http://localhost:8001/docs](http://localhost:8001/docs)

## How the match score works

1. The user selects a resume and enters a job title and description.
2. The browser sends the resume and job details to the local FastAPI service.
3. The API extracts text from the PDF or DOCX file.
4. The analyzer detects supported skill phrases in the job description and looks for those phrases in the resume.
5. It returns matched and missing phrases, evidence, and a coverage score.

The score is calculated as:

```text
matched detected job skills / total detected job skills × 100
```

Each detected skill phrase has equal weight. The analyzer may miss synonyms, equivalent skills, or context. Scanned image PDFs are not supported because OCR is not implemented.

## Data and privacy

Resume files are stored in IndexedDB in the current browser profile. Jobs, analysis results, and profile preferences are stored in localStorage. Data is not synchronized or backed up. Clearing the browser’s site data removes the local workspace.

When analysis runs, the resume is sent to the API on the same device. The guest analysis endpoint does not persist the resume on the server.

## Project structure

```text
backend/       FastAPI application, document extraction, matching logic, and tests
frontend/      React and Vite web application
supabase/      Optional SQL schema, policies, and seed data
docs/          Architecture, API, and development guides
```

## Validation

From the project root, run:

```powershell
cd frontend
npm run build
npm run lint
cd ..\backend
.\.venv\Scripts\python.exe -m pytest
```

## Current limitations

- Phrase matching is transparent, but does not understand meaning like a semantic model.
- The skill vocabulary and curated resources are limited.
- OCR, cloud sync, remote backup, and cross-device history are not part of the local workflow.
- The score is informational. Review the resume and job description yourself.

## Documentation

- [Architecture and feature status](docs/ARCHITECTURE.md)
- [API reference](docs/API.md)
- [Development guide](docs/DEVELOPMENT.md)
