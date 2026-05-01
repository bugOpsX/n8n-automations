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
