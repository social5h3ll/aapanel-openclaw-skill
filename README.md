# aaPanel / BT-Panel — OpenClaw Skill

![aaPanel OpenClaw Skill](icon/bt.png)

**Version:** 0.1.0-beta  
**Author:** [5H3LL / social5h3ll](https://github.com/social5h3ll)  
**License:** MIT  
**Repository:** https://github.com/social5h3ll/aapanel-openclaw-skill

---

A fully-featured [OpenClaw](https://github.com/openclaw/openclaw) skill for managing and monitoring **aaPanel/BT-Panel** servers. Connect one or more aaPanel instances and run health checks, inspect services, manage files, audit SSH logs, and more — directly from OpenClaw.

Built for homelab enthusiasts, sysadmins, and developers who run aaPanel as their server management panel.

---

## Features

### 🔍 Server Monitoring
- **System resources** — CPU usage, RAM, disk (per partition), network throughput, load averages
- **Site status** — All sites/projects with running/stopped state, SSL certificate expiry tracking
- **Service status** — Nginx, Apache, MySQL, Redis, Memcached, PHP (multi-version), PostgreSQL
- **Log reading** — Nginx, Apache, MySQL error/slow logs, Redis, PostgreSQL
- **SSH audit** — Failed/successful login attempts with IP geolocation, firewall status, Fail2Ban state
- **Cron job inspection** — Backup tasks, shell scripts, sync jobs, log rotation

### 📁 File Operations
- Browse remote directories with pagination
- Read files (full content or last N lines)
- Edit files with concurrency protection
- Create files and directories
- Delete to aaPanel recycle bin
- chmod/chown with recursive directory support

### ⚙️ Multi-Server Management
- Add, list, and remove multiple aaPanel servers via config tool
- Per-server health checks with configurable alert thresholds
- Global threshold configuration in `~/.openclaw/bt-skills.yaml`

---

## Requirements

- **aaPanel / BT-Panel:** >= 9.0.0
- **Python:** >= 3.10
- **Dependencies:** `requests`, `pyyaml`, `rich`

```bash
pip install requests pyyaml rich
```

---

## Quick Start

### 1. Add a Server

```bash
python3 scripts/bt-config.py add \
  -n my-server \
  -H https://panel.example.com:8888 \
  -t YOUR_API_TOKEN
```

> **Self-signed certificate?** Add `--verify-ssl false` to the add command.

**Getting an API token:**
1. Log in to your aaPanel web UI
2. Go to **Panel Settings → API Interface**
3. Click **Get API Token**
4. Ensure your calling server's IP is allowed in the aaPanel IP whitelist

### 2. Run a Health Check

```bash
# Full system resource check
python3 scripts/monitor.py --server my-server --format table

# Site status overview
python3 scripts/sites.py --server my-server

# Service status
python3 scripts/services.py --server my-server
```

### 3. Browse Server Files

```bash
# List a directory
python3 scripts/files.py ls /www

# Read a file
python3 scripts/files.py cat /www/server/nginx/conf/nginx.conf

# Edit a file
python3 scripts/files.py edit /www/config.php "new content"
```

---

## CLI Reference

### Monitoring

| Command | Description |
|---------|-------------|
| `python3 scripts/monitor.py` | System resources for all servers (JSON) |
| `python3 scripts/monitor.py --server s1 --format table` | Single server, table output |
| `python3 scripts/sites.py --filter ssl-warning` | Sites with SSL expiring within 30 days |
| `python3 scripts/sites.py --filter stopped` | Sites that are stopped |
| `python3 scripts/services.py` | All services across all servers |
| `python3 scripts/services.py --service nginx --service redis` | Specific services |
| `python3 scripts/ssh.py --logs --filter failed` | Failed SSH login attempts |
| `python3 scripts/ssh.py --status` | SSH service status |
| `python3 scripts/logs.py --service nginx --lines 100` | Last 100 lines of Nginx error log |
| `python3 scripts/logs.py --service mysql --log-type slow` | MySQL slow query log |
| `python3 scripts/crontab.py --backup-only` | All scheduled backup tasks |

### File Operations

| Command | Description |
|---------|-------------|
| `python3 scripts/files.py ls /www` | List directory contents |
| `python3 scripts/files.py cat /path/to/file` | Read file content |
| `python3 scripts/files.py cat /path/to/file -n 50` | Last 50 lines |
| `python3 scripts/files.py edit /path/to/file "content"` | Edit / overwrite file |
| `python3 scripts/files.py edit /path/to/file -f ./local.txt` | Edit from local file |
| `python3 scripts/files.py mkdir /www/newdir` | Create directory |
| `python3 scripts/files.py touch /www/newfile.txt` | Create empty file |
| `python3 scripts/files.py rm /www/old.txt` | Delete file (to recycle bin) |
| `python3 scripts/files.py chmod 755 /www/file.txt` | Change permissions |
| `python3 scripts/files.py chmod 755 /www/dir -R` | Recursive chmod |
| `python3 scripts/files.py stat /www/file.txt` | View file permissions |
| `python3 scripts/download.py --url "https://..."` | Download file to server |
| `python3 scripts/unzip.py /www/archive.zip` | Extract ZIP on server |

### Config Management

| Command | Description |
|---------|-------------|
| `python3 scripts/bt-config.py list` | List all configured servers |
| `python3 scripts/bt-config.py add -n s1 -H https://... -t TOKEN` | Add a server |
| `python3 scripts/bt-config.py remove my-server` | Remove a server |
| `python3 scripts/bt-config.py threshold --cpu 75 --memory 80` | Set alert thresholds |

---

## Alert Thresholds

Edit `~/.openclaw/bt-skills.yaml` to configure global alert thresholds:

```yaml
global:
  thresholds:
    cpu: 80      # CPU usage alert threshold (%)
    memory: 85   # Memory usage alert threshold (%)
    disk: 90     # Disk usage alert threshold (%)
```

### SSL Certificate Alerts

| Condition | Level |
|-----------|-------|
| Certificate expired | 🔴 Critical |
| ≤ 7 days until expiry | 🔴 Critical |
| ≤ 30 days until expiry | 🟡 Warning |

---

## Config File

Server configurations and API tokens are stored in:

```
~/.openclaw/bt-skills.yaml
```

This file is local to your machine — **it is never committed to the repository**.

---

## aaPanel

This skill is an independent open source project — it is not affiliated with, sponsored by, or endorsed by [aaPanel](https://github.com/aaPanel) or BT-Security.

- **aaPanel GitHub:** https://github.com/aaPanel
- **aaPanel Website:** https://www.aapanel.com

---

## Troubleshooting

```bash
# Verify Python environment
python3 scripts/check_env.py

# Test server connection
python3 scripts/monitor.py --server my-server

# Check configured servers
python3 scripts/bt-config.py list
```

### Common Issues

**"Config file not found"** — Run `bt-config.py add` to add your server first.

**"Connection refused"** — Ensure aaPanel API is enabled and your IP is whitelisted in **Panel Settings → API Interface**.

**"SSL verify failed"** — If using a self-signed certificate, add `--verify-ssl false` when adding the server.

---

## Contributing

Issues, feature requests, and pull requests are welcome. If you find bugs or want to add features, open an issue first to discuss.

---

## Changelog

### v0.1.0-beta (2026-04-10)
- Initial combined release — monitoring + file management in one skill
- Supports aaPanel/BT-Panel >= 9.0.0
