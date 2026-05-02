# Spec: Login and Logout

## Overview
Implement session-based login and logout so registered users can authenticate with their email and password. This step upgrades the `GET /login` stub into a full `GET`/`POST` route that verifies credentials with `werkzeug`, stores the authenticated user in Flask's `session`, and redirects to the dashboard (or landing page until one exists). The `GET /logout` stub is also wired up to clear the session and redirect to `/login`. Together these two routes form the auth boundary that all future protected pages will depend on.

## Depends on
- Step 01 — Database setup (`users` table, `get_db()`)
- Step 02 — Registration (`create_user()`, `users` rows exist)

## Routes
- `GET /login` — render login form — public (already exists as stub, upgrade it)
- `POST /login` — validate credentials, set `session['user_id']` and `session['user_name']`, redirect to `/` — public
- `GET /logout` — clear session, redirect to `/login` — public (already exists as stub, implement it)

## Database changes
No new tables or columns. The existing `users` table covers all requirements.

A new DB helper must be added to `database/db.py`:
- `get_user_by_email(email)` — returns the matching `sqlite3.Row` from `users` (with `id`, `email`, `name`, `password_hash`), or `None` if no match. Uses a parameterised query.

## Templates
- **Modify**: `templates/login.html`
  - Replace `{% if error %}` / `{{ error }}` with Flask `get_flashed_messages` (consistent with `register.html`)
  - Replace hardcoded `action="/login"` with `action="{{ url_for('login') }}"`
  - Keep all existing visual design and CSS classes

## Files to change
- `app.py` — upgrade `login()` to handle `GET` and `POST`; implement `logout()`; import `session` and `check_password_hash`
- `database/db.py` — add `get_user_by_email()` helper
- `templates/login.html` — switch error display to flash messages and fix `action` URL

## Files to create
None.

## New dependencies
No new dependencies. Uses `werkzeug.security.check_password_hash` (already installed) and Flask's built-in `session`.

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only — never use f-strings in SQL
- Passwords verified with `werkzeug.security.check_password_hash` — never compare plaintext
- Store only `user_id` (int) and `user_name` (str) in `session` — never store `password_hash`
- `app.secret_key` must remain set in `app.py` for `session` to work
- On login failure, flash a single generic error ("Invalid email or password.") — do not reveal which field was wrong
- On login success, `redirect` to `url_for('landing')` (until a dashboard route exists)
- `logout()` must call `session.clear()` then redirect to `url_for('login')`
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Use `url_for()` for every internal link — never hardcode URLs

## Definition of done
- [ ] `GET /login` renders the login form without errors
- [ ] Submitting valid credentials sets the session and redirects to `/`
- [ ] Submitting an unregistered email re-renders the form with "Invalid email or password." — no session set
- [ ] Submitting a wrong password re-renders the form with "Invalid email or password." — no session set
- [ ] Submitting with any empty field re-renders the form with a validation error — no DB query made
- [ ] `GET /logout` clears the session and redirects to `/login`
- [ ] After logout, navigating back does not restore the session
- [ ] The demo user (`demo@spendly.com` / `demo123`) can log in successfully
