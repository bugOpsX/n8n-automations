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

# 🔄 Restoring from Backup

If you previously backed up:

`data/postgres`

Steps:

Stop containers:

```bash
docker-compose down
```

Replace `data/postgres` with backup

Fix permissions (Linux only):

```bash
sudo chown -R 999:999 data/postgres
```

Start again:

```bash
docker-compose up -d
```

Your workflows and credentials will be restored automatically.

# 🛡️ Backup Strategy

Always back up:

- `data/postgres`
- `data/n8n`

These folders contain:

- Workflows
- Credentials
- Execution history
- Encryption keys

# 🚀 Production Upgrade Ideas

- Add domain + HTTPS (Nginx reverse proxy)
- Deploy on VPS (AWS / Oracle / DigitalOcean)
- Add Cloudflare tunnel
- Automate database backups
- Scale using Docker Swarm or Kubernetes

# 🎯 Final Result

You now have:

✅ PostgreSQL-backed n8n
✅ Persistent data
✅ Encrypted credentials
✅ Automatic restart
✅ 24/7 uptime
✅ Production-ready setup

Happy Automating ⚡

# 🌍 Public Access Using ngrok (Temporary Public URL)

If you want to access your local n8n instance from anywhere (for webhooks, testing, demos), you can use **ngrok**.

⚠️ This is ideal for development and testing.  
For production, use a proper domain + HTTPS reverse proxy.

---

# 🔹 Step 1 — Install ngrok

## Windows

1. Download from: https://ngrok.com/download
2. Extract the zip file
3. Place `ngrok.exe` inside:

`C:\ngrok`

4. Add `C:\ngrok` to System Environment Variables (optional but recommended)

---

## Ubuntu / Linux

```bash
sudo snap install ngrok
```

OR download manually from official site.

# 🔹 Step 2 — Connect ngrok to Your Account

Create account at:
https://dashboard.ngrok.com

Copy your auth token

Run:

```bash
ngrok config add-authtoken YOUR_AUTH_TOKEN
```

# 🔹 Step 3 — Start n8n First

Make sure n8n is running:

```bash
docker compose up -d
```

Verify:

```bash
docker ps
```

# 🔹 Step 4 — Expose n8n Port

n8n runs on:

http://localhost:5678

Now expose it:

```bash
ngrok http 5678
```

You will see:

`Forwarding https://random-id.ngrok-free.app -> http://localhost:5678`

Copy the HTTPS URL.

Example:

`https://abc123.ngrok-free.app`

# 🔹 Step 5 — Update n8n Webhook Configuration

Stop containers:

```bash
docker compose down
```

Edit `docker-compose.yml` and update:

```yaml
N8N_HOST: abc123.ngrok-free.app
N8N_PROTOCOL: https
WEBHOOK_URL: https://abc123.ngrok-free.app/
```

Start again:

```bash
docker compose up -d
```

# 🔹 Step 6 — Access n8n Publicly

Open:

https://abc123.ngrok-free.app

Now:

- Webhooks work externally
- Telegram bots work
- Public APIs can reach your n8n

# ⚠️ Important Notes

- Free ngrok URLs change every time you restart ngrok
- You must update WEBHOOK_URL when URL changes
- Keep ngrok terminal running
- Do not use free ngrok for production environments

# 🚀 Recommended for Production

Instead of ngrok:

- Use VPS (AWS / Oracle / DigitalOcean)
- Add Nginx reverse proxy
- Install SSL using Let's Encrypt
- Use fixed domain

# 🎯 Summary

Using ngrok allows:

- Remote testing
- Webhook testing
- Public demo
- Quick integration setup

Perfect for development. Not ideal for production.
