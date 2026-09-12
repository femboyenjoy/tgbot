# Telegram Server Bot (Improved)

A Telegram bot that executes shell commands on a remote server.

## Fixes from original

Original code hangs on commands like `ping` that require Ctrl+C to exit.

| Problem | Fix |
|---------|-----|
| `ping` hangs 300s | Default timeout reduced to **30s** |
| Subprocess survives timeout | `os.setsid` + `os.killpg` kills entire process tree |
| No output on timeout | Sends **SIGINT** first (simulates Ctrl+C), waits 3s, collects partial output, then **SIGKILL** |
| Cannot customize timeout | `/run 10 ping google.com` — first number = timeout in seconds |
| No background execution | Added `/runbg <command>` |

## Commands

| Command | Description |
|---------|-------------|
| `/start` | Show help |
| `/run <command>` | Execute shell command (default 30s timeout) |
| `/run <seconds> <command>` | Execute with custom timeout |
| `/runbg <command>` | Run in background, send result when done |
| `/getfile <path>` | Download a file from the server |
| `/ps` | List background tasks |

## Setup

### Environment variable



### Run directly



### As a systemd service



## Requirements

- Python 3.6+
- `requests` library

Looking in indexes: https://mirrors.huaweicloud.com/repository/pypi/simple
Requirement already satisfied: requests in /usr/local/python3.11/lib/python3.11/site-packages (2.34.2)
Requirement already satisfied: charset_normalizer<4,>=2 in /usr/local/python3.11/lib/python3.11/site-packages (from requests) (3.5.1)
Requirement already satisfied: idna<4,>=2.5 in /usr/local/python3.11/lib/python3.11/site-packages (from requests) (3.19)
Requirement already satisfied: urllib3<3,>=1.26 in /usr/local/python3.11/lib/python3.11/site-packages (from requests) (2.7.0)
Requirement already satisfied: certifi>=2023.5.7 in /usr/local/python3.11/lib/python3.11/site-packages (from requests) (2026.7.22)
