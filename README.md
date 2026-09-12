# Telegram Server Bot (v2)

A Telegram bot that executes shell commands on a remote server.

## What's fixed

### v2 changes (from v1)

| Issue | Fix |
|-------|-----|
| `/run` blocks the bot for up to 30s | `/run` now always runs in a **background thread**, bot stays fully responsive |
| `_bg_tasks` never populated, `/ps` always empty | Tasks properly tracked with thread-safe `_bg_lock` |
| `preexec_fn=os.setsid` | Replaced with **`start_new_session=True`** (Python-recommended) |
| Timeout message always says "SIGINT" even when SIGKILL was used | Tracks `force_killed` flag, distinguishes **SIGINT** vs **SIGKILL** in output |
| `/runbg` redundant now | Removed — `/run` is always non-blocking |

### Original → v1 fixes

| Problem | Fix |
|---------|-----|
| `ping` hangs 300s | Default timeout **30s** |
| Subprocess survives timeout | `os.killpg` kills entire process tree |
| No output on timeout | **SIGINT** first (Ctrl+C), wait 3s, collect partial output, then **SIGKILL** |
| Cannot customize timeout | `/run 10 ping google.com` syntax |

## Commands

| Command | Description |
|---------|-------------|
| `/start` | Show help |
| `/run <command>` | Execute shell command (default 30s timeout, non-blocking) |
| `/run <seconds> <command>` | Execute with custom timeout |
| `/getfile <path>` | Download a file from the server |
| `/ps` | Show running/completed tasks |

## How `/run` works

```
/run ping google.com
   ↓
Bot immediately replies: "⏳ 开始执行..."
   ↓ (bot is free to handle other messages)
Background thread executes command
   ↓
On timeout: SIGINT → wait 3s → SIGKILL if needed
   ↓
Bot sends result with exit code + output
```

## Setup

### Environment variable

```bash
export BOT_TOKEN="your-telegram-bot-token"
```

### Run directly

```bash
python3 tgbot.py
```

### As a systemd service

```ini
[Unit]
Description=Telegram Bot
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/python3 /path/to/tgbot.py
Environment="BOT_TOKEN=your-token"
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

## Requirements

- Python 3.6+
- `requests` library
