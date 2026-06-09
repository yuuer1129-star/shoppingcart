# Shopping Cart Database Configuration

This project contains the Docker configuration for setting up a **PostgreSQL 18** database named **`SHOPPINGCART`**.

## Prerequisites

1. **Docker Desktop** installed and running on your machine.
2. **Docker Compose** (included with Docker Desktop).

## Configuration

The database is configured in the root [docker-compose.yml](../docker-compose.yml):

- **Image**: `postgres:18-alpine`
- **Database Name**: `SHOPPINGCART`
- **User**: `postgres`
- **Password**: `mysecretpassword`
- **Port**: `5432`
- **Data Persistence**: Uses a named volume `pgdata` to persist database files across restarts.

## How to Start the Database

1. Ensure **Docker Desktop** is open and running.
2. Open your terminal in this directory and run:
   ```bash
   docker compose up -d
   ```

## How to Access the Database

You can connect to the database using any PostgreSQL client (e.g., pgAdmin, DBeaver) or via terminal:

### CLI Access
To connect directly inside the running container:
```bash
docker exec -it shoppingcart-postgres psql -U postgres -d SHOPPINGCART
```

## How to Stop the Database

To stop the container without losing your data:
```bash
docker compose down
```
