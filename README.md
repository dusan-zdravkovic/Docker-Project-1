# End-to-End Data Engineering Practice Project


This project demonstrates a complete, containerized data pipeline—from ingestion to transformation—using industry-relevant tools like **Docker**, **PostgreSQL**, **Python**, and **dbt**.

It was designed to simulate how a real-world data engineering team would ingest, move, transform, and model data across systems using modern tools and containers.

Built from scratch, it mimics real-world workflows with:
- Multi-container orchestration (Docker Compose)
- ELT-style data flow using custom Python scripting
- Postgres-to-Postgres data movement
- Transformations powered by **dbt**
- Modular, reproducible design ready for automation

**Goal:** Show hands-on experience in production-style data engineering workflows and tooling.

## Stack & Tools Used

- **Docker & Docker Compose**
- **PostgreSQL (Source + Destination)**
- **Python (for ELT scripting)**
- **dbt (data modeling + transformation)**
- Bash, `pg_dump`, `psql`, Jinja SQL

## Docker Setup Process

Used Docker to containerize a sample app. Built with:
- `docker build -t getting-started .`
- `docker run -dp 127.0.0.1:3000:3000 getting-started`

## Docker Volume Setup

Created a persistent volume using:
- `docker volume create todo-db`
- Mounted to `/etc/todos` for persistent app data.

## MySQL Container Setup

Note: This was part of earlier Docker practice and not used in the final pipeline.

- Create a Docker network to enable container communication:
```
docker network create todo-app
```

- Run the MySQL container with environment variables for root password and database name:
```
docker run -d \
  --network todo-app --network-alias mysql \
  -v todo-mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=todos \
  mysql:8.0
```

- Connect to the running MySQL container:
```
docker exec -it <mysql-container-id> mysql -u root -p
```

- Confirm the `todos` database exists by listing all databases:
```
-- List all available databases to confirm 'todos' was created
SHOW DATABASES;
```

- Verify that todo items are being stored in the MySQL database:
```
docker exec -it <mysql-container-id> mysql -p todos
```
```
SELECT * FROM todo_items;
```
- This confirms the application is correctly writing todo items to the database.

## Docker Compose Setup

- Created a `compose.yaml` file to define and manage multi-container services.
- Defined services for both the app and MySQL, specifying image, ports, volumes, environment variables, and working directory.
- Used `docker compose up -d` to bring up the full stack with a single command.
- Stopped and removed containers with `docker compose down`.
- Replaced manual container management with Docker Compose to improve reproducibility, maintainability, and scalability.

- Completed Docker section

## Pipeline Breakdown

**1. Ingestion Layer (Source Postgres)**
- Starts with sample user and film data loaded into a source PostgreSQL container.

**2. ELT Script (Python)**
- Waits for DB to be ready
- Uses `pg_dump` to extract from source
- Uses `psql` to load into destination Postgres

**3. dbt Transformation**
- dbt models transform raw data into clean views with business logic (e.g., rating categories, actor summaries)
- Final outputs: 5+ relational tables materialized in `destination_postgres`

## Source Data Setup & SQL Practice

- Deployed a PostgreSQL container using the official image.
- Created `users` and `films` tables with constraints and inserted sample records:

```sql
INSERT INTO films (title, release_date, price, rating, user_rating) VALUES
('Inception', '2010-07-16', 12.99, 'PG-13', 4.8),
('The Shawshank Redemption', '1994-09-23', 9.99, 'R', 4.9),
('The Godfather', '1972-03-24', 14.99, 'R', 4.7);
```

- Demonstrated SQL fundamentals using `SELECT`, `LIMIT`, `COUNT`, `JOIN`, and `UNION` queries.

## Dockerized ELT Pipeline

- Built a Dockerized ELT pipeline with three coordinated services:
  - `source_postgres`: seeded with schema and sample data
  - `destination_postgres`: the target PostgreSQL instance
  - `elt_script`: Python container that handles data transfer using `pg_dump` and `psql`

- Defined orchestration via `docker-compose.yaml` and custom Dockerfile in `elt_script/`

- ELT script workflow:
  1. Waits for source DB readiness via `pg_isready`
  2. Dumps `source_postgres` to `data_dump.sql`
  3. Loads data into `destination_postgres` via `psql`

- Result: clean, reproducible cross-database migration handled entirely in containers

## Architecture Overview

```
+------------------+        +-------------------+        +---------------------+
| source_postgres  | -----> |   ELT Python      | -----> | destination_postgres|
+------------------+        +-------------------+        +---------------------+
                               |
                               |
                          [dbt run]
                               ↓
                     Transformed business tables
```

---

## Why This Project Matters

- Shows **proficiency** in Dockerized workflows
- Demonstrates **data flow orchestration** with real-world tooling
- Hands-on use of **dbt**, a critical skill for modern data teams
- Highlights **problem-solving** and debugging in multi-container systems

## dbt Transformation Workflow

- Implemented dbt transformations on ELT-loaded tables in `destination_postgres`
- Created four models in the `models/example/` directory:
  - `films.sql`
  - `film_actors.sql`
  - `film_category.sql`
  - `film_ratings.sql`
- Used CTEs and Jinja templating to build derived tables (e.g. rating categories, actor aggregates)
- Executed `dbt run` to compile and apply transformations in PostgreSQL
- Verified model creation in the `destination_db` schema using `psql`
- Final models include business logic transformations and SQL best practices for portability and reuse

- Final database schema includes:

  ```
  List of relations
  Schema | Name           | Type  | Owner
  -------+----------------+-------+---------
  public | actors         | table | postgres
  public | film_actors    | table | postgres
  public | film_category  | table | postgres
  public | film_ratings   | table | postgres
  public | films          | table | postgres
  public | users          | table | postgres
  ```

- Demonstrated a complete data pipeline: from ingestion → ELT → transformation → materialized data models
- Sample transformed data in the `film_ratings` model:

  ```
   film_id | title                     | release_date | price | rating | user_rating | rating_category |      actors
  ---------+---------------------------+--------------+-------+--------+--------------+------------------+---------------------
        1 | Inception                 | 2010-07-16   | 12.99 | PG-13  | 4.8          | Excellent        | Leonardo DiCaprio
        2 | The Shawshank Redemption | 1994-09-23   |  9.99 | R      | 4.9          | Excellent        | Tim Robbins
        3 | The Godfather            | 1972-03-24   | 14.99 | R      | 4.7          | Excellent        | Marlon Brando
        4 | The Dark Knight          | 2008-07-18   | 11.99 | PG-13  | 4.8          | Excellent        | Christian Bale
        5 | Pulp Fiction             | 1994-10-14   | 10.99 | R      | 4.6          | Excellent        | John Travolta
  ```

- This demonstrates the final result of dbt transformations combining ratings, user reviews, and actor information into a single clean view.