# Spec: Registration

## Overview
Implement user registration for Spendly. This step wires up the existing `register.html` template to a `POST /register` route that validates submitted form data, checks for email uniqueness, hashes the password, inserts a new user into the database, and redirects to the login page on success. It also adds the `create_user()` DB helper and flash-message feedback for validation errors. This is the first step that allows real users to create accounts.

## Depends on
- Step 1 (Database Setup) — `get_db()`, `init_db()`, and the `users` table must exist.

## Routes
- `GET /register` — renders `register.html` — public (already implemented, keep as-is)
- `POST /register` — processes registration form, inserts user, redirects — public

## Database changes
No new tables or columns. All required columns (`name`, `email`, `password_hash`, `created_at`) already exist in the `users` table.

## Templates
- **Modify:** `templates/register.html`
  - Add `method="POST"` and `action="{{ url_for('register') }}"` to the `<form>` tag
  - Add `name` attributes to all inputs: `name`, `email`, `password`, `confirm_password`
  - Render flashed error messages above the form using `get_flashed_messages()`
  - Include `{{ csrf_token() }}` if Flask-WTF is used — otherwise omit (no new packages allowed)

## Files to change
- `app.py` — add `POST` method to `/register` route; import `request`, `redirect`, `url_for`, `flash`, `session` from flask; import `create_user` and `get_user_by_email` from `database/db.py`; set `app.secret_key`
- `database/db.py` — add `create_user()` and `get_user_by_email()` helpers
- `templates/register.html` — wire up form attributes and flash message display

## Files to create
No new files.

## New dependencies
No new dependencies.

## Rules for implementation
- No SQLAlchemy or ORMs — use raw `sqlite3` via `get_db()`
- Parameterised queries only — never use f-strings or `.format()` in SQL
- Passwords hashed with `werkzeug.security.generate_password_hash` — never store plaintext
- Use CSS variables — never hardcode hex values in templates or stylesheets
- All templates extend `base.html`
- Validation order: (1) all fields present, (2) passwords match, (3) email not already taken
- On any validation failure: flash the error message and re-render `register.html` (do not redirect)
- On success: redirect to `url_for('login')` — do not auto-login the user in this step
- `app.secret_key` must be set for flash/session to work — use a hardcoded dev string for now (e.g. `"dev-secret-key"`)
- `create_user()` must close the DB connection before returning
- `get_user_by_email()` must return `None` if no match — never raise on missing row

## Definition of done
- [ ] Submitting the form with all valid fields inserts a new row in `users` and redirects to `/login`
- [ ] Submitting with an empty field shows a flash error and stays on `/register`
- [ ] Submitting with mismatched passwords shows a flash error and stays on `/register`
- [ ] Submitting with an already-registered email shows a flash error and stays on `/register`
- [ ] Password stored in DB is a werkzeug hash, not plaintext
- [ ] Navigating to `/register` with GET still renders the empty form
- [ ] No raw SQL strings use f-strings or `.format()`
- [ ] App starts without errors after changes
