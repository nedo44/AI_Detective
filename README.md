#Kurim Mystery - Interaktivní AI Detektivka

Tento projekt byl vytvořen jako maturitní projekt do předmětu operační systémy.

![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.115-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-2.0-D71F00?style=for-the-badge&logo=sqlalchemy&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI_API-Compatible-412991?style=for-the-badge&logo=openai&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-UI-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)

**Kurim Mystery** je plnohodnotná webová konverzační detektivní hra poháněná velkými jazykovými modely (LLM). Hráč se ujímá role vyšetřovatele a v reálném čase vyslýchá trojici podezřelých v případu vraždy zakladatele technologického startupu **Nexus AI**. Každá postava má vlastní osobnost, historii, motiv, alibi i tajemství definované propracovaným systémovým promptem a pamatuje si celý kontext probíhajícího výslechu v databázi PostgreSQL.

---

## 📸 Ukázky z aplikace

### Hlavní výslechová místnost
![Výslechová místnost](static/screenshots/interrogation.png)

| Informace o oběti | Profil podezřelého | Vyhodnocení obvinění (Rozsudek) |
| :---: | :---: | :---: |
| ![O oběti](static/screenshots/victim_modal.png) | ![Profil postavy](static/screenshots/profile_modal.png) | ![Vítězství](static/screenshots/verdict_victory.png) |

---

## 📖 Příběh: *Smrt v cloudu*

> **Místo činu:** Kanceláře a serverovna startupu **Nexus AI**  
> **Oběť:** **Viktor Černý** (CEO) — nalezen mrtvý v noci v serverovně, uškrcený ethernetovým kabelem.  
> **Pozadí případu:** Viktor Černý vybudoval úspěšný AI startup, ale krátce před smrtí oznámil plán prodat celou společnost velké korporaci a po akvizici propustit většinu týmu. Každý z přítomných měl silný motiv.

### 👥 Podezřelí

| ID | Jméno | Role ve firmě | Charakteristika a motiv |
| :--- | :--- | :--- | :--- |
| `marek` | **Marek Kříž** | Spoluzakladatel & CTO | S Viktorem firmu před 10 lety založil v garáži. Prodej firmy by zničil jeho životní práci. Chladný, technický, odkazuje se na alibi v Git commit logu (`23:15`). |
| `jana` | **Jana Horáková** | Hlavní vývojářka | Ve firmě je 7 let od dob stáže. Viktor ji extrémně přetěžoval, nutil pracovat po nocích a upíral jí slíbený podíl. Vyhořelá, mluví v metaforách o kódu. |
| `petr` | **Petr Vacek** | Seed investor | Do Nexus AI vložil veškerý svůj kapitál (Viktor mu dlužil 5 milionů Kč a peníze utrácel za luxusní auta). Výbušný, orientovaný na peníze a ROI, v noci čekal před budovou. |

---

## ✨ Klíčové funkce

- **Dynamické AI výslechy v přirozeném jazyce:** Postavy odpovídají česky v první osobě bez rušivých roleplay poznámek, drží se své povahy a znají vzájemné vztahy.
- **Perzistentní paměť rozhovorů:** Veškerá historie zpráv pro každou postavu se ukládá do relační databáze PostgreSQL a při každém dalším dotazu se předává LLM modelu jako kontext.
- **Odolnost vůči výpadkům (Graceful Fallback):** Při nedostupnosti AI endpointu nebo chybě API klíče aplikace nespadne, ale vrátí bezpečnou fallback zprávu a informuje uživatele ve stavovém řádku.
- **Automatický seeding a migrace schématu:** Při startu aplikace (`on_startup`) probíhá automatické napojení na databázi (s retry mechanismem), vytvoření tabulek, zpětně kompatibilní úprava sloupců a inicializace podezřelých.
- **Systém finálního rozsudku a auto-reset hry:** Hráč může kdykoliv vznést finální obvinění (`/verdict`). Aplikace vyhodnotí, zda byl odhalen skutečný pachatel, zobrazí dramatický verdikt a automaticky vyčistí hru pro novou partii.
- **Moderní responzivní UI:** Tmavý detektivní design postavený na **Tailwind CSS** s indikátorem psaní (*„Marek Kříž píše...“*), přepínáním výslechových místností a modálními okny.

---

## 📂 Struktura projektu

```text
AiDetectiveFull/
├── app/
│   ├── ai_client.py      # Inicializace asynchronního OpenAI klienta (AsyncOpenAI)
│   ├── config.py         # Konfigurace aplikace a čtení ENV proměnných přes Pydantic Settings
│   ├── db.py             # SQLAlchemy 2.0 ORM modely (GameSession, Character, Message) a init_db()
│   ├── main.py           # FastAPI aplikace, routy, seeding dat a herní logika
│   └── prompts.py        # Příběhový kontext, pravidla chování AI a systémové prompty postav
├── static/
│   ├── screenshots/      # Snímky obrazovky uživatelského rozhraní
│   └── placeholder.txt   # Drží adresář static ve verzovacím systému
├── templates/
│   └── index.html        # Kompletní frontend (Jinja2 šablona, Tailwind CSS, Vanilla JS)
├── Dockerfile            # Definice produkčního obrazu (Python 3.12-slim + uvicorn)
├── compose.yml           # Orchestrace kontejnerů (FastAPI aplikace + PostgreSQL 16 s healthcheckem)
└── requirements.txt      # Seznam Python závislostí
```

