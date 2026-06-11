# 📖 Hermes Agent — Telegram Bot Setup Guide

> **Complete step-by-step guide to connect a Telegram bot to [Hermes Agent](https://hermes-agent.nousresearch.com/) — from creating a bot on Telegram to running the Gateway 24/7.**
>
> **Difficulty:** 🟢 Beginner-friendly
> **Time:** ~15–20 minutes
> **Platform:** Linux / macOS / Windows

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Prerequisites](#-prerequisites)
- [Step 1: Create a Bot via BotFather](#-step-1-create-a-bot-via-botfather)
- [Step 2: Customize Your Bot (Optional)](#-step-2-customize-your-bot-optional)
- [Step 3: Get Your Telegram User ID](#-step-3-get-your-telegram-user-id)
- [Step 4: Configure Hermes (.env)](#-step-4-configure-hermes-env)
- [Step 5: Install Dependencies](#-step-5-install-dependencies)
- [Step 6: Run the Gateway](#-step-6-run-the-gateway)
- [Step 7: Test Your Bot](#-step-7-test-your-bot)
- [Step 8: Group Chat Setup](#-step-8-group-chat-setup)
- [Step 9: Troubleshooting](#-step-9-troubleshooting)
- [Step 10: Telegram Commands Reference](#-step-10-telegram-commands-reference)
- [Cloud & Deployment Options](#-cloud--deployment-options-run-without-a-pc)
- [Advanced Tips](#-advanced-tips)
- [Useful Links](#-useful-links)
- [Checklist](#-checklist)

---

## 📖 Overview

| Capability | Description |
|---|---|
| 💬 **Text messages** | Chat with Hermes from anywhere |
| 🎤 **Voice messages** | Send voice memos — auto-transcribed |
| 🖼️ **Images** | Send photos — Hermes analyses them |
| 📁 **File attachments** | Send documents — Hermes reads them |
| 📅 **Cron delivery** | Scheduled task results arrive in Telegram |
| 👥 **Group chats** | Bot works in groups with topic support |
| 🌐 **Cross-platform** | Same full power as the terminal CLI |

### How It Works

```
[Telegram App] ←→ [Hermes Gateway] ←→ [Hermes Agent (LLM)]
                        ↕
              [Tools & Skills & Memory]
```

The **Gateway** is a background process that listens for messages from Telegram (and other platforms), forwards them to the Hermes AI agent, and sends responses back.

---

## 📦 Prerequisites

- ✅ **Hermes Agent installed** and working (`hermes --version` shows a version number)
- ✅ **Python 3.10+** with `pip` or `uv` package manager
- ✅ **A Telegram account** (any device)

---

## 🪜 Step 1: Create a Bot via BotFather

**@BotFather** is Telegram's official bot management tool. It issues API tokens that allow programs like Hermes to connect to Telegram.

### Instructions

1. Open Telegram on any device (phone, desktop, or web)
2. Find **@BotFather** — search for it or visit [t.me/BotFather](https://t.me/BotFather)
   - The real BotFather has a ✅ blue verification checkmark
3. Send the command:
   ```
   /newbot
   ```
4. BotFather asks for a **display name** (any name, users will see this in their chat list):
   ```
   Alright, a new bot. How are we going to call it? Please choose a name for your bot.
   ```
   For example:
   ```
   My Assistant Bot
   ```
5. Then it asks for a **username** — this must end in `bot` and be globally unique:
   ```
   Good. Now let's choose a username for your bot. It must end in 'bot'. Like this, for example: TetrisBot or tetris_bot.
   ```
   For example:
   ```
   my_assistant_bot
   ```
   > The username is used for the bot's link: `t.me/my_assistant_bot`

6. BotFather replies with your **API token**:
   ```
   Done! Congratulations on your new bot. You will find it at:
   t.me/my_assistant_bot

   Use this token to access the HTTP API:
   123456789:ABCdefGHIjklMNOpqrSTUvwxYZ
   ```

> [!CAUTION]
> **Keep your bot token secret!** Anyone with this token can control your bot.
> - If it leaks, immediately use `/revoke` in BotFather, then `/token` to get a new one
> - Store it only in your `~/.hermes/.env` file

7. Open your bot at `t.me/your_bot_username` and press **START** to activate the conversation

---

## 🎨 Step 2: Customize Your Bot (Optional)

Back in @BotFather, use these commands to polish your bot's appearance:

| Command | Purpose | Example Content |
|---|---|---|
| `/setdescription` | Bot bio (up to 512 chars) | "🤖 AI assistant for coding, research and analytics. Available 24/7." |
| `/setabouttext` | Short profile description (120 chars) | "AI assistant — code, research, data analysis" |
| `/setuserpic` | Avatar image (square, 512×512 px recommended) | Upload a PNG or JPEG |
| `/setcommands` | Slash-command menu for users | See recommended set below |

### Recommended Command List (`/setcommands`)

```
help — 📖 Help and command list
new — 🆕 Start new conversation
topic — 💬 Manage topic threads (groups)
speed — ⚡ Fast / full response mode
model — 🧠 Show current AI model
status — 📊 Current session info
```

After setting commands, users see a menu when they type `/` in your bot chat.

---

## 🆔 Step 3: Get Your Telegram User ID

> [!WARNING]
> Without `TELEGRAM_ALLOWED_USERS` configured, the bot ignores all incoming messages. This is a security measure to prevent spam and unauthorized access.

### Method 1: @userinfobot (Easiest)

1. Find [@userinfobot](https://t.me/userinfobot)
2. Send any message
3. Receive your info:
   ```
   Id: 123456789
   First name: YourName
   Language: en
   ```

### Method 2: @getmyid_bot

1. Find **@getmyid_bot**
2. Send `/start`
3. The bot displays your ID

> [!CAUTION]
> - `TELEGRAM_ALLOWED_USERS` must be your **personal numeric user ID** (obtained via @userinfobot or @getmyid_bot)
> - ❌ NOT the bot's own ID
> - ❌ NOT your @username
> - Setting the bot's ID in `ALLOWED_USERS` causes `403 Forbidden` (Telegram blocks bot-to-bot communication)

### Allowing Multiple Users

To allow several people to use your bot, list their IDs separated by commas:

```
TELEGRAM_ALLOWED_USERS=123456789,987654321,555666777
```

Each person gets their own ID from @userinfobot.

---

## 🔧 Step 4: Configure Hermes (.env)

The `.env` file at `~/.hermes/.env` stores API keys and secrets. This is where you tell Hermes about your Telegram bot.

> [!NOTE]
> On Windows, `~/.hermes/` typically resolves to `C:\Users\<YourUsername>\AppData\Local\hermes\`
> On Linux/macOS, it's `~/.hermes/` in your home directory.

### Option A: Terminal (Recommended)

```bash
cat >> ~/.hermes/.env << 'EOF'

# === TELEGRAM ===
TELEGRAM_BOT_TOKEN=***   TELEGRAM_ALLOWED_USERS=123456789
EOF
```

### Option B: Using a Text Editor

1. Navigate to your Hermes configuration directory (`~/.hermes/`)
2. Open or create the `.env` file (no file extension!)
3. Add these lines:
   ```
   TELEGRAM_BOT_TOKEN=123456>   TELEGRAM_ALLOWED_USERS=123456789
   ```
4. Save and close

### Option C: Interactive Setup

```bash
hermes setup gateway
```

This launches an interactive wizard that walks you through the Telegram configuration.

### Verification

Check that the values are set correctly:

```bash
grep TELEGRAM_BOT_TOKEN ~/.hermes/.env
grep TELEGRAM_ALLOWED_USERS ~/.hermes/.env
```

### Advanced: config.yaml Options

For fine-grained control, edit `~/.hermes/config.yaml`:

```yaml
gateway:
  platforms:
    telegram:
      enabled: true
      topics: false          # Enable topic threads in groups
      max_message_length: 4096  # Max response length
      parse_mode: MarkdownV2  # or HTML
```

The default settings work out of the box — these are optional tweaks.

---

## 📦 Step 5: Install Dependencies

Hermes uses the `python-telegram-bot` library to communicate with Telegram.

### Check if Already Installed

```bash
python -c "import telegram; print(telegram.__version__)"
```

### Install or Upgrade

```bash
pip install python-telegram-bot --upgrade
```

Or using `uv` (if you prefer):

```bash
uv pip install python-telegram-bot --upgrade
```

### Voice Message Support (Optional)

For automatic transcription of voice messages:

```bash
pip install faster-whisper

# Configure STT
hermes config set stt.enabled true
hermes config set stt.provider local
```

> [!TIP]
> The `faster-whisper` model downloads automatically on first use (~150 MB for the "base" model). You only need this if you want voice message support.

---

## 🚀 Step 6: Run the Gateway

**Gateway** is the background process that connects Telegram to Hermes. It listens for incoming messages, passes them to the AI agent, and delivers responses back.

### Option A: Foreground (Testing)

```bash
hermes gateway run
```

Keep this terminal window open. Press `Ctrl+C` to stop.

Expected output:
```
[12:00:00] INFO     Starting Gateway...
[12:00:01] INFO     Telegram: Connected as @my_assistant_bot
[12:00:01] INFO     Gateway is running. Press Ctrl+C to stop.
```

### Option B: System Service (Permanent)

```bash
# Install as a system service
hermes gateway install

# Start
hermes gateway start

# Check status
hermes gateway status
```

Expected output:
```
✓ Gateway is running
```

### Option C: Background Process (Fallback)

**Linux / macOS:**
```bash
nohup hermes gateway run > ~/.hermes/logs/gateway.log 2>&1 &

# To stop later:
ps aux | grep "hermes gateway"
kill [PID]
```

**Windows:** Use Task Scheduler (`taskschd.msc`) to create a task that runs `hermes gateway run` at user login.

---

## ✅ Step 7: Test Your Bot

### If Using Foreground Mode (Option A)

Keep the gateway terminal open, then on your phone or desktop:

1. Open Telegram and find your bot (`@my_assistant_bot`)
2. Send a message: `Hello!`
3. Wait 2–10 seconds for a response

### If Using Service Mode (Option B)

Simply open Telegram and message your bot.

### Expected Results

**In Telegram:**
```
You: Hello!
Bot: Hello! I'm Hermes Agent, your AI assistant. How can I help you today?
```

**In the gateway terminal:**
```
[12:05:00] INFO     Received message from @username (123456789): "Hello!"
[12:05:03] INFO     Sending response...
[12:05:04] INFO     Response sent successfully
```

> [!NOTE]
> 🎉 If you got a response — congratulations! Your Telegram bot is working correctly.

---

## 👥 Step 8: Group Chat Setup

To add your bot to groups for team collaboration:

### 1. Disable Privacy Mode (Required for Full Group Access)

In @BotFather:
```
/mybots → select your bot → Bot Settings → Group Privacy → Turn off
```

> [!INFO]
> By default, bots in groups only see messages that start with `/` or reply to their own messages. Disabling privacy mode lets the bot see **all** messages in any group.

### 2. Add Bot to a Group

Open your group → tap group name → `Add members` → enter your bot's @username.

### 3. Alternative: Make Bot an Admin

If you don't want to disable privacy globally, make the bot an **administrator** of the specific group. It will see all messages in that group only.

### 4. Group Commands

| Command | Description |
|---|---|
| `/topic on` | Enable per-user topic threads (each user gets their own session) |
| `/topic off` | Disable topics (shared group chat) |
| `/sethome` | Set this group as the delivery channel for cron job results |

---

## 🔍 Step 9: Troubleshooting

### ❌ Bot Doesn't Respond

**Check 1: Is the gateway running?**
```bash
hermes gateway status
```
Expected: `✓ Gateway is running`

**Check 2: Is the token correct?**
```bash
grep TELEGRAM_BOT_TOKEN ~/.hermes/.env
```
Ensure no extra spaces, quotes, or line breaks.

**Check 3: Is the user ID correct?**
```bash
grep TELEGRAM_ALLOWED_USERS ~/.hermes/.env
```
Must be your personal numeric ID (not the bot's ID).

**Check 4: Gateway logs**
```bash
tail -n 50 ~/.hermes/logs/gateway.log
```
Look for keywords: `Error`, `FAILED`, `403`, `ConnectionError`

**Check 5: Dependencies installed**
```bash
python -c "import telegram; print(telegram.__version__)"
```

### ❌ `403 Forbidden` Error

**Cause:** `TELEGRAM_ALLOWED_USERS` contains the bot's own ID. Telegram blocks bot-to-bot communication.

**Fix:** Get your real user ID from @userinfobot and update the `.env` file. Then restart the gateway.

### ❌ `ConnectionError` / Timeout

```bash
# Test network connectivity
ping -c 1 api.telegram.org

# Test DNS resolution
nslookup api.telegram.org
```

**Firewall:** Ensure your machine can make outbound HTTPS connections to `api.telegram.org:443`.

### ❌ Gateway Crashes Immediately

```bash
# Check recent errors
tail -n 100 ~/.hermes/logs/gateway.log

# Reinstall dependencies
pip install --upgrade python-telegram-bot httpx
```

### ❌ Voice Messages Not Working

```bash
# Install transcription engine
pip install faster-whisper

# Enable STT
hermes config set stt.enabled true
hermes config set stt.provider local

# Restart gateway
hermes gateway restart
```

### ❌ Broken Text Formatting (Emojis/Markdown)

Add this to your `.env`:
```bash
echo 'TELEGRAM_PARSE_MODE=HTML' >> ~/.hermes/.env
```

Or try `MarkdownV2` instead of `HTML` if you prefer Markdown formatting.

### ❌ Service Doesn't Start at Boot

**Windows:**
1. Open **Task Scheduler** (`taskschd.msc`)
2. Find the `Hermes Gateway` task
3. Verify:
   - Task is enabled
   - Paths to Python and Hermes are correct
   - Runs under your user account
   - Trigger is set to "At logon"

**Linux (systemd):**
```bash
# Enable user-linger (so services run without login)
loginctl enable-linger $USER

# Enable the service
systemctl --user enable hermes-gateway

# Check logs
journalctl --user -u hermes-gateway -n 30 --no-pager
```

---

## ⌨️ Step 10: Telegram Commands Reference

Once connected, all Hermes commands work through Telegram.

### Core Commands

| Command | Description |
|---|---|
| `/help` | Show list of available commands |
| `/new` | Reset conversation (clear context, start fresh) |
| `/retry` | Resend the last message to the AI |
| `/undo` | Remove the last exchange |
| `/title <name>` | Rename the current session |
| `/stop` | Kill background processes |
| `/model` | Show current AI model in use |
| `/fast` | Toggle fast response mode |

### Gateway Commands

| Command | Description |
|---|---|
| `/sethome` | Set this chat as the delivery channel for cron results |
| `/topic on/off` | Enable/disable per-user topic threads in groups |
| `/platforms` | Show connected platform status |
| `/restart` | Restart the gateway |
| `/status` | Show current session info |

### Info Commands

| Command | Description |
|---|---|
| `/usage` | Token usage statistics |
| `/profile` | Show active Hermes profile |
| `/debug` | Generate and upload a debug report |
| `/insights` | Usage analytics over time |

---

## 🧠 Advanced Tips

### Personality Customization

Set your bot's communication style via the AI persona file:

```bash
cat > ~/.hermes/SOUL.md << 'EOF'
You are a friendly, concise AI assistant.
Communicate clearly and directly.
Use emojis sparingly.
Respond in the user's language.
EOF
```

### Custom Welcome Message

In `~/.hermes/config.yaml`:

```yaml
gateway:
  telegram:
    welcome_message: "🤖 Hello! I'm your AI assistant. How can I help you today?"
```

### Cron Jobs Delivered to Telegram

```bash
# Example: receive a daily morning digest in your Telegram bot
hermes cron create "0 9 * * *" \
  --name "Daily Digest" \
  --prompt "Create a brief digest of the latest news in AI and technology"
```

Results arrive automatically in your Telegram bot chat.

### Multiple Hermes Profiles

Run different profiles for different contexts:

```bash
hermes profile create work
hermes profile use work
# This profile has its own skills, memory, and config
```

---

## ☁️ Cloud & Deployment Options (Run Without a PC)

> One of Hermes Agent's superpowers: **you don't need a PC**. Run it on a cheap cloud server and talk to it from your phone via Telegram. This section covers every option — from free to enterprise — so you can pick what fits your budget and country.

---

### 🎯 Audience Quick Guide

| Your Situation | Recommended Option | Est. Monthly Cost |
|---|---|---|
| 🧑‍💻 Solo developer, just testing | OpenRouter Free + local PC | **$0** |
| 🌍 Non-US / restricted country | OpenRouter (crypto via MetaMask) + $5 VPS | **$5–$15** |
| 💼 Professional, needs reliability | Nous Portal + Modal/Fly.io | **$10–$50** |
| 🏢 Team / Enterprise | Enterprise OpenRouter + Dedicated VPS | **$50–$500+** |
| 🎓 Student, limited budget | OpenRouter Free + free cloud tier (Fly.io, Railway) | **$0** |
| 🔒 Privacy-focused | Self-host local models + VPS with no external APIs | **$10–$30** (GPU extra) |
| 🇨🇳 China / restricted region | DeepSeek / Moonshot / MiniMax providers + local VPS | **$5–$20** |

---

### Architecture Overview

```
[Your Phone — Telegram App]
        ↕   (Internet)
[Cloud Server — Hermes Gateway]
        ↕
[LLM Provider — OpenRouter / Nous Portal / Anthropic / etc.]
```

Your Hermes gateway runs **24/7 on a cloud server**. You control it entirely from Telegram. No PC needed after initial setup.

---

### Option 1: Nous Portal (All-in-One Subscription)

**Best for:** Users who want everything bundled — models + tools + support — with one login.

**What is it:** Nous Portal is Nous Research's paid platform. A single subscription gives you:
- ✅ Access to 235+ models (via OpenRouter)
- ✅ Tool Gateway (web search, browser automation, image generation, TTS)
- ✅ Embeddings API (25 models)
- ✅ Priority support
- ✅ `hermes setup --portal` — one OAuth command configures everything

**Pricing:**
- **Tool pricing (pay-per-use):**
  - Browser automation: **$0.0011/min**
  - Web search (Firecrawl): **$0.0005/credit**
  - Image gen (various models): **$0.005–$1.05/image**
  - Modal sandbox: **$0.0495/CPU-hour** + **$0.0084/GiB-hour**
  - Audio transcription: **$0.0063/min** of audio
- Model costs vary by model (see [portal.nousresearch.com](https://portal.nousresearch.com/) → Info tab)

**How to subscribe:**
1. Go to [portal.nousresearch.com](https://portal.nousresearch.com/)
2. Sign up (OAuth)
3. Add payment method
4. Run `hermes setup --portal` to link your local Hermes

**Payment methods:** Credit/debit card

---

### Option 2: OpenRouter (Pay-as-You-Go, Crypto-Friendly)

**Best for:** Users worldwide, including countries with restricted banking — **accepts crypto**.

**What is it:** OpenRouter is the universal LLM API gateway. Hermes uses it by default. You buy credits and spend them on any model.

**Pricing:**

| Plan | Cost | Limits |
|---|---|---|
| **Free** | $0 | 25+ free models, 50 requests/day |
| **Pay-as-you-go** | 5.5% platform fee + model cost | 400+ models, unlimited requests |
| **Enterprise** | Custom pricing | Volume discounts, SLAs, SSO |

**Model cost examples (pay-as-you-go):**

| Model | Input (per 1M tokens) | Output (per 1M tokens) |
|---|---|---|
| DeepSeek V4 | ~$0.50 | ~$2.00 |
| Claude Sonnet 4 | ~$3.00 | ~$15.00 |
| Gemini 2.5 Pro | ~$1.25 | ~$5.00 |
| Llama 4 (open) | ~$0.20 | ~$0.60 |
| GPT-4o | ~$2.50 | ~$10.00 |

> Full pricing: [openrouter.ai/models](https://openrouter.ai/models) — search any model

**💳 Crypto Payment (Full Details):**

| Detail | Info |
|---|---|
| **Accepts crypto?** | ✅ Yes (Pay-as-you-go plan) |
| **How to pay with crypto** | 1. Sign up at [openrouter.ai](https://openrouter.ai) with Google, GitHub, or **MetaMask** 2. Go to Credits → Buy Credits → select Crypto 3. Pay with USDC/USDT/ETH |
| **Supported wallets** | MetaMask, WalletConnect, Coinbase Wallet, and any Web3 wallet |
| **Supported networks** | Ethereum (ERC-20), Polygon, Optimism, Arbitrum, Base |
| **Coins accepted** | USDC, USDT, ETH, DAI (and more via on-ramp) |
| **Minimum crypto purchase** | ~$10 equivalent |
| **MetaMask sign-up** | You can create an account **directly with MetaMask** — no email needed |
| **Refunds** | Unused credits are refundable on request |

**✅ Why this matters:**
- Works in countries where international credit cards are blocked
- No bank account needed — just a crypto wallet
- Privacy-friendly (no KYC for small amounts)
- Instant top-up from any exchange

**How much will you spend?** Typical light Telegram usage (20–50 messages/day) costs **$3–$15/month** with capable models like DeepSeek or Claude Sonnet.

---

### Option 3: Self-Hosted VPS ($5–$20/month)

**Best for:** Users who want full control, run their own server, or need a fixed monthly cost.

A **VPS** (Virtual Private Server) is a cloud computer that runs 24/7. You install Hermes on it once, and it's always online.

**Recommended providers (sorted by price):**

| Provider | Cheapest Plan | Crypto Payment? | Notes |
|---|---|---|---|
| **[Hetzner](https://hetzner.com)** | €3.79/mo | ✅ Yes (via invoice/SEPA) | Best value in Europe, excellent reliability |
| **[DigitalOcean](https://digitalocean.com)** | $4/mo | ❌ Credit card/PayPal | Simple, beginner-friendly UI |
| **[Vultr](https://vultr.com)** | $2.50/mo | ✅ Yes (USDC/ETH/BTC) | Cheap, many locations worldwide |
| **[Linode (Akamai)](https://linode.com)** | $5/mo | ❌ Credit card/PayPal | Good docs, stable |
| **[BuyVM](https://buyvm.net)** | $3.50/mo | ✅ Yes (BTC, Monero, USDC) | Privacy-focused, no KYC |
| **[HostHatch](https://hosthatch.com)** | $3/mo | ✅ Yes (BTC, USDC) | Good for Asia/Africa |
| **[Contabo](https://contabo.com)** | €6.99/mo | ✅ Yes (crypto via CoinGate) | Lots of RAM/CPU for the price |

**Minimal VPS specs for Hermes:**
- **CPU:** 1 vCPU
- **RAM:** 1–2 GB
- **Storage:** 10–25 GB SSD
- **OS:** Ubuntu 22.04 or 24.04 LTS

**Setup time:** ~10 minutes (install Hermes + configure gateway)

> See our separate [VPS Deployment Guide](GUIDE.md#-vps-deployment-quickstart) below.

---

### Option 4: Serverless Cloud (Modal / Daytona / Fly.io)

**Best for:** Advanced users who want to pay only when the bot is active — nearly $0 when idle.

| Platform | How It Works | Cost When Idle | Best For |
|---|---|---|---|
| **[Modal](https://modal.com)** | Serverless containers, wake on demand | $0 (sleeps when not in use) | Hermes + Telegram backend; Hermes has native Modal support |
| **[Fly.io](https://fly.io)** | Global edge deployment, auto-scaling | $0 (free tier: 3 shared VMs) | Lightweight gateway hosting |
| **[Railway](https://railway.app)** | Simple deploy from GitHub | $0 (free $5 credit/month) | Quick deployment, beginner-friendly |
| **[Daytona](https://daytona.io)** | Dev environment as a service | Varies | Hermes has native Daytona terminal support |

**Modal + Hermes specifics:**
- `terminal.backend: modal` in config.yaml
- Runs Hermes in Modal's serverless sandbox
- Auto-scales to zero when idle
- Pay only for compute seconds used
- Typical cost: < **$5/month** for personal use

**Fly.io + Telegram gateway:**
- Deploy the gateway as a Fly.io app
- Free tier handles personal bot traffic easily
- No server management

---

### Option 5: GPU Cloud (For Running Local Models)

**Best for:** Privacy-focused users who want to run open-source models instead of paying per-token APIs.

| Provider | Crypto? | Min Cost | Notes |
|---|---|---|---|
| **[RunPod](https://runpod.io)** | ✅ Yes | $0.34/hr (RTX 4090) | Serverless GPU, pay by second |
| **[Vast.ai](https://vast.ai)** | ✅ Yes (BTC) | $0.20/hr (RTX 3090) | Marketplace, cheap |
| **[TensorDock](https://tensordock.com)** | ✅ Yes | $0.25/hr | Cloud + marketplace |
| **[Massed Compute](https://massedcompute.com)** | ✅ Yes (crypto) | $0.30/hr | Consumer GPUs |

With a GPU cloud, you run models like Llama 4, Hermes 4, or DeepSeek locally on the GPU. No API costs — just the GPU rental.

---

### 🇷🇺🇨🇳🇧🇷 International Users — Special Notes

#### For Russian / CIS users:
- **OpenRouter** works with crypto (MetaMask + USDC on any network)
- **Vultr, BuyVM, HostHatch** accept crypto for VPS
- **Hetzner** accepts PayPal and SEPA from Russian banks
- Recommended models: DeepSeek, Qwen, Llama (via OpenRouter) — competitive prices
- **RunPod** and **Vast.ai** accept crypto for GPU rental

#### For China / Asia users:
- Hermes supports **Chinese providers natively**: MiniMax, Moonshot/Kimi, Alibaba DashScope, ZhipuAI (GLM), Qwen
- Configure via `hermes model` → select Chinese provider
- VPS: **Alibaba Cloud**, **Tencent Cloud**, **Huawei Cloud** — local payment methods
- No VPN needed if using Chinese providers directly

#### For Brazil / Latin America:
- OpenRouter credits via crypto (USDT on Polygon — low fees)
- VPS: **HostHatch (Brazil location)**, **DigitalOcean (São Paulo)**
- Contabo accepts crypto via CoinGate

#### For Africa / Middle East:
- **BuyVM** (Luxembourg) — crypto-friendly, no KYC
- **Vultr** (Johannesburg, Tel Aviv, Dubai locations) — accepts crypto
- OpenRouter via MetaMask — no bank card needed

---

### 💰 Cost Comparison Summary

All prices are approximate monthly for a personal Telegram bot (50–100 messages/day).

| Setup | Model Costs | Infrastructure | Tools | **Total/mo** |
|---|---|---|---|---|
| OpenRouter Free + PC | $0 | $0 (your PC) | $0 | **$0** |
| OpenRouter PAYG + $5 VPS | $3–$15 | $5 | $0–$2 | **$8–$22** |
| Nous Portal + Modal | $5–$20 | $0–$5 | Included | **$5–$25** |
| Self-hosted GPU (RunPod) | $0 (local models) | $30–$150 (GPU) | $0 | **$30–$150** |
| Enterprise VPS | $50–$200 | $20–$100 | $10–$50 | **$80–$350** |

> **💡 Tip for most users:** OpenRouter Pay-as-you-go + $5 VPS = **~$10/month**. This is the sweet spot.

---

### 🖥️ VPS Deployment Quickstart

```bash
# 1. SSH into your VPS
ssh root@your-vps-ip

# 2. Install Hermes
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash

# 3. Configure
hermes setup --portal  # Or manually configure OpenRouter key

# 4. Set up Telegram (same steps as this guide)
hermes gateway run

# 5. Keep it running 24/7 (auto-restart on reboot)
crontab -e
# Add line: @reboot sleep 30 && /root/.hermes/hermes-agent/venv/bin/hermes gateway run > /root/.hermes/logs/gateway.log 2>&1 &
```

> **Pro tip:** Use `tmux` or `screen` to keep the gateway running even if SSH disconnects:
> ```bash
> tmux new-session -d -s hermes 'hermes gateway run'
> ```

---

### 🔐 Security Best Practices for Cloud Deployment

| Practice | Why |
|---|---|
| Use a **firewall** (UFW) | Allow only SSH (22) + outbound HTTPS. No open ports needed for Telegram polling |
| **SSH keys only** (no passwords) | Prevents brute-force attacks |
| Regular updates | `apt update && apt upgrade -y` monthly |
| **Don't run as root** | Create a limited user for Hermes |
| Keep `.env` permissions strict | `chmod 600 ~/.hermes/.env` (only owner can read) |

---

### 📦 All Payment Methods at a Glance

| Method | Works With | Best For |
|---|---|---|
| 💳 Credit/Debit card (Visa, MC) | Nous Portal, OpenRouter, DigitalOcean, Linode | Most users |
| 🅿️ PayPal | OpenRouter, Hetzner, Contabo | Europe/Americas |
| ₿ Bitcoin (BTC) | BuyVM, Vast.ai, some VPS providers | Privacy-focused users |
| 💎 USDC / USDT | OpenRouter, Vultr, BuyVM, HostHatch, CoinGate merchants | **Crypto users worldwide** |
| 🔷 Ethereum (ETH) | OpenRouter (via MetaMask) | General crypto |
| 🪙 Monero (XMR) | BuyVM, some privacy VPS | Maximum privacy |
| 💼 Bank transfer / SEPA | Hetzner, some enterprise providers | European business |
| 🇨🇳 Alipay / WeChat Pay | Chinese VPS providers (Alibaba, Tencent) | China users |
| 🇧🇷 Local payment (PIX, Boleto) | Some Brazilian VPS resellers | Brazil users |

---

### 🌐 Networks & Wallet Setup for Crypto Payments

**If you're new to crypto, here's the simplest path:**

```mermaid
1. Install MetaMask (browser extension or mobile app)
    ↓
2. Buy USDC on an exchange (Binance, Bybit, OKX, Kraken)
    ↓
3. Send USDC to your MetaMask wallet on Arbitrum/Base network (low fees)
    ↓
4. Go to OpenRouter → Buy Credits → Pay with MetaMask
    ↓
5. Done! Your Hermes bot runs on crypto
```

**Recommended networks for lowest fees:**
| Network | Fee for $50 transfer | Time |
|---|---|---|
| **Arbitrum** | ~$0.10 | ~1 min |
| **Base** | ~$0.05 | ~1 min |
| **Polygon** | ~$0.02 | ~1 min |
| **Optimism** | ~$0.10 | ~1 min |
| Ethereum (ERC-20) | ~$2–$10 | ~5 min |

> 🔥 Use **Arbitrum, Base, or Polygon** — avoid Ethereum mainnet for small payments (fees eat your money).

---

## 🔗 Useful Links

| Resource | Link |
|---|---|
| 📖 Hermes Official Documentation | https://hermes-agent.nousresearch.com/docs/ |
| 🔌 Gateway Setup Guide | https://hermes-agent.nousresearch.com/docs/user-guide/messaging/ |
| 📱 Telegram Setup (Official Docs) | https://hermes-agent.nousresearch.com/docs/user-guide/messaging/telegram/ |
| 🤖 @BotFather (Create Bots) | https://t.me/BotFather |
| 🆔 @userinfobot (Get User ID) | https://t.me/userinfobot |
| 💬 Python Telegram Bot Library | https://github.com/python-telegram-bot/python-telegram-bot |
| 🏠 Hermes Agent GitHub | https://github.com/NousResearch/hermes-agent |

---

## ✅ Checklist

Use this to track your progress:

- [ ] **Step 1:** Bot created via @BotFather, token saved securely
- [ ] **Step 2:** (Optional) Description, avatar, and command menu configured
- [ ] **Step 3:** Telegram User ID obtained via @userinfobot
- [ ] **Step 4:** Token and User ID added to `~/.hermes/.env`
- [ ] **Step 5:** `python-telegram-bot` installed
- [ ] **Step 6:** Gateway running (foreground or as a service)
- [ ] **Step 7:** Bot responds in Telegram chat
- [ ] **Step 8:** (Optional) Bot added to group with proper permissions

---

## 📄 License

MIT — feel free to use, share, and adapt this guide.

---

> [!NOTE]
> Built with [Hermes Agent](https://hermes-agent.nousresearch.com/) by Nous Research.
> This guide was created on 2026-06-10 for Hermes Agent v0.16.0.
> Always check the [official documentation](https://hermes-agent.nousresearch.com/docs/) for the latest information.