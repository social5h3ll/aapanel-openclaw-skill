# btpanel — aaPanel / BT-Panel Skill for OpenClaw

**Version:** 0.1.0-beta  
**Server:** aaPanel/BT-Panel >= 9.0.0 | **Python:** >= 3.10

Combined server monitoring and file management skill for OpenClaw. Connect to your aaPanel/BT-Panel servers and monitor resources, check site/service status, manage files, and more — all from OpenClaw.

## Features

### Monitoring
- **System resources** — CPU, RAM, disk, network, load averages
- **Site status** — running/stopped, SSL certificates (expiry alerts), process info per site type
- **Service status** — Nginx, Apache, MySQL, Redis, Memcached, PHP multi-version, PostgreSQL
- **SSH logs** — failed/successful login attempts, geolocation, firewall status
- **Cron jobs** — backup tasks, shell scripts, sync jobs, log rotation
- **Log reading** — Nginx, Apache, MySQL error/slow, Redis, PostgreSQL

### File Operations
- **Browse** remote directories with pagination
- **Read** files with last-N-lines support
- **Edit** files with concurrency protection
- **Create** files and directories
- **Delete** to aaPanel recycle bin
- **Chmod/chown** with recursive support

## Quick Start

### 1. Install dependencies

```bash
pip install requests pyyaml rich
```

### 2. Add a server

```bash
python3 scripts/bt-config.py add \
  -n my-server \
  -H https://panel.example.com:8888 \
  -t YOUR_API_TOKEN
```

> **Self-signed cert?** Add `--verify-ssl false`

### 3. Run a health check

```bash
python3 scripts/monitor.py --server my-server --format table
python3 scripts/sites.py --server my-server
python3 scripts/services.py --server my-server
```

### 4. Browse server files

```bash
python3 scripts/files.py ls /www
python3 scripts/files.py cat /www/config.php
```

## Config File

Server configs and tokens are stored in `~/.openclaw/bt-skills.yaml` — **never commit this file to git**.

## aaPanel

- **aaPanel GitHub:** https://github.com/aaPanel
- **This skill:** independent open source project, not affiliated with aaPanel

## Notes

- Files deleted via this skill go to the aaPanel recycle bin, not permanent deletion
- Concurrency protection on file saves checks `st_mtime` to detect external modifications
- aaPanel API token requires IP whitelist access — ensure your calling server is allowed in panel settings
