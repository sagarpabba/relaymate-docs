# 🔌 Admin & Multi-Channel Integrations Guide

RelayMate includes a concurrent multi-channel dispatch engine. Messages captured on the Sender device can be broadcast simultaneously to multiple destinations.

---

## 🌐 Channels Overview

All channels run fully asynchronously on a cached background thread pool. If one service experiences high latency or rate limits, other channels continue undisrupted.

---

## 1. 🤖 Telegram Bot API

Forward messages directly to your private Telegram chat, family group, or private channel.

### Setup Instructions:
1. Open Telegram and search for `@BotFather`.
2. Send `/newbot` and follow the prompts to create your bot.
3. Copy the **HTTP API Bot Token** (e.g. `7123456789:ABCdefGHIjklMNOpqrsTUVwxyz`).
4. Find your Telegram **Chat ID**:
   * Message `@userinfobot` or `@getmyid_bot` in Telegram to get your numeric ID (e.g. `987654321`).
   * For groups: Add the bot to your group and use the group ID (starts with `-`).
5. In RelayMate $\rightarrow$ Tap **✈️ Telegram** $\rightarrow$ Paste your **Bot Token** and **Chat ID**.
6. Tap **"Test Telegram"** to verify delivery.

---

## 2. 💬 Discord Webhooks

Forward messages as formatted rich embeds into a Discord server channel.

### Setup Instructions:
1. Open Discord on desktop or web $\rightarrow$ Go to your **Server Settings** $\rightarrow$ **Integrations** $\rightarrow$ **Webhooks**.
2. Click **New Webhook** $\rightarrow$ Select the channel (e.g. `#otp-alerts`).
3. Click **Copy Webhook URL**.
4. In RelayMate $\rightarrow$ Tap **🌐 Channels (Discord, Slack, Teams, Webhook)**.
5. Paste into **Discord Webhook URL** $\rightarrow$ Toggle switch ON $\rightarrow$ Tap **Test Discord**.

### Embed Payload Structure:
```json
{
  "embeds": [
    {
      "title": "[SMS from HDFC Bank]",
      "description": "Your account 1234 has been debited...",
      "color": 3447003,
      "footer": { "text": "RelayMate • 13 Nov, 16:30" }
    }
  ]
}
```

---

## 3. 💼 Slack Webhooks

Broadcast alerts into a designated Slack channel.

### Setup Instructions:
1. Visit [api.slack.com/apps](https://api.slack.com/apps) $\rightarrow$ Create an App $\rightarrow$ From Scratch.
2. Under Features, select **Incoming Webhooks** $\rightarrow$ Toggle **Activate Incoming Webhooks** ON.
3. Click **Add New Webhook to Workspace** $\rightarrow$ Pick a channel $\rightarrow$ Copy Webhook URL.
4. In RelayMate $\rightarrow$ Connected Channels $\rightarrow$ Paste into **Slack Webhook URL** $\rightarrow$ Tap **Test Slack**.

---

## 4. 🏢 Microsoft Teams Webhooks

Relay messages into Microsoft Teams channels.

### Setup Instructions:
1. In Teams, go to your channel $\rightarrow$ Click `...` $\rightarrow$ **Connectors** (or **Workflows**).
2. Add **Incoming Webhook** $\rightarrow$ Give it a name (e.g. `RelayMate`) $\rightarrow$ Copy URL.
3. In RelayMate $\rightarrow$ Connected Channels $\rightarrow$ Paste into **Teams Incoming Webhook URL** $\rightarrow$ Tap **Test Teams**.

---

## 5. 🛠️ Generic Custom REST Webhooks

Integrate with automation platforms (Zapier, Make.com, n8n) or your own internal API.

### Setup Instructions:
1. Enter your custom HTTP or HTTPS endpoint URL.
2. Toggle **Custom Webhook** ON $\rightarrow$ Tap **Save All**.

### Standard JSON Payload:
```json
{
  "from": "HDFC-BANK",
  "body": "Your OTP is 849201 for transaction of INR 1,200.00.",
  "type": "RCS/SMS",
  "otp": "849201",
  "timestamp": 1731502800000
}
```

---

## 6. 📧 SMTP Email Backup

Forward messages to any email inbox over encrypted TLS/SSL.

### Supported Presets:
* **Gmail**: `smtp.gmail.com` (Port 587, TLS) — *Requires an App Password if 2FA is enabled*.
* **Outlook / Hotmail**: `smtp-mail.outlook.com` (Port 587, STARTTLS).
* **Yahoo Mail**: `smtp.mail.yahoo.com` (Port 465 or 587).
* **Custom SMTP Host**: Enter custom host, port, username, and password.

---

## 7. 🛡️ Spam Defense & Keyword Blacklists

Configure custom filter rules to eliminate unwanted promotional SMS:

1. On the Sender phone dashboard, locate **SMART RULES & SECURITY**.
2. **"Forward OTPs Only" Mode**:
   * Drops marketing, promotional, and telemarketing text messages.
   * Only forwards messages containing a detected OTP, PIN, or authentication code.
   * *System, low-battery, and missed call alerts bypass this filter so urgent notices are never missed.*
3. **Keyword Blacklist**:
   * Messages containing words like `loan`, `casino`, `rummy`, `discount`, `credit card offer`, or `flat 50%` are automatically dropped before dispatch.

---

## 8. 💓 Automated Health Monitoring & Alerts

* **Low Battery Alert ($\le 15\%$)**:
  * `BatteryStatusReceiver.kt` continuously monitors the sender device.
  * When the battery drops to 15% or lower, it sends an emergency push:
    `⚠️ Low Battery Warning: Sender device is at 14%. Please connect it to a charger.`
  * Rate-limited to once every 4 hours to prevent spam.
* **Daily 24-Hour Heartbeat Ping**:
  * `ForwarderWatchdogService.kt` transmits a daily status check confirming the sender device is online and operational with its battery percentage.

