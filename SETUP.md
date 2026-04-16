# 🔧 Setup Guide

Step-by-step instructions for deploying the AI News Bot.

---

## Prerequisites

- n8n instance (self-hosted via Docker, or n8n Cloud)
- [Polza AI](https://polza.ai) account with API access
- Google Cloud project with Sheets API enabled
- Telegram Bot (created via [@BotFather](https://t.me/BotFather))
- Telegram Channel where the bot is an admin

---

## Step 1 — Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/ai-news-bot.git
cd ai-news-bot
```

---

## Step 2 — Set Up Google Sheets

1. Create a new Google Sheet with two sheets (tabs):
   - `published` — for tracking published articles
   - `counters` — for tracking image generation counters per category

2. Add headers to the `published` sheet:
   ```
   A1: url    B1: category    C1: published_at
   ```

3. Add headers to the `counters` sheet:
   ```
   A1: category    B1: count
   ```
   Then add rows:
   ```
   AI     0
   IT     0
   Crypto 0
   ```

4. Note your **Spreadsheet ID** from the URL:
   `https://docs.google.com/spreadsheets/d/SPREADSHEET_ID/edit`

---

## Step 3 — Set Up Google Service Account

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or use an existing one)
3. Enable **Google Sheets API**
4. Go to **IAM & Admin → Service Accounts** → Create Service Account
5. Download the JSON key file
6. Share your Google Sheet with the service account email (Editor access)

---

## Step 4 — Create a Telegram Bot

1. Open [@BotFather](https://t.me/BotFather) in Telegram
2. Send `/newbot` and follow the instructions
3. Save the **Bot Token**
4. Add the bot to your Telegram channel as an **Administrator**
5. Get the **Channel ID** (e.g. `@your_channel` or numeric `-1001234567890`)

---

## Step 5 — Get Polza AI API Key

1. Register at [polza.ai](https://polza.ai)
2. Navigate to API settings
3. Generate and copy your API key

---

## Step 6 — Configure Environment Variables

Copy the example file and fill in your values:

```bash
cp .env.example .env
```

Edit `.env` with your credentials (see [.env.example](.env.example) for all variables).

---

## Step 7 — Import the n8n Workflow

1. Open your n8n instance
2. Go to **Workflows → Import from file**
3. Select `workflow/ai_news_bot.json`
4. The workflow will be imported with all nodes

---

## Step 8 — Configure Credentials in n8n

After importing, configure each credential in n8n:

### Polza AI (HTTP Request nodes)
- In the LLM and Image Generation nodes, set the **Authorization** header:
  `Bearer YOUR_POLZA_AI_API_KEY`

### Google Sheets (Service Account)
1. Go to **Settings → Credentials → Add Credential**
2. Choose **Google Sheets API**
3. Select **Service Account** authentication
4. Paste the contents of your downloaded JSON key file
5. Update all Google Sheets nodes to use this credential

### Telegram
1. Go to **Settings → Credentials → Add Credential**
2. Choose **Telegram**
3. Enter your **Bot Token**
4. In the Telegram nodes, update the **Chat ID** to your channel ID

---

## Step 9 — Configure RSS Feeds

In the workflow, update the **RSS Read** nodes with your preferred feeds:

**AI news feeds (examples):**
- `https://feeds.feedburner.com/oreilly/radar`
- `https://rss.arxiv.org/rss/cs.AI`

**IT news feeds (examples):**
- `https://feeds.feedburner.com/TechCrunch`
- `https://hnrss.org/frontpage`

**Crypto news feeds (examples):**
- `https://cointelegraph.com/rss`
- `https://decrypt.co/feed`

---

## Step 10 — Set the Schedule

1. Click the **Schedule Trigger** node
2. Set your preferred publishing interval (e.g., every 2 hours)
3. Adjust per-topic schedules if needed

---

## Step 11 — Activate the Workflow

1. Toggle the workflow to **Active**
2. Monitor the first few runs in **Executions** tab
3. Check your Telegram channel for published posts

---

## Troubleshooting

**Articles not publishing:**
- Check that RSS feeds are accessible from your n8n server
- Verify that the LLM API key is valid and has quota
- Ensure the Google Sheets service account has Editor access

**Duplicate posts appearing:**
- Verify the Google Sheets `published` tab has the correct headers
- Check that the deduplication JavaScript node is running without errors

**Images not generating:**
- Confirm your Polza AI account has image generation enabled
- Check the image generation counter in the `counters` tab

**Telegram errors:**
- Ensure the bot is an Administrator in the channel
- Verify the Chat ID format (channels use `-100` prefix for numeric IDs)
