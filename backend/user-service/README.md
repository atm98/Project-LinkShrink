# User Service

## 1. Overview

Handles all user-related concerns: registration, login, profile management, and authentication (issuing JWTs). It is the sole owner of the user-related database tables.

---

## 2. Database Schema

This service owns the following tables.

### `users` table

| Column Name     | Data Type                  | Constraints & Notes                               |
|-----------------|----------------------------|---------------------------------------------------|
| `id`            | `UUID`                     | Primary Key. UUID is preferred in distributed systems. |
| `username`      | `VARCHAR(50)`              | Unique, Indexed. For user login.                  |
| `email`         | `VARCHAR(255)`             | Unique, Indexed. For communication.               |
| `password_hash` | `VARCHAR(255)`             | Stores the hashed password.                       |
| `created_at`    | `TIMESTAMP WITH TIME ZONE` | Auto-generated on record creation.                |
| `updated_at`    | `TIMESTAMP WITH TIME ZONE` | Auto-updated on record modification.              |

### `refresh_tokens` table

| Column Name  | Data Type                  | Constraints & Notes                                           |
|--------------|----------------------------|---------------------------------------------------------------|
| `id`         | `UUID`                     | Primary Key.                                                  |
| `user_id`    | `UUID`                     | Foreign Key -> `users.id`. Indexed for quick lookups.         |
| `token_hash` | `VARCHAR(255)`             | A hash of the refresh token. Indexed for fast lookups. |
| `expires_at` | `TIMESTAMP WITH TIME ZONE` | Must match the expiration of the actual token.                |
| `created_at` | `TIMESTAMP WITH TIME ZONE` | Auto-generated on record creation.                            |

---

## 3. API Endpoints

### Un-authenticated Routes

*   **`POST /api/users/register`**
    *   **Description**: Allows a new user to create an account.
    *   **Request Body**: `{ "username": "...", "email": "...", "password": "..." }`

*   **`GET /api/users/exists`**
    *   **Description**: Checks if a given username is already registered. Must be rate-limited.
    *   **Query**: `?username=someuser`

*   **`POST /api/users/login`**
    *   **Description**: Authenticates a user and returns JWTs.
    *   **Request Body**: `{ "login": "...", "password": "..." }`

*   **`POST /api/users/refresh-token`**
    *   **Description**: Obtains a new access token using a refresh token.

### Authenticated Routes

*   **`GET /api/users/me`**
    *   **Description**: Retrieves the profile for the currently logged-in user.

*   **`PUT /api/users/me`**
    *   **Description**: Updates the profile for the currently logged-in user.
    *   **Request Body**: `{ "email": "...", "password": "..." (optional) }`

*   **`POST /api/users/logout`**
    *   **Description**: Logs the user out by invalidating their refresh token.

---

## 4. Technical Stack

*   **Language**: Go
*   **Web Framework / Router**: Chi
*   **Database**: PostgreSQL
*   **Database Driver**: pgx
*   **Key Libraries**:
    *   `golang-jwt/jwt` for JWT creation and validation.
    *   `golang.org/x/crypto/bcrypt` for password hashing.
    *   `joho/godotenv` for local environment variable management.
*   **Containerization**: Docker 