# Spec: Login and Logout

## Overview

This feature implements the authentication flow, allowing registered users to securely log in and log out of the Spendly application. This is a critical step in the roadmap to ensure that expense data is private and tied to a specific authenticated user session.

## Depends on

- 02-registration

## Routes

- `POST /login` - Authenticates user credentials and establishes a session - public
- `GET /logout` - Clears the user session and redirects to the login page - logged-in

## Database changes

No database changes.

## Templates

- Modify: `templates/login.html` - Implement the login form with appropriate input fields and error handling.

## Files to change

- `app.py` - Implement authentication logic for `/login` and session clearance for `/logout`.
- `database/db.py` - Add a helper function `verify_user(email, password)` to check credentials.
- `templates/login.html` - Update the template to handle form submission and error display.

## Files to create

No new files.

## New dependencies

No new dependencies.

## Rules for implementation

- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords verified with `werkzeug.security.check_password_hash`
- Use CSS variable - never hardcode hex values
- All templates extends base.html
- Use Flask `session` to store the `user_id` upon successful login.

## Definition of done

- [ ] Valid credentials successfully log the user in and redirect them (e.g., to the landing page or profile).
- [ ] Invalid email or password displays an "Invalid credentials" error message on the login page.
- [ ] Clicking "Logout" clears the session and redirects the user back to the login page.
- [ ] Verified that password hashing/checking is handled via `werkzeug`.
