# Data Engineering Project

Uses sample application for users following the getting started guide at https://docs.docker.com/get-started/.

- Learning Docker, SQL, Pipeline from scratch, CRON job, airflow, Airbyte

## Process - Docker

- Clone the starter repo
git clone https://github.com/docker/getting-started-app.git

- Build Docker image
```
docker build -t getting-started .
```

- Run the container on port 3000
```
docker run -dp 127.0.0.1:3000:3000 getting-started
```

### Add a volume for persistence

- Create a volume to store data
```
docker volume create todo-db
```

- Run the container and mount the volume to /etc/todos inside the container
```
docker run -dp 127.0.0.1:3000:3000 \
  --mount type=volume,src=todo-db,target=/etc/todos \
  getting-started
```

### Set up MySQL container

- Create a Docker network for app communication
```
docker network create todo-app
```

- Run the MySQL container with environment variables
```
docker run -d \
  --network todo-app --network-alias mysql \
  -v todo-mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=todos \
  mysql:8.0
```

- Connect to the MySQL container
```
docker exec -it <mysql-container-id> mysql -u root -p
```

- Show databases to verify `todos` exists
```
SHOW DATABASES;
```
