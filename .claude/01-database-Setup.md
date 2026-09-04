# Database Setup Implementation Plan

## Context
The project "Spendly" requires a persistence layer to store user accounts and their personal expenses. This is the foundational step for the application, providing the data layer upon which all other features (authentication, profile, expense tracking) are built. The goal is to implement a lightweight SQLite database utility module and integrate it into the application startup.

## Implementation Approach

### 1. Core Database Utilities (`database/db.py`)
Implement the following functions to manage `spendly.db`:

- **`get_db()`**:
    - Connects to `spendly.db`.
    - Sets `conn.row_factory = sqlite3.Row` for name-based column access.
    - Executes `PRAGMA foreign_keys = ON` to enforce referential integrity.
    - Returns the connection.

- **`init_db()`**:
    - Creates the `users` table: `id` (PK, AUTOINCREMENT), `name` (NOT NULL), `email` (UNIQUE, NOT NULL), `password_hash` (NOT NULL), `created_at` (DEFAULT CURRENT_TIMESTAMP).
    - Creates the `expenses` table: `id` (PK, AUTOINCREMENT), `user_id` (FK to `users.id`, NOT NULL), `amount` (REAL, NOT NULL), `category` (NOT NULL), `date` (TEXT NOT NULL), `description` (TEXT), `created_at` (DEFAULT CURRENT_TIMESTAMP).
    - Uses `CREATE TABLE IF NOT EXISTS` for idempotency.
    - Enforces foreign key relationship: `FOREIGN KEY (user_id) REFERENCES users (id)`.

- **`seed_db()`**:
    - Checks if the `users` table is already populated to avoid duplicate seeding.
    - Hashes the demo password (`demo123`) using `werkzeug.security.generate_password_hash`.
    - Inserts one demo user: `demo@spendly.com`, `Demo User`.
    - Inserts exactly 8 sample expenses linked to the demo user, covering the following fixed categories:
        - Food, Transport, Bills, Health, Entertainment, Shopping, Other.
    - Ensures dates are in `YYYY-MM-DD` format.
    - Uses parameterized queries with `executemany()` for efficient insertion.

### 2. Application Integration (`app.py`)
- Import `init_db` and `seed_db` from `database.db`.
- Call both functions inside `app.app_context()` after the `Flask` app is initialized. This ensures the database is created and seeded before the server starts handling requests.

## Critical Files
- `database/db.py`: Implementation of all DB utility functions.
- `app.py`: Startup logic to initialize and seed the database.

## Verification Plan
1. **Startup Test**: Run `python app.py` and verify that `spendly.db` is created in the project root.
2. **Schema Validation**: Use a SQLite browser or a temporary script to verify that the `users` and `expenses` tables exist with the correct columns (`password_hash`, `created_at`, etc.) and constraints.
3. **Data Seeding Test**: Query the database to confirm that:
    - One demo user exists.
    - The demo user's password is a valid hash (not plain text).
    - 8 expenses exist, each mapped to the demo user and covering the required categories.
4. **Integrity Test**: Attempt to insert an expense with a non-existent `user_id` to verify that `PRAGMA foreign_keys = ON` is working (should raise `sqlite3.IntegrityError`).
5. **Idempotency Test**: Restart the app multiple times to ensure `init_db()` and `seed_db()` do not create duplicate tables or records.
