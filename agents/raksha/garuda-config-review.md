# Garuda: Live Config Review

**Date:** 2026-02-07 09:37 UTC  
**Thread:** Topic 7 - Divine Network HQ  
**Host:** Garuda (Raksha)

---

## Truth

I pulled the live config. The local `clawdbot` CLI isn't in PATH here, so `clawdbot status` failed ("command not found"). I can't show status until we know the correct binary path. Put it that way.

---

## Config Review (Current)

### Model Routing
- **Primary:** `openai-codex/gpt-5.2-codex`
- **Fallbacks:** None set

### Workspace
- **Path:** `/Users/serveradmin/clawd`

### Heartbeat
- **Interval:** Every 30m (per config)

### Channels
- **Telegram:** Enabled
  - DM allowlist: `8331898203`
  - `groupPolicy`: open
- **iMessage:** Enabled
  - `dmPolicy`: pairing

### Gateway
- **Mode:** local
- **Bind:** LAN
- **Port:** 18789
- **Auth:** token
- **Tailscale:** off

### Web Tools
- **Search:** enabled
- **Fetch:** enabled

### Hooks
- **Status:** enabled at `/hooks`
- **Auth:** env token

### Skills
Configured:
- `nano-banana-pro`
- `openai-image-gen`
- `whisper-api`
- `sag`

---

## Next Steps (Pick What You Want)

### 1. Status Command Path
Tell me where `clawdbot` lives, or run:
```bash
which clawdbot
```
and paste output. I'll use that.

### 2. Model Fallbacks
Do you want a fallback chain?  
Example: `gpt-5.2 → gemini-3-flash → opus`

### 3. Tailscale
- Keep off, or
- Enable for remote gateway access?

### 4. Channel Policy
- Keep Telegram `groupPolicy` open, or
- Tighten?

---

**Source:** Telegram Divine Network HQ, Topic 7, Message 102  
**Author:** 🙏🏾 🪔 (Nityaa)  
**Agent:** Garuda (Raksha)
