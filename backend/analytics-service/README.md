# Analytics Service

## 1. Overview

Processes click events asynchronously. It consumes events from the message queue and aggregates them into a database for analysis. It provides endpoints for users to view statistics for their links. The analytics database is a logical database/schema within a shared Amazon RDS instance. The service also reads from the Link Service's logical database/schema as needed.

---

## 2. Dependencies

*   **Message Queue (Kafka)**: Consumes click events.
*   **Analytics Database**: Stores the `clicks` data.
*   **Link Database**: Reads link data to verify ownership when a user requests stats.

---

## 3. Database Schema

### `clicks` table

| Column Name    | Data Type                  | Constraints & Notes                               |
|----------------|----------------------------|---------------------------------------------------|
| `id`           | `BIGINT`                   | Primary Key, Auto-incrementing.                   |
| `url_id`       | `BIGINT`                   | Foreign Key -> `urls.id`. Indexed for fast lookups. |
| `clicked_at`   | `TIMESTAMP WITH TIME ZONE` | The timestamp of the click event. Indexed.        |
| `ip_address`   | `VARCHAR(45)`              | IP address of the client (supports IPv4 & IPv6).  |
| `user_agent`   | `TEXT`                     | User-Agent string from the client's browser.      |
| `country_code` | `VARCHAR(2)`               | ISO 3166-1 alpha-2 country code (e.g., 'US').     |
| `referrer`     | `TEXT`                     | The referring URL, if available.                  |

---

## 4. API Endpoints

### Authenticated Routes

*   **`GET /api/analytics/{short_code}/stats`**
    *   **Description**: Retrieves detailed analytics for a specific short URL owned by the user.

*   **`GET /api/analytics/overview`**
    *   **Description**: Retrieves an overview of statistics for all links owned by the user.

---

## 5. Technical Stack

*   **Language**: Go
*   **Web Framework / Router**: Chi
*   **Database**: ClickHouse (or another OLAP-focused database)
*   **Message Queue Consumer**: A Kafka client library (e.g., `segmentio/kafka-go`)
*   **Key Libraries**:
    *   `joho/godotenv` for local environment variable management.
*   **Containerization**: Docker 