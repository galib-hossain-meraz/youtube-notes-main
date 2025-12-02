## YouTube Notes – Docker Run & Database Management Guide

This document explains how to start, stop, pause, and reset the database for the YouTube Notes project using Docker and Docker Compose.

> **Note:** Service names in this guide assume your `docker-compose.yml` uses names similar to `backend`, `frontend`, and `database` (or `db`).  
> If your actual service or volume names differ, replace them accordingly.

---

## 1. Prerequisites

- Docker and Docker Compose installed.
- Project root directory: `youtube-notes`.

From anywhere:

```bash
cd /youtube-notes
```

All commands below are run from this project root.

---

## 2. Starting the Project

### 2.1 Start all services (backend, frontend, database)

Run in detached mode (recommended for normal use):

```bash
docker compose up -d
```

Follow logs for all services:

```bash
docker compose logs -f
```

### 2.2 Start specific services only

For example:

```bash
docker compose up -d backend
docker compose up -d frontend
docker compose up -d database   # or db
```

---

## 3. Stopping Services

### 3.1 Stop all services

```bash
docker compose stop
```

Containers are stopped but not removed. You can start them again with:

```bash
docker compose start
```

### 3.2 Stop a specific service

```bash
docker compose stop backend
docker compose stop frontend
docker compose stop database   # or db
```

---

## 4. Pausing and Resuming Services

Pausing a container keeps it in memory but temporarily suspends CPU usage.

### 4.1 Pause all services

```bash
docker compose pause
```

### 4.2 Pause a specific service

```bash
docker compose pause backend
docker compose pause database   # or db
```

### 4.3 Resume (unpause) services

Resume all:

```bash
docker compose unpause
```

Resume specific:

```bash
docker compose unpause backend
docker compose unpause database   # or db
```

---

## 5. Shutting Down and Removing Containers

### 5.1 Remove containers and networks (keep database data)

```bash
docker compose down
```

This removes containers and related networks, but **keeps Docker volumes**, so your database data is preserved.

### 5.2 Remove everything including volumes (full reset)

```bash
docker compose down -v
```

This removes:

- Containers
- Networks
- Volumes (including PostgreSQL data)

> **Warning:** This completely clears the database and any other persisted data.

---

## 6. Clearing / Resetting the Database

You have several options depending on how aggressively you want to reset.

### 6.1 Full database reset via `down -v` (simple and clean)

This is the easiest way to fully clear the database:

```bash
# 1) Stop and remove all containers
docker compose down

# 2) Remove volumes as well (clears DB data)
docker compose down -v

# 3) Start everything fresh
docker compose up -d
```

All notes, users, and other DB records will be removed.

### 6.2 Clear only the database volume (by name)

If your `docker-compose.yml` defines a named volume for the database (for example `db_data`), you can delete just that volume:

```bash
# 1) Stop the database service
docker compose stop database   # or db

# 2) Remove the volume (replace with your actual volume name)
docker volume rm youtube-notes_db_data

# 3) Start the database again
docker compose up -d database   # or db
```

List volumes to find the exact name:

```bash
docker volume ls
```

---

## 7. Accessing the Database Manually (Optional)

If the database service is running (e.g. `database` or `db`), you can exec into it:

```bash
docker compose exec database bash
```

Inside the container, connect to PostgreSQL:

```bash
psql -U <db_user> -d <db_name>
```

Use the actual `db_user` and `db_name` from your `docker-compose.yml` or `.env` file.

---

## 8. Logs and Health Checks

### 8.1 View logs for a specific service

```bash
docker compose logs -f backend
docker compose logs -f frontend
docker compose logs -f database   # or db
```

### 8.2 List running containers

```bash
docker ps
```

---

## 9. Recommended Workflows

### 9.1 During normal development

First run or after major changes:

```bash
docker compose up -d --build
```

When you are done working:

```bash
docker compose stop
```

### 9.2 When you need a full reset (including DB)

```bash
docker compose down -v
docker compose up -d --build
```

---

## 10. Quick Reference

- **Start all services:**  
  `docker compose up -d`

- **Stop all services:**  
  `docker compose stop`

- **Pause / resume all services:**  
  `docker compose pause`  
  `docker compose unpause`

- **Full shutdown (keep data):**  
  `docker compose down`

- **Full reset including database:**  
  `docker compose down -v` then `docker compose up -d`

Adjust service and volume names if they differ from those used in this guide.
