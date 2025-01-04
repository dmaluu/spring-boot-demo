## Endpoints

- GET /book will return an array of books
- POST /book with request body 

```json
{
  "id": 1,
  "title": "Book B",
  "author": "Peter",
  "publishedDate": "2021-01-01"
}
```

See the RestController class file [here](./src/main/java/com/example/demo/controller/BookController.java).
# Setup Instructions (Normal Docker)

## STEP 1: Create the dockerfile

Create the docker file for java spring boot. Fill in the blank.
```dockerfile
FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
COPY src ./src
RUN <<install the packages>>
CMD ["./mvnw", "spring-boot:run"]
```

## Step 2 Build the docker image

```docker build -t "springboot:latest" .```

## Step 3 Create a docker container for postgres, and initialise it

```docker run -d -p 5122:5432 postgis/postgis:15-3.3```

The setup is using PostgreSQL. Use the following SQL Commands on localhost:5122 to setup a database.
```sql
CREATE DATABASE demodb;
CREATE USER demouser WITH ENCRYPTED PASSWORD 'password';
GRANT ALL PRIVILEGES ON DATABASE demodb to demouser; 
```

## Step 4 Create a docker container for springboot, get it to connect to db

```docker run -d -p 8080:8080 springboot:latest```

## Step 5 Check if app is initialised using docker log

## Step 6 Try to use the endpoints

# Setup Instructions (Docker Compose)

## STEP 0: Remove the previous docker images
```docker rm <spring boot container>```

## STEP 1: Create the docker-compose.yml

```yml
version: '3.8'
services:
  postgres:
    container_name: spring_demo_db
    image: postgis/postgis:15-3.3
    restart: always
    environment:
      - POSTGRES_USER=demouser
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=demodb
    ports:
      - "5122:5432"
    volumes:
      - ./postgres-data:/var/lib/postgresql/data
  app:
    container_name: spring_demo_app    
    build:
      dockerfile: ./Dockerfile
    depends_on:
      - postgres
    dns:
      - 8.8.8.8
    ports:
      - "8088:8080"
```

## STEP 2: Run docker compose

Navigate to the same directory as `docker-compose.yml` file and run the following command:

```sh
docker compose up
```

If you change any code/content, use the `--build` flag:

```sh
docker compose up --build
