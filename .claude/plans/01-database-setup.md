# Plan: 01 — Database Setup

## Context

The Spendly app currently has a stub `database/db.py` with no implementation. All future features (auth, expenses, profile) depend on a working SQLite data layer. This plan implements `get_db()`, `init_db()`, and `seed_db()` per the spec in `.claude/specs/01-database-setup.md`, then wires the DB startup into `app.py`.

---

## Files Modified

- `database/db.py` — implemented all three functions
- `app.py` — added imports and startup initialization block

---

## Implementation Summary

### `database/db.py`

**`get_db()`**
- Resolves DB path relative to `database/` directory → `spendly.db` in project root
- Sets `conn.row_factory = sqlite3.Row` for dict-like row access
- Executes `PRAGMA foreign_keys = ON` on every connection
- Returns the open connection

**`init_db()`**
- Creates `users` table: `id`, `name`, `email` (UNIQUE NOT NULL), `password_hash`, `created_at`
- Creates `expenses` table: `id`, `user_id` (FK → users.id), `amount` (REAL), `category`, `date`, `description`, `created_at`
- Uses `CREATE TABLE IF NOT EXISTS` — safe to call repeatedly

**`seed_db()`**
- Guards against duplicates: checks `COUNT(*) FROM users`, returns early if > 0
- Inserts demo user: `Demo User / demo@spendly.com / demo123` (password hashed via `werkzeug`)
- Inserts 8 sample expenses across all 7 categories (Food, Transport, Bills, Health, Entertainment, Shopping, Other)
- All inserts use `?` parameterized queries

### `app.py`

- Imports `get_db`, `init_db`, `seed_db` from `database.db`
- Calls `init_db()` and `seed_db()` inside `with app.app_context():` immediately after `app = Flask(__name__)`

---

## Rules Satisfied

- No ORM, no SQLAlchemy
- Parameterized queries only
- `PRAGMA foreign_keys = ON` on every connection
- `amount` stored as REAL
- Passwords hashed via `werkzeug.security.generate_password_hash`
- Seed guard prevents duplicate inserts
- Dates in YYYY-MM-DD format

---

## Verification Results

- App starts without errors, `spendly.db` created in project root
- Demo user present with correct fields
- 8 expenses inserted covering all 7 categories
- Second startup does not duplicate seed data (user count stays at 1)
- INSERT with invalid `user_id=999` raises `FOREIGN KEY constraint failed`
