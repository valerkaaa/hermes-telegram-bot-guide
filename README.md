# 🤖 Hermes Agent — Telegram Bot Setup Guide

> Complete, beginner-friendly guide to connect a Telegram bot to [Hermes Agent](https://hermes-agent.nousresearch.com/).
> From @BotFather to a fully running Gateway — in 10 steps.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Hermes Agent](https://img.shields.io/badge/Hermes%20Agent-v0.16%2B-purple)](https://hermes-agent.nousresearch.com/)

---

## ✨ Features

| Capability | Description |
|---|---|
| 💬 **Text chat** | Talk to Hermes from anywhere |
| 🎤 **Voice messages** | Auto-transcribed speech |
| 🖼️ **Image analysis** | Send photos, get AI analysis |
| 📁 **File support** | Send documents, Hermes reads them |
| 📅 **Cron delivery** | Scheduled results in Telegram |
| 👥 **Group chats** | Topic threads support |
| 🌐 **Cross-platform** | Phone + Desktop + Web |

---

## 🚀 Quick Start

```bash
# 1. Create bot via @BotFather on Telegram → save the token
# 2. Get your user ID from @userinfobot

# 3. Add credentials
echo "TELEGRAM_BOT_TOKEN=your_token" >> ~/.hermes/.env
echo "TELEGRAM_ALLOWED_USERS=your_user_id" >> ~/.hermes/.env

# 4. Install dependencies
pip install python-telegram-bot --upgrade

# 5. Launch
hermes gateway run
```

---

## 📖 Full Guide

The complete step-by-step guide with all details, edge cases, troubleshooting, and commands:

➡️ **[GUIDE.md](GUIDE.md)** — 10 detailed steps (~15 min read)

### What's Inside

| # | Step | Description |
|---|---|---|
| 1 | 🪜 **Create Bot** | @BotFather, token generation |
| 2 | 🎨 **Customize** | Description, avatar, command menu |
| 3 | 🆔 **User ID** | Get your numeric Telegram ID |
| 4 | 🔧 **Configure .env** | Add token + ID to Hermes |
| 5 | 📦 **Dependencies** | Install python-telegram-bot + faster-whisper |
| 6 | 🚀 **Run Gateway** | Foreground, service, or background |
| 7 | ✅ **Test Bot** | Send a message, verify response |
| 8 | 👥 **Group Chat** | Privacy mode, admin setup, topic threads |
| 9 | 🔍 **Troubleshooting** | 7 common problems + fixes |
| 10 | ⌨️ **Commands** | Full Telegram command reference |
| — | ☁️ **Cloud & Pricing** | Run without PC, crypto payment, VPS hosting |

---

## ☁️ Cloud & Deployment

Run Hermes 24/7 — no PC needed. Control it from your phone via Telegram.

➡️ **[Full guide: Cloud options, pricing, crypto payments](GUIDE.md#-cloud--deployment-options-run-without-a-pc)**

| Option | Est. Cost/mo | Crypto? |
|---|---|---|
| OpenRouter Free + your PC | **$0** | — |
| OpenRouter PAYG + $5 VPS | **$8–$22** | ✅ USDC/USDT/ETH |
| Nous Portal + Modal | **$5–$25** | ❌ (card only) |
| Self-hosted GPU cloud | **$30–$150** | ✅ BTC/USDC |

**Crypto-friendly providers:** OpenRouter (MetaMask), Vultr, BuyVM, HostHatch, RunPod, Vast.ai

---

## 🧠 Advanced

- [Personality customization](GUIDE.md#-advanced-tips) via `SOUL.md`
- [Cron job delivery](GUIDE.md#cron-jobs-delivered-to-telegram) to Telegram
- [Multiple profiles](GUIDE.md#multiple-hermes-profiles) for different contexts

---

## 🔗 Links

| Resource | Link |
|---|---|
| Hermes Docs | https://hermes-agent.nousresearch.com/docs/ |
| Telegram Guide (Official) | https://hermes-agent.nousresearch.com/docs/user-guide/messaging/telegram/ |
| @BotFather | https://t.me/BotFather |
| @userinfobot | https://t.me/userinfobot |

---

## 📄 License

MIT