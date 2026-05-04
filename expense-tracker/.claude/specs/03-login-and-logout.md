# Spec: Login and Logout

## Overview
Implement session-based login and logout for Spendly. This step wires up the existing `login.html` form to a `POST /login` route that validates credentials, verifies the password hash, stores the user's identity in Flask's server-side session, and redirects to the profile page. The `GET /logout` route clears the session and redirects to the landing page. The navbar in `base.html` is updated to show contextual links based on whether the user is logged in.

## Depends on
- Step 1 (Database Setup) — `get_db()` and `users` table must exist.
- Step 2 (Registration) — `get_user_by_email()` helper and `app.secret_key` must be in place.

## Routes
- `GET /login` — renders `login.html` — public (already implemented, extend to accept POST)
- `POST /login` — validates credentials, sets session, redirects — public
- `GET /logout` — clears session, redirects to landing — logged-in (stub exists, implement now)

## Database changes
No database changes. All required data (`email`, `password_hash`) is already in the `users` table.

## Templates
- **Modify:** `templates/login.html`
  - Fix `action="/login"` → `action="{{ url_for('login') }}"` (no hardcoded URLs)
  - The `{% if error %}` error block is already present — no change needed there
- **Modify:** `templates/base.html`
  - Update the `nav-links` div to show different links based on session state:
    - Logged out: "Sign in" + "Get started" (current behaviour)
    - Logged in: greeting with user's name + "Sign out" link pointing to `url_for('logout')`

## Files to change
- `app.py` — add `session` to Flask imports; add `check_password_hash` import from `werkzeug.security`; update `/login` to `methods=["GET", "POST"]` with POST handler; implement `/logout`
- `templates/login.html` — fix hardcoded form action
- `templates/base.html` — conditional nav links based on `session`

## Files to create
No new files.

## New dependencies
No new dependencies. `werkzeug.security.check_password_hash` is already available via the existing werkzeug install.

## Rules for implementation
- No SQLAlchemy or ORMs — use raw `sqlite3` via `get_db()`
- Parameterised queries only — no f-strings or `.format()` in SQL
- Passwords verified with `werkzeug.security.check_password_hash` — never compare plaintext
- Use CSS variables — never hardcode hex values in templates or stylesheets
- All templates extend `base.html`
- Session keys to store on login: `session['user_id']` (integer) and `session['user_name']` (string)
- On login failure (wrong email or wrong password): use the same generic error message for both cases — "Invalid email or password." — never reveal which field was wrong
- On login success: `redirect(url_for('profile'))`
- On logout: call `session.clear()`, then `redirect(url_for('landing'))`
- Reuse the existing `get_user_by_email()` helper — do not query users inline in the route
- `check_password_hash` is imported in `app.py`, not in `db.py` — password verification is the route's responsibility

## Definition of done
- [ ] Submitting valid credentials sets `session['user_id']` and redirects to `/profile`
- [ ] Submitting wrong password shows "Invalid email or password." and stays on `/login`
- [ ] Submitting unregistered email shows "Invalid email or password." and stays on `/login`
- [ ] Submitting with empty field shows "All fields are required." and stays on `/login`
- [ ] Visiting `/logout` clears the session and redirects to `/`
- [ ] After logout, revisiting `/logout` again still redirects to `/` without error
- [ ] Navbar shows "Sign in" + "Get started" when logged out
- [ ] Navbar shows user name + "Sign out" when logged in
- [ ] GET `/login` renders empty form with no error
- [ ] No raw SQL strings use f-strings or `.format()`
- [ ] App starts without errors after changes
