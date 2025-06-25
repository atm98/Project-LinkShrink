# Link Service

## 1. Overview

Manages the lifecycle of shortened URLs. This includes creating, updating, deleting, and listing links for both anonymous and authenticated users. It is the sole owner of the `urls` database table.

---

## 2. Database Schema

### `urls` table

| Column Name  | Data Type                  | Constraints & Notes                               |
|--------------|----------------------------|---------------------------------------------------|
| `id`         | `BIGINT`                   | Primary Key, Auto-incrementing.                   |
| `short_code` | `VARCHAR(30)`              | **Unique, Indexed**. The unique identifier in the short URL. |
| `long_url`   | `TEXT`                     | Not Null. The original URL to redirect to.        |
| `user_id`    | `UUID`                     | Nullable for anonymous users. Application-level integrity: not a DB foreign key; validated by Link Service via User Service API. |
| `created_at` | `TIMESTAMP WITH TIME ZONE` | Auto-generated on record creation.                |
| `expires_at` | `TIMESTAMP WITH TIME ZONE` | Nullable. For links with an expiration date.      |

---

## 3. API Endpoints

### Un-authenticated Routes

*   **`POST /api/links/anonymous`**
    *   **Description**: Creates a short URL for an anonymous user. The `short_code` is always auto-generated.
    *   **Request Body**: `{ "long_url": "...", "expires_at": "..." (optional) }`

### Authenticated Routes

*   **`POST /api/links`**
    *   **Description**: Creates a short URL for the logged-in user. Supports an optional `custom_code`.
    *   **Request Body**: `{ "long_url": "...", "expires_at": "..." (optional), "custom_code": "..." (optional) }`

*   **`GET /api/links`**
    *   **Description**: Retrieves a list of all short URLs created by the authenticated user.

*   **`GET /api/links/exists`**
    *   **Description**: Checks if a given custom `short_code` is already in use. Must be rate-limited.
    *   **Query**: `?code=my-custom-link`

*   **`GET /api/links/{short_code}`**
    *   **Description**: Retrieves the details for a specific short URL owned by the user.

*   **`PUT /api/links/{short_code}`**
    *   **Description**: Updates a user's existing short URL.
    *   **Request Body**: `{ "long_url": "...", "expires_at": "..." }`

*   **`DELETE /api/links/{short_code}`**
    *   **Description**: Deletes a short URL owned by the user.

---

## 4. Technical Stack

*   **Language**: Go
*   **Web Framework / Router**: Chi
*   **Database**: PostgreSQL
*   **Database Driver**: pgx
*   **Key Libraries**:
    *   `nanoid` for generating unique, URL-friendly short codes.
    *   `joho/godotenv` for local environment variable management.
*   **Containerization**: Docker 