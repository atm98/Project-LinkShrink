# LinkShrink

A URL shortening service that helps you create shorter, more manageable links.

**Database Architecture Note:**
All backend services use a shared Amazon RDS PostgreSQL instance. Each service has its own logical database or schema within the instance, ensuring strong logical isolation while optimizing cost and management overhead.
