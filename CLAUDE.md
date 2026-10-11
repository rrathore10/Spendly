# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

"Spendly" is a Flask + SQLite expense tracker built as a step-by-step learning project (CampusX). The landing, auth form pages and legal pages exist as static templates; the database layer, auth, and expense CRUD are intentionally unimplemented and are filled in by numbered "Steps" referenced in the code.

## Architecture

- `app.py` — single Flask app; all routes live here. Runs on port 5001 with debug on.
- `database/db.py` — comment-only stub describing the planned contract: `get_db()` (SQLite connection with `row_factory` and foreign keys enabled), `init_db()` (`CREATE TABLE IF NOT EXISTS`), `seed_db()` (sample dev data). `database/__init__.py` is empty.
- `templates/` — every page extends `base.html`, which provides the navbar, Google Fonts (DM Serif Display, DM Sans), and the blocks `title`, `head`, `content`, `scripts`. `base.html` also loads `static/js/main.js`.
- `static/css/style.css` — single shared stylesheet (~600 lines). `static/js/main.js` is an empty placeholder.
- `landing.html` has its own inline `<script>` (the YouTube modal) rather than using `main.js`.

## Where things belong

- New routes: `app.py`, under the matching section banner (real routes vs. placeholder routes).
- SQL, connection handling, schema, seed data: `database/db.py` only — routes should call its helpers, not open SQLite directly.
- New pages: `templates/<name>.html` extending `base.html`.
- Styles: `static/css/style.css`. Shared JS: `static/js/main.js`; page-specific JS goes in the page's `{% block scripts %}`.
- Tests: pytest, in a top-level `tests/` directory (none exist yet).

## Code style

- Python: 4-space indent, snake_case, route function names match the `url_for` endpoint names used in templates (`landing`, `login`, `register`, ...).
- `app.py` groups routes under `# ---- ... ----` comment banners; keep that structure.
- Templates use `url_for(...)` for links and static assets, never hard-coded paths.
- Comments are sparse; no docstrings or type hints in existing code.

## Tech constraints

- Python with Flask 3.1.3, Werkzeug 3.1.6, SQLite (stdlib `sqlite3`), pytest 8.3.5, pytest-flask 1.3.0 (see `requirements.txt`). No ORM, no frontend framework or build tooling is present — don't introduce them without being asked.
- Foreign keys must be enabled on every SQLite connection (per the `get_db()` contract).

## Commands

```bash
source venv/bin/activate
pip install -r requirements.txt
python app.py                              # dev server at http://localhost:5001
pytest                                     # run tests
pytest path/to/test_file.py::test_name     # single test
```

No linter or build step is configured.

## Implemented vs stub routes

| Route | Status |
|-------|--------|
| `/` (landing), `/register`, `/login`, `/terms`, `/privacy` | Implemented — render templates, GET only (no form handling, sessions, or auth yet) |
| `/logout` | Stub — Step 3 |
| `/profile` | Stub — Step 4 |
| `/expenses/add` | Stub — Step 7 |
| `/expenses/<int:id>/edit` | Stub — Step 8 |
| `/expenses/<int:id>/delete` | Stub — Step 9 |

Stubs return a plain string naming the step. Only replace a stub when working on that step.

## Warnings and things to avoid

- Don't implement stub routes or `database/db.py` beyond the step you're asked to work on.
- Port is 5001, not Flask's default 5000.
- Don't commit `venv/`, `expense_tracker.db`, `.env`, or `.claude/settings.local.json` (all gitignored).
- `file.md` in the repo root is an untracked pasted session transcript, not documentation — don't treat it as a spec or commit it.
- The delete stub is a GET route; when implementing it for real, use POST rather than a state-changing GET.
