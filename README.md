# 🤖 AI News Bot — Automated Telegram News Channel

Fully automated news bot for Telegram, built on **n8n**. Collects news across three topics — **AI, IT, and Crypto** — filters, summarizes, and publishes them to a channel with AI-generated images.

![n8n](https://img.shields.io/badge/n8n-self--hosted-orange?logo=n8n)
![Telegram](https://img.shields.io/badge/Telegram-Bot-blue?logo=telegram)
![Google Sheets](https://img.shields.io/badge/Google_Sheets-API-green?logo=googlesheets)
![AI](https://img.shields.io/badge/LLM-Polza_AI-purple)

---

## ✨ Features

- **3 parallel topic branches** — AI, IT, and Crypto run independently
- **LLM scoring** — each article rated 1–10; only scores above 5 are published
- **Smart deduplication** — Google Sheets tracks published links to avoid reposts
- **Russian summaries** — LLM translates headlines and writes 4–5 sentence summaries
- **AI image generation** — every 3rd post gets an AI-generated image; falls back to RSS enclosure or text-only
- **Scheduled publishing** — runs on a cron schedule via n8n Schedule Trigger

---

## 🏗️ Architecture

```
Schedule Trigger
       │
  ┌────┼────┐
  │    │    │
 AI   IT  Crypto
  │    │    │
  └────┴────┘
       │
  RSS Feed Read
       │
  LLM Evaluation
  (score + summary + image prompt)
       │
  Filter (score > 5)
       │
  Google Sheets (deduplication)
       │
  Best article selection
       │
  ┌────┴────┐
  │         │
Every 3rd  Others
  │         │
Image gen  RSS image / no image
  │         │
  └────┬────┘
       │
  Telegram Publish
       │
  Save to Google Sheets
```

---

## 🛠️ Tech Stack

| Component | Technology |
|-----------|-----------|
| Workflow engine | n8n (self-hosted) |
| LLM + Image generation | Polza AI API |
| Published links storage | Google Sheets (Service Account) |
| Publishing channel | Telegram Bot API |
| Scheduling | n8n Schedule Trigger |

---

## 📋 Pipeline Details

### 1. Data Collection
- **RSS Read** node fetches articles from topic-specific feeds
- Each article is sent to the LLM for evaluation

### 2. LLM Processing
Each article receives:
- **Relevance score** (1–10)
- **Russian headline translation**
- **4–5 sentence summary** in Russian
- **Image generation prompt**

### 3. Filtering & Deduplication
- Articles scoring below 5 are discarded
- Google Sheets is queried via Service Account API for already-published URLs
- The highest-scoring new article is selected

### 4. Image Generation
- Every 3rd publication per category gets an AI-generated image
- Counter is tracked per category in Google Sheets
- Fallback chain: AI image → RSS enclosure → text-only post

### 5. Publishing
- `IF` node routes to `Send Photo` or `Send Message` based on image availability
- After publishing, the URL and category are written to Google Sheets

---

## 🚀 Quick Start

See [SETUP.md](SETUP.md) for full installation and configuration guide.

**Requirements:**
- n8n instance (self-hosted or cloud)
- Polza AI API key
- Google Cloud Service Account with Sheets access
- Telegram Bot Token and Channel ID

---

## 📁 Repository Structure

```
ai-news-bot/
├── README.md           # This file
├── SETUP.md            # Installation & configuration guide
├── .env.example        # Environment variables template
├── .gitignore          # Git ignore rules
├── LICENSE             # MIT License
└── workflow/
    └── ai_news_bot.json  # n8n workflow export (import this into n8n)
```

---

## 📄 License

MIT — see [LICENSE](LICENSE) for details.
