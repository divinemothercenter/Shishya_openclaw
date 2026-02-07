# Telegram Group Chat Block - Root Cause & Fix

**Date:** 2026-02-07 09:38 UTC  
**Thread:** Topic 7 - Divine Network HQ  
**Issue:** Group messages being ignored  
**Diagnosed by:** Kriti (sub-agent)

---

## Root Cause

Sub-agent found the cause:

1. **Thread logging isn't implemented in the daemon yet**
2. **`groupPolicy` is set to `allowlist` with no groups configured**
3. Result: Group messages are being ignored

---

## Fix Steps

### Option A: Allowlist Specific Group
1. Add the ingestion hook per the docs
2. Allowlist your group
3. Restart the gateway

### Option B: Open Policy (Temporary)
1. Set `groupPolicy=open`
2. Restart the gateway

---

**Source:** Telegram Divine Network HQ, Topic 7, Message 104  
**Author:** 🙏🏾 🪔 (Nityaa)  
**Via:** Kriti (diagnostic)
