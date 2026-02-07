# Raksha 🦉 — Initial Install & Setup Review (Plan)

**Date:** 2026-02-07  
**Thread:** Topic 7 - Divine Network HQ  
**Host:** Garuda

---

## ✅ What's Already Done
*(from our current environment)*

- **SOUL.md / IDENTITY.md / USER.md / MEMORY system** — already exist and are active
- **Heartbeat system** — live and working (cron + heartbeat tasks)
- **Telegram integration** — working (daily news + direct messages)
- **Local workspace** — established at `/Users/dmc-mbp/clawd`
- **Skills folder** — already populated with many standard tools
- **Security posture** — already includes "ask before external actions"

---

## 🔎 Gaps & Priorities
*(from the CloudBot checklist)*

### 1) Model Routing & Defaults
- **Status:** Not explicitly configured here
- **Next step:** Review routing settings and decide on primary + fallback (Sonnet / Gemini / Opus etc.) once needed

### 2) API Keys (.env)
- **Status:** We use `.env.gateway`, but not fully verified
- **Next step:** Confirm Anthropic/OpenAI/Gemini keys are set there (and no keys stored elsewhere)

### 3) Google Drive OAuth + Write Access
- **Status:** Not yet configured
- **Next step:** Run Drive OAuth setup tomorrow (as Nityaa requested) with write scopes

### 4) Task Queue (Beads CLI)
- **Status:** `bd` command missing on this host
- **Next step:** Install or symlink Beads CLI so heartbeat task queue can run

### 5) Agent Config Gaps
- **Status:** `/shared/read-sibling-memory.sh raksha` fails (unknown agent)
- **Next step:** Fix agent registry/config so sibling awareness checks work properly

### 6) SMB Mount Reliability
- **Status:** Intermittently failing; Garuda reports mounts down
- **Next step:** Remount + make persistent (check launchctl or login items)

### 7) Daily Security Audit (OpenClaw)
- **Status:** Not currently running
- **Next step:** Decide if we want a daily cron audit; low priority unless Nityaa wants it

---

## 📌 Immediate Next Actions
*(ordered)*

1. Confirm model routing preference (primary + fallback)
2. Fix Beads CLI (`bd`) on this host
3. Fix agent registry so sibling memory checks pass
4. Stabilize SMB mounts (persistent remount)
5. Enable Google Drive OAuth (write scope) tomorrow

---

**Source:** Telegram Divine Network HQ, Topic 7, Message 89  
**Author:** 🙏🏾 🪔 (Nityaa)
