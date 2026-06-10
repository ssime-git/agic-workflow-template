# Agent Rules

## Stack
- **Language:** Python 3.12+
- **Packaging:** `uv` only — no `pip install`, no `requirements.txt`, no `setup.py`
- **Linting:** `ruff check` + `ruff format` — no black, no flake8
- **Tests:** `pytest` via `uv run pytest`
- **CI:** GitHub Actions, lint + tests block PR merge

## Architecture conventions
- Source code lives in `src/<package>/`
- Tests mirror src: `tests/test_<module>.py`
- Config via env vars + `.env` (never hardcoded secrets)
- One module = one responsibility; no god files
- Async preferred for I/O-bound work

## Forbidden patterns
- No `requirements.txt` / `setup.cfg` / `setup.py`
- No hardcoded API keys or credentials anywhere in code
- No `print()` for logging — use `logging` or `structlog`
- No mutable default arguments
- No wildcard imports (`from module import *`)

## Multi-agent workflow

**Resume:** If a "where you left off" block is present at startup, start from there — no re-exploration.

**Deep context:** `memory_query <topic>` — use ai-memory wiki for transient session context only.

**Resync mid-session:** ask the active agent `catch me up`.

**Agent handoff:** run `/checkpoint` in the active agent before switching. Entire handles the session snapshot.

**Parallel work:** `git worktree add ../<project>-<feature> -b feat/<feature>` — one agent per worktree, shared ai-memory.

## Persistence rules
- Architecture decisions, benchmarks, validated runbooks → explicit file in `docs/`
- ai-memory wiki → transient context only (never write directly, always via `memory_write_page`)
- No session state in this file
- No parallel memory files
