# Data Engineering Project

Uses sample application for users following the getting started guide at https://docs.docker.com/get-started/.

- Learning Docker, SQL, Pipeline from scratch, CRON job, Airflow, Airbyte

## Docker Setup Process

- Clone the starter repository:
`git clone https://github.com/docker/getting-started-app.git`

- Build the Docker image:
```
docker build -t getting-started .
```

- Run the container on port 3000:
```
docker run -dp 127.0.0.1:3000:3000 getting-started
```

### Step 4: Add Volume for Persistence

- Create a Docker volume to store data persistently:
```
docker volume create todo-db
```

- Run the container and mount the volume to the path `/etc/todos` inside the container:
```
docker run -dp 127.0.0.1:3000:3000 \
  --mount type=volume,src=todo-db,target=/etc/todos \
  getting-started
```

### Step 5: Set Up MySQL Container

- Create a Docker network for container communication:
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

- Verify that the `todos` database exists by showing databases:
```
-- List all available databases to confirm 'todos' was created
SHOW DATABASES;
```
