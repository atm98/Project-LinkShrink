# Redirect Service

## 1. Overview

Its sole responsibility is to perform the redirection from a `short_code` to a `long_url`. It is heavily optimized for reads and designed for high availability and low latency. It does not own a database but reads from the Link DB and a cache.

---

## 2. Dependencies

*   **Link Database**: Reads `long_url` data on a cache miss.
*   **Cache (Redis)**: Reads from the cache on every request to find a `long_url`.
*   **Message Queue (Kafka)**: Publishes a "click" event after a successful redirect.

---

## 3. API Endpoints

### Un-authenticated Routes

*   **`GET /{short_code}`**
    *   **Description**: The primary endpoint of the application. Redirects a user and fires an event to track the click.
    *   **Response**: `302 Found` to the `long_url`, or `404 Not Found`.

---

## 4. Technical Stack

*   **Language**: Go
*   **Web Framework / Router**: Chi
*   **Cache Client**: go-redis
*   **Message Queue Producer**: A Kafka client library (e.g., `segmentio/kafka-go`)
*   **Database Driver**: pgx (for cache-miss scenarios)
*   **Key Libraries**:
    *   `joho/godotenv` for local environment variable management.
*   **Containerization**: Docker 