# Spec: Registration

## Overview

This feature allows new users to create a Spendly account by providing their full name, email address, and password. It provides the essential onboarding entry point for users to start tracking their expenses.

## Depends on

- 01-database-Setup

## Routes

- POST /register - Create a new user account - public

## Database changes

No database changes.

## Templates

- Modify: `templates/register.html` - Wire the existing form to the POST handler and display error messages.

## Files to change

- `app.py`
- `database/db.py`

## Files to create

No new files.

## New dependencies

No new dependencies.

## Rules for implementation

- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with werkzeug
- Use CSS variable - never hardcode hex values
- All templates extends base.html

## Definition of done

- [ ] User can successfully register with a valid name, email, and password.
- [ ] User is redirected to the login page after successful registration.
- [ ] Attempting to register with an existing email displays an "Email already registered" error message.
- [ ] Registered passwords are stored as hashes in the database, not as plain text.
- [ ] No new pip packages are installed.
