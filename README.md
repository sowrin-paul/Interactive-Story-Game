# Interactive Story Game

An AI-powered choose-your-own-adventure web application. Enter a theme, and the app uses OpenAI (via LangChain) to generate a fully branching story. Navigate through story nodes, make choices, and reach winning or losing endings — all stored in a database so stories can be replayed at any time.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Setup & Installation](#setup--installation)
  - [Backend](#backend)
  - [Frontend](#frontend)
- [Environment Variables](#environment-variables)
- [Running the Application](#running-the-application)
- [API Overview](#api-overview)
- [Contributing](#contributing)

---

## Features

- **AI Story Generation** — Generates branching choose-your-own-adventure stories using OpenAI GPT-4 via LangChain.
- **Theme-Based Stories** — Users enter a custom theme (e.g., "fantasy", "space horror", "detective noir") to shape the generated story.
- **Branching Narrative** — Each story has 3–4 levels of depth with multiple paths, 2–3 options per node.
- **Multiple Endings** — Stories contain both winning and losing endings across different paths.
- **Background Job Processing** — Story generation runs as a background task; the frontend polls for completion status.
- **Persistent Storage** — Stories and their node trees are stored in a relational database using SQLAlchemy.
- **Story Replay** — Generated stories can be reloaded at any time via their unique ID.
- **REST API** — FastAPI backend with interactive Swagger docs at `/docs`.

---

## Tech Stack

| Layer     | Technology                                      |
|-----------|-------------------------------------------------|
| Frontend  | React 19, Vite, React Router v7, Axios          |
| Backend   | Python 3.13+, FastAPI, Uvicorn                  |
| AI        | LangChain, LangChain-OpenAI, OpenAI GPT-4 Turbo |
| Database  | SQLAlchemy 2.x (SQLite by default / PostgreSQL) |
| Config    | pydantic-settings, python-dotenv                |

---

## Project Structure

```
Interactive-Story-Game/
├── backend/
│   ├── core/
│   │   ├── config.py          # App settings (pydantic-settings)
│   │   ├── models.py          # Pydantic models for LLM responses
│   │   ├── prompts.py         # System prompts for story generation
│   │   └── story_generator.py # LangChain story generation logic
│   ├── db/
│   │   └── database.py        # SQLAlchemy engine & session setup
│   ├── models/
│   │   ├── jobs.py            # StoryJob ORM model
│   │   └── story.py           # Story & StoryNode ORM models
│   ├── routers/
│   │   ├── jobs.py            # Job status endpoints
│   │   └── story.py           # Story create & retrieve endpoints
│   ├── schemas/
│   │   ├── jobs.py            # Job response schemas
│   │   └── story.py           # Story response schemas
│   ├── main.py                # FastAPI app entry point
│   ├── pyproject.toml         # Python project metadata & dependencies
│   └── requirements.txt       # Pinned dependencies
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── LoadingStatus.jsx   # Loading indicator during generation
│   │   │   ├── StoryGame.jsx       # Interactive story navigation
│   │   │   ├── StoryGenerator.jsx  # Theme input & job polling
│   │   │   ├── StoryLoader.jsx     # Loads a story by ID from the API
│   │   │   └── ThemeInput.jsx      # Theme entry form
│   │   ├── App.jsx            # Root component with routing
│   │   ├── main.jsx           # React entry point
│   │   └── utils.js           # Shared constants (API base URL)
│   ├── index.html
│   ├── package.json
│   └── vite.config.js
└── README.md
```

---

## Prerequisites

- **Node.js** 18+ and **npm** (for the frontend)
- **Python** 3.13+ and **uv** or **pip** (for the backend)
- An **OpenAI API key** with access to `gpt-4-turbo`
- A relational database (SQLite works out of the box; PostgreSQL requires `psycopg2-binary`)

---

## Setup & Installation

### Backend

1. **Navigate to the backend directory:**
   ```bash
   cd backend
   ```

2. **Create and activate a virtual environment:**
   ```bash
   python -m venv .venv
   source .venv/bin/activate   # Windows: .venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   # or, using uv:
   uv sync
   ```

4. **Create a `.env` file** in the `backend/` directory (see [Environment Variables](#environment-variables)).

### Frontend

1. **Navigate to the frontend directory:**
   ```bash
   cd frontend
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

---

## Environment Variables

Create a `.env` file inside the `backend/` directory with the following variables:

```env
# Required — OpenAI API key
OPENAI_API_KEY=sk-...

# Required — SQLAlchemy database connection URL
# SQLite (local file, no extra setup):
DATABASE_URL=sqlite:///./database.db
# PostgreSQL example:
# DATABASE_URL=postgresql://user:password@localhost:5432/storydb

# Optional — comma-separated list of allowed CORS origins
ALLOWED_ORIGINS=http://localhost:5173

# Optional — toggles debug mode (default: false)
DEBUG=false
```

---

## Running the Application

### Start the Backend

```bash
cd backend
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at `http://localhost:8000`.  
Interactive API docs are available at `http://localhost:8000/docs`.

### Start the Frontend

```bash
cd frontend
npm run dev
```

The React app will be available at `http://localhost:5173`.

> **Note:** The frontend is pre-configured to proxy API calls to `/choreo-apis/choose-your-own-adventure/backend/v1/api` for Choreo deployments. For local development, update `src/utils.js` to point to `http://localhost:8000/api`.

---

## API Overview

| Method | Endpoint                     | Description                               |
|--------|------------------------------|-------------------------------------------|
| POST   | `/api/stories/create`        | Start generating a new story (background) |
| GET    | `/api/stories/{story_id}/complete` | Retrieve full story tree by ID      |
| GET    | `/api/jobs/{job_id}`         | Poll the status of a story generation job |

Full interactive documentation is available via Swagger UI at `/docs` and ReDoc at `/redoc` when the backend is running.

---

## Contributing

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/my-feature`.
3. Commit your changes: `git commit -m "Add my feature"`.
4. Push to the branch: `git push origin feature/my-feature`.
5. Open a Pull Request.