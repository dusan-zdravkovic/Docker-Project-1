# Data Engineering Portfolio Project

This project serves as a portfolio showcase demonstrating practical skills using a sample application following the getting started guide at https://docs.docker.com/get-started/.

- Built Docker images and launched containers to run and test the app
- Docker containerization
- SQL and MySQL integration
- Data pipeline concepts
- Workflow automation tools (CRON, Airflow)
- Data ingestion tools (Airbyte)

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
- Demonstrated improved reproducibility and organization by replacing manual container startup steps with `docker compose`.

- Completed Docker section
