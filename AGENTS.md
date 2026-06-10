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

## First-run setup check

On first start in a new clone, verify the stack before doing any work:

```bash
# 1. ai-memory server running
ai-memory status          # must respond; if not → see ~/project/ai-memory/AGENTS.md

# 2. ai-memory wired to this agent
#    (idempotent — safe to re-run)
ai-memory install-mcp   --client claude-code --apply
ai-memory install-hooks --agent  claude-code --apply

# 3. uv available
uv --version              # if missing: curl -LsSf https://astral.sh/uv/install.sh | sh

# 4. dev deps installed
uv sync --all-extras

# 5. Entire active (already committed in .entire/)
entire checkpoint list    # should respond (0 checkpoints on fresh clone = normal)
```

If ai-memory is not installed on this machine, follow the full runbook at `~/project/ai-memory/AGENTS.md` before proceeding.

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
