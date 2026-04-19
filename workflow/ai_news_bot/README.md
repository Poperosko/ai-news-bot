# workflow/

This folder contains the n8n workflow export.

## How to export your workflow

1. Open your workflow in n8n
2. Click the **⋮ menu** (top right) → **Download**
3. Save the file as `ai_news_bot.json` in this folder
4. Commit and push to GitHub

## How to import the workflow

1. Open your n8n instance
2. Go to **Workflows → Import from file**
3. Select `ai_news_bot.json`
4. Configure credentials as described in [SETUP.md](../SETUP.md)

---

> **Note:** Before committing the workflow JSON, make sure it does not contain
> any hardcoded API keys, tokens, or personal data. n8n stores credentials
> separately — the exported JSON should only reference credential names, not values.
