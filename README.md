# Workout Tracker

Small personal app for logging workouts against a **regiment**.

- Put your weekly plan in **Regiment** (edit each weekday)
- **Today** shows the workout of the day from the active regiment
- Start a session, log **actual sets**, add **notes** per exercise and for the whole workout
- SQLite only — no external services

## Quick start (local, uv)

```bash
uv sync
uv run uvicorn workout_tracker.main:app --reload --host 127.0.0.1 --port 8000
```

Open http://127.0.0.1:8000

DB defaults to `data/workouts.db` (`WORKOUT_DB_PATH` to override).

## Quick start (Docker)

```bash
docker compose up --build
```

Open http://localhost:8000 — data volume `workout-data`.

## How to use

1. **Regiment** — create/edit your plan. Day exercises: `Name | target | notes` (one per line)
2. **Today** — WOD for the date, start session
3. Log sets one per line: `80 kg x 5` or `135x5 @ RPE8`
4. Exercise + workout comments as you go
5. **History** — past sessions

First boot seeds a sample **3-Day Strength** push/pull/legs plan.

## Oz cloud

Built for [Oz cloud agent environments](https://docs.warp.dev/platform/environments).

| Requirement | This project |
|---|---|
| glibc image (no Alpine/musl) | `python:3.12-slim-bookworm` |
| Non-root UID 1000 | `app` user in Dockerfile |
| Repeatable setup | `uv sync` |
| Health check | `GET /healthz` |
| Agent guidance | `AGENTS.md` |

### Live environment

- **ID:** `8vZmtXcj19992bGCeiQabz`
- **Name:** `workout-tracker` (personal)
- **Image:** `python:3.12-slim-bookworm`
- **Setup:** install git/curl → uv → clone `eprykhodko/workout-tracker` → `uv sync`

Run a cloud agent:

```bash
oz agent run-cloud \
  --environment 8vZmtXcj19992bGCeiQabz \
  --prompt "Read AGENTS.md, then add a weekly volume summary page"
```

Repo is public so setup can `git clone` without Warp `--repo` binding (that flow kept failing OAuth against this account).

## Routes

| Method | Path | Purpose |
|---|---|---|
| GET | `/` | WOD + start session |
| GET/POST | `/regiment` | Weekly plan + create/activate |
| GET/POST | `/regiment/days/{id}` | Edit one weekday |
| GET | `/sessions` | History |
| GET/POST | `/sessions/{id}` | Log sets + comments |
| GET | `/healthz` | Liveness |

## Layout

```
src/workout_tracker/
  main.py          # FastAPI + forms
  db.py            # SQLite
  templates/
  static/style.css
AGENTS.md
Dockerfile
docker-compose.yml
```
