# Kurim Mystery - Interactive AI Detective Game

[![Zobrazit README v češtině](https://img.shields.io/badge/%F0%9F%87%A8%F0%9F%87%BF_%C4%8Cesky-Zobrazit_README_v_%C4%8CJ-2563EB?style=for-the-badge)](README.cs.md)

This project was created as a high school graduation (*maturita*) project for the Operating Systems course.

![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.115-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-2.0-D71F00?style=for-the-badge&logo=sqlalchemy&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI_API-Compatible-412991?style=for-the-badge&logo=openai&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-UI-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)

**Kurim Mystery** is a full-featured web-based conversational detective game powered by Large Language Models (LLMs). The player takes on the role of an investigator and interrogates three suspects in real time in the murder case of the founder of the tech startup **Nexus AI**. Each character has their own personality, backstory, motive, alibi, and secrets defined by a detailed system prompt, and remembers the entire context of the ongoing interrogation in a PostgreSQL database.

---

## Application Screenshots

### Main Interrogation Room
![Interrogation Room](static/screenshots/interrogation.png)

| Victim Information | Suspect Profile | Accusation Evaluation (Verdict) |
| :---: | :---: | :---: |
| ![About the Victim](static/screenshots/victim_modal.png) | ![Character Profile](static/screenshots/profile_modal.png) | ![Victory](static/screenshots/verdict_victory.png) |

---

## 📖 Story: *Death in the Cloud*

> **Crime Scene:** Offices and server room of the **Nexus AI** startup  
> **Victim:** **Viktor Černý** (CEO) — found dead at night in the server room, strangled with an Ethernet cable.  
> **Case Background:** Viktor Černý built a successful AI startup, but shortly before his death he announced a plan to sell the entire company to a large corporation and lay off most of the team after the acquisition. Everyone present had a strong motive.

### Suspects

| ID | Name | Role at the Company | Characteristics & Motive |
| :--- | :--- | :--- | :--- |
| `marek` | **Marek Kříž** | Co-founder & CTO | Founded the company with Viktor 10 years ago in a garage. Selling the company would destroy his life's work. Cold, technical, references an alibi in the Git commit log (`23:15`). |
| `jana` | **Jana Horáková** | Lead Developer | Has been at the company for 7 years since her internship. Viktor severely overworked her, forced her to work nights, and denied her promised equity. Burned out, speaks in code metaphors. |
| `petr` | **Petr Vacek** | Seed Investor | Invested all his capital into Nexus AI (Viktor owed him 5 million CZK and was spending money on luxury cars). Hot-tempered, focused on money and ROI, waited outside the building at night. |

---

## Key Features

- **Dynamic AI Interrogations in Natural Language:** Characters respond in Czech in the first person without distracting roleplay notes, stay in character, and are aware of their mutual relationships.
- **Persistent Conversation Memory:** The entire message history for each character is stored in a PostgreSQL relational database and passed to the LLM as context on every subsequent query.
- **Outage Resilience (Graceful Fallback):** If the AI endpoint is unavailable or an API key error occurs, the application does not crash; instead, it returns a safe fallback message and informs the user in the status bar.
- **Automatic Seeding and Schema Migration:** On application startup (`on_startup`), it automatically connects to the database (with a retry mechanism), creates tables, applies backward-compatible column updates, and initializes the suspects.
- **Final Verdict System and Game Auto-Reset:** The player can make a final accusation at any time (`/verdict`). The application evaluates whether the true culprit was identified, displays a dramatic verdict, and automatically resets the game for a new round.
- **Modern Responsive UI:** Dark detective-themed design built with **Tailwind CSS**, featuring a typing indicator (*„Marek Kříž píše...“* / *"Marek Kříž is typing..."*), interrogation room switching, and modal windows.

---

## Project Structure

```text
AiDetectiveFull/
├── app/
│   ├── ai_client.py      # Initialization of the asynchronous OpenAI client (AsyncOpenAI)
│   ├── config.py         # Application configuration and ENV variable loading via Pydantic Settings
│   ├── db.py             # SQLAlchemy 2.0 ORM models (GameSession, Character, Message) and init_db()
│   ├── main.py           # FastAPI application, routes, data seeding, and game logic
│   └── prompts.py        # Story context, AI behavior rules, and character system prompts
├── static/
│   ├── screenshots/      # UI screenshots
│   └── placeholder.txt   # Keeps the static directory tracked in version control
├── templates/
│   └── index.html        # Complete frontend (Jinja2 template, Tailwind CSS, Vanilla JS)
├── Dockerfile            # Production image definition (Python 3.12-slim + uvicorn)
├── compose.yml           # Container orchestration (FastAPI app + PostgreSQL 16 with healthcheck)
└── requirements.txt      # List of Python dependencies
```

---

## 🚀 Running the Project

The project supports both the **OpenAI API** and any **OpenAI-compatible server** (e.g., a school/self-hosted Ollama, vLLM, or LiteLLM instance running the `gemma3:27b` model).

### Option 1: Running with Docker Compose (Recommended)

1. **Clone the repository:**
   ```bash
   git clone https://github.com/nedo44/AiDetectiveFull.git
   cd AiDetectiveFull
   ```

2. **Configure environment variables:**
   Create a `.env` file in the root directory (or pass the variables in your terminal):
   ```env
   OPENAI_API_KEY=your_api_key
   OPENAI_BASE_URL=https://kurim.ithope.eu/v1
   OPENAI_MODEL=gemma3:27b
   ```
   > **Tip:** If you want to use the official OpenAI API, set `OPENAI_BASE_URL=https://api.openai.com/v1` and, for example, `OPENAI_MODEL=gpt-4o-mini`. If you are using a local **Ollama** instance, set `OPENAI_BASE_URL=http://host.docker.internal:11434/v1` and any `OPENAI_API_KEY=ollama`.

3. **Start the containers:**
   ```bash
   docker compose up --build
   ```

4. **Open the application in your browser:**
   Navigate to **[http://localhost:8000](http://localhost:8000)**.

---

### Option 2: Local Development Setup (without Docker)

1. **Create and activate a virtual environment:**
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate  # On Windows: .venv\Scripts\activate
   pip install -r requirements.txt
   ```

2. **Create a `.env` file in the root directory:**
   ```env
   DATABASE_URL=postgresql+psycopg://kurim:kurim_password@localhost:5432/kurim_mystery
   OPENAI_API_KEY=your_api_key
   OPENAI_BASE_URL=https://kurim.ithope.eu/v1
   OPENAI_MODEL=gemma3:27b
   APP_TITLE=Kurim Mystery
   ```

3. **Start the Uvicorn development server:**
   ```bash
   uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
   ```

---

## Configuration Variables (`.env`)

| Variable | Default Value | Description |
| :--- | :--- | :--- |
| `DATABASE_URL` | *(required)* | PostgreSQL database connection string (`postgresql+psycopg://...`) |
| `OPENAI_API_KEY` | *(required)* | API key for authenticating with the AI server |
| `OPENAI_BASE_URL` | `https://kurim.ithope.eu/v1` | URL of the OpenAI-compatible API endpoint |
| `OPENAI_MODEL` | `gemma3:27b` | Name of the language model used for generating responses |
| `APP_TITLE` | `Kurim Mystery` | Application title displayed in the header and page title |
| `PORT` | `8000` | Port on which the Uvicorn server listens inside the Docker container |

---

## REST API Endpoints Overview

FastAPI automatically generates interactive OpenAPI documentation available at `/docs` (Swagger UI) and `/redoc`.

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/` | Renders the main HTML game interface (`templates/index.html`) with the list of suspects. |
| `GET` | `/api/state` | Returns JSON with the current game session, list of characters, and active suspect. |
| `GET` | `/api/profile/{suspect_id}` | Returns the detailed profile and description of the selected character (`marek`, `jana`, `petr`). |
| `GET` | `/api/history/{suspect_id}` | Returns the chronologically ordered interrogation history of the given character. |
| `POST` | `/api/chat` | Accepts form data (`suspect_id`, `message`), saves the query to the DB, calls the LLM with full history, and saves the response. |
| `POST` | `/verdict` | Accepts JSON `{"accused_name": "..."}`, evaluates the accusation, returns the result, and resets the game for a new round. |

---

## Technologies Used

- **Backend:** [FastAPI](https://fastapi.tiangolo.com/), [Uvicorn](https://www.uvicorn.org/), Python 3.12
- **Database & ORM:** [PostgreSQL 16](https://www.postgresql.org/), [SQLAlchemy 2.0](https://www.sqlalchemy.org/), `psycopg` (v3)
- **AI Integration:** [OpenAI Python SDK](https://github.com/openai/openai-python) (`AsyncOpenAI`), Pydantic Settings
- **Frontend:** HTML5, [Jinja2](https://jinja.palletsprojects.com/), [Tailwind CSS](https://tailwindcss.com/), Vanilla JavaScript (ES6+)
- **Infrastructure:** Docker, Docker Compose