---

## 🚀 Spuštění projektu

Projekt podporuje jak **OpenAI API**, tak jakýkoliv **OpenAI-kompatibilní server** (např. školní/vlastní instanci Ollama, vLLM či LiteLLM s modelem `gemma3:27b`).

### Možnost 1: Spuštění přes Docker Compose (Doporučeno)

1. **Naklonujte repozitář:**
   ```bash
   git clone https://github.com/nedo44/AiDetectiveFull.git
   cd AiDetectiveFull
   ```

2. **Nastavte proměnné prostředí:**
   Vytvořte v kořenovém adresáři soubor `.env` (nebo proměnné předejte v terminálu):
   ```env
   OPENAI_API_KEY=vas_api_klic
   OPENAI_BASE_URL=https://kurim.ithope.eu/v1
   OPENAI_MODEL=gemma3:27b
   ```
   > **Tip:** Pokud chcete použít oficiální OpenAI API, nastavte `OPENAI_BASE_URL=https://api.openai.com/v1` a např. `OPENAI_MODEL=gpt-4o-mini`. Pokud používáte lokální **Ollamu**, nastavte `OPENAI_BASE_URL=http://host.docker.internal:11434/v1` a libovolný `OPENAI_API_KEY=ollama`.

3. **Spusťte kontejnery:**
   ```bash
   docker compose up --build
   ```

4. **Otevřete aplikaci v prohlížeči:**
   Přejděte na adresu **[http://localhost:8000](http://localhost:8000)**.

---

### Možnost 2: Lokální spuštění pro vývoj (bez Dockeru)

1. **Vytvořte a aktivujte virtuální prostředí:**
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate  # Na Windows: .venv\Scripts\activate
   pip install -r requirements.txt
   ```

2. **Vytvořte soubor `.env` v kořenové složce:**
   ```env
   DATABASE_URL=postgresql+psycopg://kurim:kurim_password@localhost:5432/kurim_mystery
   OPENAI_API_KEY=vas_api_klic
   OPENAI_BASE_URL=https://kurim.ithope.eu/v1
   OPENAI_MODEL=gemma3:27b
   APP_TITLE=Kurim Mystery
   ```

3. **Spusťte vývojový server Uvicorn:**
   ```bash
   uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
   ```

---

## ⚙️ Konfigurační proměnné (`.env`)

| Proměnná | Výchozí hodnota | Popis |
| :--- | :--- | :--- |
| `DATABASE_URL` | *(povinné)* | Připojovací řetězec k PostgreSQL databázi (`postgresql+psycopg://...`) |
| `OPENAI_API_KEY` | *(povinné)* | API klíč pro autentizaci vůči AI serveru |
| `OPENAI_BASE_URL` | `https://kurim.ithope.eu/v1` | URL adresa OpenAI-kompatibilního API endpointu |
| `OPENAI_MODEL` | `gemma3:27b` | Název jazykového modelu použitého pro generování odpovědí |
| `APP_TITLE` | `Kurim Mystery` | Název aplikace zobrazovaný v hlavičce a titulku stránky |
| `PORT` | `8000` | Port, na kterém naslouchá Uvicorn server v Docker kontejneru |

---

## 🔌 Přehled REST API Endpointů

FastAPI automaticky generuje interaktivní OpenAPI dokumentaci dostupnou na `/docs` (Swagger UI) a `/redoc`.

| Metoda | Endpoint | Popis |
| :--- | :--- | :--- |
| `GET` | `/` | Vykreslí hlavní HTML rozhraní hry (`templates/index.html`) se seznamem podezřelých. |
| `GET` | `/api/state` | Vrátí JSON s aktuální herní relací, seznamem postav a aktivním podezřelým. |
| `GET` | `/api/profile/{suspect_id}` | Vrátí detailní profil a popis vybrané postavy (`marek`, `jana`, `petr`). |
| `GET` | `/api/history/{suspect_id}` | Vrátí chronologicky seřazenou historii výslechu dané postavy. |
| `POST` | `/api/chat` | Přijme formulářová data (`suspect_id`, `message`), uloží dotaz do DB, zavolá LLM s celou historií a uloží odpověď. |
| `POST` | `/verdict` | Přijme JSON `{"accused_name": "..."}`, vyhodnotí správnost obvinění, vrátí výsledek a vyresetuje hru pro novou partii. |

---

## 🛠️ Použité technologie

- **Backend:** [FastAPI](https://fastapi.tiangolo.com/), [Uvicorn](https://www.uvicorn.org/), Python 3.12
- **Databáze & ORM:** [PostgreSQL 16](https://www.postgresql.org/), [SQLAlchemy 2.0](https://www.sqlalchemy.org/), `psycopg` (v3)
- **AI Integrace:** [OpenAI Python SDK](https://github.com/openai/openai-python) (`AsyncOpenAI`), Pydantic Settings
- **Frontend:** HTML5, [Jinja2](https://jinja.palletsprojects.com/), [Tailwind CSS](https://tailwindcss.com/), Vanilla JavaScript (ES6+)
- **Infrastruktura:** Docker, Docker Compose
