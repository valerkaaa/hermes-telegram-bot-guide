# Hermes Agent — Telegram Bot Setup Guide

Complete step-by-step guide to connect a Telegram bot to Hermes Agent.

[License: MIT](LICENSE)

## Overview

| Capability | Description |
|---|---|
| Text messages | Chat with Hermes from anywhere |
| Voice messages | Send voice memos — auto-transcribed |
| Images | Send photos — Hermes analyses them |
| File attachments | Send documents — Hermes reads them |
| Cron delivery | Scheduled task results arrive in Telegram |
| Group chats | Bot works in groups with topic support |
| Cross-platform | Same full power as the terminal CLI |

## Quick Start

```bash
# 1. Create a bot via @BotFather on Telegram, save the token
# 2. Get your numeric user ID via @userinfobot
# 3. Add to .env
echo "TELEGRAM_BOT_TOKEN=your_token_here" >> ~/.hermes/.env
echo "TELEGRAM_ALLOWED_USERS=123456789" >> ~/.hermes/.env
# 4. Install dependencies
pip install python-telegram-bot --upgrade
# 5. Run the gateway
hermes gateway run
```

## Full Guide

See the complete step-by-step guide with screenshots and troubleshooting:

- [GUIDE.md](GUIDE.md) — Full detailed guide (10 steps)

## License

MIT
