# Two-Tier Flask Application with Docker

This project demonstrates a **Two-Tier Web Application** built using **Flask (Python)** and **MySQL**, fully containerized using **Docker**.

The architecture separates the backend and database layers into separate containers and shows how they communicate using Docker networking. This setup simulates real-world deployment and DevOps fundamentals like containerization, environment-based configuration, and data persistence.

---

## Project Overview

A **Two-Tier Application** consists of:

| Layer             | Technology                              |
| ----------------- | --------------------------------------- |
| Application Layer | Flask (Python)                          |
| Data Layer        | MySQL                                   |
| Containerization  | Docker                                  |
| Deployment Model  | Multi-container using Docker networking |

*Each tier runs in its own container and connects via a custom Docker bridge network.*

---

## Project Structure

```
Project-2-Two-Tier-Flask-using-Docker/
├── app.py
├── Dockerfile
├── requirements.txt
├── docker-compose.yml
├── message.sql
├── templates/
├── static/
└── README.md
```

---

## Prerequisites

Before running the project locally, make sure you have:

✔ Docker installed
✔ Git (to clone the repository)
✔ Bash terminal / Linux environment

Verify Docker:

```bash
docker --version
```

---

## 🧱 Architecture Diagram

```
Browser (Client)
     ↓
Flask Backend (Docker Container)
     ↓
MySQL Database (Docker Container)
```

* Flask receives requests from the user.
* Flask stores/retrieves data from MySQL.
* MySQL stores data in persistent storage.

---

## How to Deploy Locally (Manual Docker)

### 1️⃣ Clone the Repository

```bash
git clone https://github.com/Shipra-SG/Project-2-Two-Tier-Flask-using-Docker.git
cd Project-2-Two-Tier-Flask-using-Docker
```

---

### 2️⃣ Build Flask Docker Image

```bash
docker build -t flaskapp .
```

This builds the Flask backend into a Docker image.
Images are local copies of your application that can be run as containers.

---

### 3️⃣ Create a Docker Network

```bash
docker network create twotier
```

This makes sure both containers can communicate by service name.

---

### 4️⃣ Run MySQL Container

```bash
docker run -d \
  --name mysql \
  --network twotier \
  -v mysql-data:/var/lib/mysql \
  -e MYSQL_DATABASE=mydb \
  -e MYSQL_ROOT_PASSWORD=admin \
  -p 3306:3306 \
  mysql:5.7
```

* `-v mysql-data:/var/lib/mysql` ensures the database is persistent if the container restarts.

---

### 5️⃣ Run Flask Container

```bash
docker run -d \
  --name flaskapp \
  --network twotier \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=admin \
  -e MYSQL_DB=mydb \
  -p 5000:5000 \
  flaskapp:latest
```

Here, the backend connects to MySQL using Docker network DNS (`mysql`).
Environment variables are used to pass secure credentials.

---

## Test the Application

### Open in Browser

```
http://localhost:5000
```

Use the form to add a message - it should be stored in the database.

### Validate with MySQL

```bash
docker exec -it mysql mysql -u root -p
```

Then inside MySQL:

```sql
USE mydb;
SELECT * FROM messages;
```

---

## Docker Compose (For running multiple containers)

If you want to run everything with one command:

```
docker compose up --build -d
```

> This will spin up both the Flask backend and MySQL database together.
> (Only works if `docker-compose.yml` is configured properly.) 

---

## Persistent Data with Volumes

MySQL uses a Docker volume (`mysql-data`) so data persists even if the MySQL container is removed.
This is important for real deployments to ensure data is not lost.

---

## Environment Variables (Explained)

| Variable         | Purpose                                 |
| ---------------- | --------------------------------------- |
| `MYSQL_HOST`     | Hostname of the MySQL service container |
| `MYSQL_USER`     | Database user                           |
| `MYSQL_PASSWORD` | Password for the DB user                |
| `MYSQL_DB`       | Database name                           |

Pass these securely using `-e` flags or `.env` file.

---

## Best Practices Followed

✔ Separate containers for backend & DB
✔ Use Docker network for service discovery
✔ Persistent storage with Docker volume
✔ Environment variables for config
✔ One-command deployment option via Docker Compose

---

## Learned Concepts

* Dockerizing Python applications
* Container networking
* Working with MySQL inside Docker
* Environment-based configuration
* Manual container orchestration with Docker CLI

---

## Notes

* Make sure **no other service is running on port 5000** before starting containers.
* Always rebuild images after code changes:

  ```bash
  docker build --no-cache -t flaskapp .
  ```
* If MySQL fails on startup, ensure the Docker network exists:

  ```bash
  docker network ls
  ```

---

## Troubleshooting

### If containers aren’t communicating:

Check the network with:

```bash
docker network inspect twotier
```

### If backend logs errors:

```bash
docker logs flaskapp
```

---

## Use Case

> This project simulates a real-world containerized backend deployment where an application directly interacts with a database in a two-tier architecture using Docker containers - a fundamental skill in modern DevOps workflows.
---

## Typical Usage Flow

```
docker network create twotier
docker run mysql
docker run flaskapp
open browser → http://localhost:5000
```
