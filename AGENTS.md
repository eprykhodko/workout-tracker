# Agent notes — workout-tracker

## Stack
- Python 3.12+, FastAPI, Jinja2, SQLite
- Package manager: **uv** (`uv sync`, `uv run …`)
- App package: `src/workout_tracker`

## Run locally
```bash
uv sync
uv run uvicorn workout_tracker.main:app --reload --host 127.0.0.1 --port 8000
```

Health: `GET /healthz`

DB path: `WORKOUT_DB_PATH` (default `data/workouts.db`). Do not commit `data/`.

## Layout
- `src/workout_tracker/main.py` — routes + form handlers
- `src/workout_tracker/db.py` — SQLite schema/queries
- `src/workout_tracker/templates/` — HTML
- `src/workout_tracker/static/style.css`

## Product behavior
- **Regiment** = weekly plan (Mon–Sun). Active regiment drives WOD.
- **Session** = actual workout log: sets per exercise + comments.
- Sets input: one line per set, e.g. `80 kg x 5` or `135x5 @ RPE8`.
- Day edit exercises: `Name | target | notes` per line.

## Oz environment expectations
- Base image must be **glibc** (Debian/bookworm). No Alpine/musl.
- Prefer `python:3.12-slim-bookworm` or `warpdotdev/dev-web:latest`.
- Setup: install uv if missing, then `uv sync` from repo root.
- Non-root UID 1000 is fine; app writes only under `data/`.

## Tests / smoke
```bash
uv sync
uv run python -c "from workout_tracker.main import app; print(app.title)"
uv run uvicorn workout_tracker.main:app --host 127.0.0.1 --port 8000 &
sleep 1
curl -sf http://127.0.0.1:8000/healthz
```
