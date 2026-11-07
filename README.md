# Redis-Mini-Projects

Implemented Redis operations through small projects to see how things actually work.

## Projects

### 1. Basics and Setup
- Set up a local development environment for Redis using Docker containers for both Redis and MongoDB.
- Configured a `docker-compose.yml` file to manage both databases independently on a local machine.
- Built a Node.js and Express app that uses `ioredis` and `Mongoose` to handle the database connections.
- Validated the setup by testing API endpoints, confirming both databases are connected and working.

### 2. Banner Caching
- Used Redis as a caching layer for website banners to reduce database load.
- Managed banner content with `SET`, `GET`, `EXISTS`, and `DEL`.
- Integrated these commands into an Express.js backend for fast data delivery.
- Offloads static or semi-static data, giving faster responses and fewer unnecessary database queries.

### 3. Email Queue with Redis List
- Redis lists can work as a basic email queue: `LPUSH` to add jobs, `RPOP` to process them.
- No built-in retry mechanism, so failed jobs can't be reprocessed automatically.
- No support for parallel worker processing without extra libraries or configuration.

**Why it is not preferred for production:**
- **Job loss:** `RPOP` removes the job from Redis immediately. If the worker crashes or the email fails to send, the job is gone for good.
- **No retries:** The job is removed on the first attempt, so nothing tracks failures or requeues messages that failed due to temporary issues.
- **No parallelism:** The manual setup is sequential. It has no worker management or parallel execution, which is needed for high email volumes.

### 4. Pub/Sub
- Publishers send messages to channels, and active subscribers receive them in real time.
- Multiple channels let subscribers listen only to the streams relevant to them.
- Messages are sent as strings, so subscribers must parse them back into objects.
- A lightweight, built-in way to handle notifications and real-time events without third-party packages.

### 5. OTP and Time To Live (TTL)
- Used TTL so temporary data like OTPs expires automatically after a set time.
- Stored verification codes as key-value pairs with an expiry, with no persistent database storage needed.
- Generated keys dynamically per OTP, allowing quick validation and immediate deletion after successful verification.
- Redis is great for temporary auth data, but it complements a primary database rather than replacing it.

### 6. BullMQ Infrastructure
- BullMQ is a production-grade, Redis-backed queue system for asynchronous tasks.
- Producers submit jobs and consumers process them independently, so the two are decoupled.
- Implementation involves defining queue names, writing worker logic, and handling events like completed and failed jobs.
- Workers can be scaled easily to handle varying loads, giving load balancing and reliability.

### 7. User Profile: JSON vs Hash
- Redis can store user profiles either as a JSON string or as a `HASH`.
- **JSON string:** simple, but every update requires parsing and replacing the whole object.
- **HASH:** lets you read and update individual fields directly, which is more efficient.
- HASH is usually the better choice for production because it simplifies data management and improves performance.
