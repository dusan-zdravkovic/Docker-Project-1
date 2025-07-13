# Data Engineering Portfolio Project

This project is a hands-on portfolio piece demonstrating real-world data engineering skills using Docker, SQL, and a custom ELT pipeline.

It was built by following and extending Docker’s official getting started guide: https://docs.docker.com/get-started/

Key skills showcased:
- Docker containerization and orchestration
- SQL (PostgreSQL & MySQL) schema design and querying
- ELT pipeline development with Python and Docker Compose
- Workflow automation concepts (e.g., CRON, Airflow)
- Modern data ingestion tooling (e.g., Airbyte)

## Docker Setup Process

- Start with the sample app repository:
`git clone https://github.com/docker/getting-started-app.git`

- Build the Docker image:
```
docker build -t getting-started .
```

- Launch the app container on port 3000:
```
docker run -dp 127.0.0.1:3000:3000 getting-started
```

### Step 4: Add Volume for Persistence

- Create a Docker volume to persist data:
```
docker volume create todo-db
```

- Launch the container with the volume mounted at `/etc/todos` inside the container:
```
docker run -dp 127.0.0.1:3000:3000 \
  --mount type=volume,src=todo-db,target=/etc/todos \
  getting-started
```

### Step 5: Set Up MySQL Container

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

### Step 6: Docker Compose Setup

- Created a `compose.yaml` file to define and manage multi-container services.
- Defined services for both the app and MySQL, specifying image, ports, volumes, environment variables, and working directory.
- Used `docker compose up -d` to bring up the full stack with a single command.
- Stopped and removed containers with `docker compose down`.
- Replaced manual container management with Docker Compose to improve reproducibility, maintainability, and scalability.

- Completed Docker section

## PostgreSQL SQL Practice

- Deployed a PostgreSQL container using the official image
- Created a `users` table and inserted sample records for querying and testing
- Created a `films` table with constraints (e.g., `CHECK` on `user_rating`) and inserted 20+ movie records
  ```sql
  INSERT INTO films (title, release_date, price, rating, user_rating)
  VALUES ('Inception', '2010-07-16', 12.99, 'PG-13', 4.8);
  ```
- Demonstrated SQL fundamentals using `SELECT`, `LIMIT`, `COUNT`, `JOIN`, and `UNION` queries

## PostgreSQL ELT Pipeline

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

- Demonstrated real-world automation, error handling, and container communication in a production-style setup

---

## dbt Integration (Completed)

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

- Demonstrated a complete data pipeline: from ingestion → ELT → transformation → materialized data models