# 🚀 n8n Docker Setup (PostgreSQL + 24/7 Uptime)

This guide explains how to set up **n8n using Docker + Docker Compose + PostgreSQL** with persistent storage and automatic restart for 24/7 uptime.

Works on:
- ✅ Ubuntu / Linux
- ✅ Windows (Docker Desktop with WSL2)

---

# 📌 Why This Setup?

- Uses **PostgreSQL** (production ready)
- Auto-restart on crash or reboot
- Persistent workflows & credentials
- Encryption key configured
- Clean, portable architecture

---

# 🧠 Architecture Overview
Browser → n8n (Docker) → PostgreSQL (Docker)


- n8n = Application
- PostgreSQL = Database (stores workflows, credentials, execution history)

---

# ⚙️ Step 1 — Install Docker

## Ubuntu

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl enable docker
sudo systemctl start docker
```

Verify:

```bash
docker --version
docker-compose --version
```

## Windows

Install Docker Desktop

Enable:
- WSL2
- Linux Containers

Restart system

Verify in PowerShell:

```powershell
docker --version
docker compose version
```

# 📁 Step 2 — Create Project Structure

## Linux
```bash
mkdir n8n
cd n8n
mkdir -p data/n8n data/postgres
sudo chown -R 1000:1000 data
```

## Windows (PowerShell)
```powershell
mkdir C:\n8n
cd C:\n8n
mkdir data\n8n
mkdir data\postgres
```

# 📄 Step 3 — Create docker-compose.yml

Create file:

`docker-compose.yml`

Paste:

```yaml
version: "3.8"

services:
  postgres:
    image: postgres:15
    restart: always
    environment:
      POSTGRES_USER: n8n
      POSTGRES_PASSWORD: n8npassword
      POSTGRES_DB: n8n
    volumes:
      - ./data/postgres:/var/lib/postgresql/data

  n8n:
    image: n8nio/n8n:latest
    restart: always
    ports:
      - "5678:5678"
    environment:
      DB_TYPE: postgresdb
      DB_POSTGRESDB_HOST: postgres
      DB_POSTGRESDB_PORT: 5432
      DB_POSTGRESDB_DATABASE: n8n
      DB_POSTGRESDB_USER: n8n
      DB_POSTGRESDB_PASSWORD: n8npassword

      N8N_BASIC_AUTH_ACTIVE: "true"
      N8N_BASIC_AUTH_USER: admin
      N8N_BASIC_AUTH_PASSWORD: strongpassword

      N8N_ENCRYPTION_KEY: my_super_long_random_secret_key

      N8N_HOST: localhost
      N8N_PORT: 5678
      N8N_PROTOCOL: http

      GENERIC_TIMEZONE: Asia/Kolkata
      NODE_ENV: production

    volumes:
      - ./data/n8n:/home/node/.n8n

    depends_on:
      - postgres
```

⚠️ Change passwords before production use.

# ▶️ Step 4 — Start n8n

From project folder:

## Linux
```bash
docker-compose up -d
```

## Windows
```powershell
docker compose up -d
```

# 📜 Step 5 — Check Status
```bash
docker ps
```

Check logs:

```bash
docker-compose logs -f n8n
```

Wait until you see:

`n8n ready on 0.0.0.0, port 5678`

# 🌐 Step 6 — Access n8n

Open browser:

http://localhost:5678

Login:

Username: admin
Password: strongpassword

# 🔁 Step 7 — Test 24/7 Uptime

Reboot system:

```bash
sudo reboot
```

After restart:

```bash
docker ps
```

If containers are running → 24/7 uptime confirmed.

# 🎯 Final Result

You now have:

✅ PostgreSQL-backed n8n
✅ Persistent data
✅ Encrypted credentials
✅ Automatic restart
✅ 24/7 uptime
✅ Production-ready setup

Happy Automating ⚡


> Perfect for development. Not ideal for production.
