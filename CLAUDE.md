# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Spendly** (code name: fintrack-engine) — an AI-assisted expense intelligence platform for natural language expense logging, automated categorization, and behavioral spending insights.

## Commands

```bash
# Activate virtual environment first
source venv/bin/activate

# Run development server (http://localhost:5001)
python app.py

# Run tests
pytest

# Run a single test file
pytest tests/test_routes.py

# Install dependencies
pip install -r requirements.txt
```

## Architecture

This is a monolithic Flask application using server-side rendering via Jinja2 templates.

**Request flow:** Browser → Flask routes (`app.py`) → Jinja2 templates (`templates/`) → served HTML

**Key layers:**
- `app.py` — all route definitions; currently returns rendered templates; no JSON API yet
- `database/db.py` — database layer stub (functions `get_db()`, `init_db()`, `seed_db()` are not yet implemented); uses SQLite
- `templates/base.html` — shared layout (navbar, footer); all pages extend this
- `static/css/` — custom CSS using CSS variables for theming; `landing.css` is landing-page-specific, `style.css` is global
- `static/js/main.js` — vanilla JS placeholder

**Current route inventory** (many are unimplemented stubs returning placeholder strings):
- `/` — landing page
- `/register`, `/login`, `/logout` — auth flow (logout/register/login logic TBD)
- `/profile` — user profile (TBD)
- `/terms`, `/privacy` — static legal pages
- `/expenses/add`, `/expenses/<id>/edit`, `/expenses/<id>/delete` — expense CRUD (TBD)

## Development Status

The project is in early stages. Steps 1–2 (templates/landing page) are complete. The database layer, authentication, and expense CRUD are not yet implemented. When implementing features, the natural next steps are:
1. Implement `database/db.py` (SQLite schema + connection helpers)
2. Add auth (register/login/logout/session)
3. Build expense CRUD routes
4. Integrate Claude API for AI-powered categorization and natural language logging
