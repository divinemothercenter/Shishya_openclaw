# Raksha Setup Assessment (Refined)

**Date:** 2026-02-07  
**Thread:** Topic 7 - Divine Network HQ  
**Host:** Garuda  
**Context:** Clarifying relevant vs. irrelevant items from generic CloudBot tutorial

---

## Truth

That doc is a generic "CloudBot" onboarding checklist. Our environment is already a **local Clawdbot workspace** with SOUL/IDENTITY/USER/MEMORY/HEARTBEAT present.

So the "initial install" items to focus on are **config, routing, integrations, and safety** — not VPS deployment.

---

## Assessment (What's Relevant vs. Not)

### ✅ Relevant

- Model routing
- Skills/integrations
- Cron/heartbeats
- Security audit
- `.env` hygiene
- Telegram setup
- Daily maintenance

### ❌ Not Relevant Right Now

- Hostinger VPS
- One-click install (we're already running locally)

---

## Next Steps (Practical Order)

### 1. Confirm Runtime Health

**Run:** `clawdbot status` (I can do this for you)

### 2. Verify Config + Model Routing

- Review current model chain + defaults
- Adjust if needed
- *(If you want, tell me your preferred primary/fallback models and I'll patch config.)*

### 3. Security Baseline

- Ensure `.env` exists and is in `.gitignore`
- Run the security audit: `openclaw security audit --fix` (if available here)
- *(I'll ask before running external changes.)*

### 4. Integrations (Pick Only What You Want Now)

Options:
- Gmail
- Calendar
- Drive
- Slack
- Asana
- Telegram

*I can set up each when you say "yes, connect X."*

### 5. Workflow Structure

- Decide if you want a Telegram group with topics or keep webchat
- Define 2–4 primary threads (e.g., Research, Ops, Content)

### 6. Automation

- Define 1–2 cron jobs (e.g., morning summary, weekly review)
- Confirm heartbeat scope (it's already defined in `HEARTBEAT.md`)

### 7. Personality + Memory Pass

- Quick review of SOUL/IDENTITY for any tweaks you want

---

**Source:** Telegram Divine Network HQ, Topic 7, Message 96  
**Author:** 🙏🏾 🪔 (Nityaa)  
**Agent:** Garuda (Raksha)
