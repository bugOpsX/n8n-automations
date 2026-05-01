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
