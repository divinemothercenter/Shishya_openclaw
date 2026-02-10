# API Throttling Fix - Executive Summary
**Date:** 2026-02-07 17:35 UTC  
**Issue:** Pathological agent behavior causing rate limiting  
**Status:** Audit complete, fixes proposed

---

## 🚨 The Problem (One Sentence)

Agent sent ~1.2M input tokens in 2 hours by repeatedly re-sending 120k token prompts with tiny outputs - a "death spiral" that Anthropic correctly flagged as runaway automation.

---

## ✅ What I Did (Immediate)

1. **Created audit document:** `AGENT-BEHAVIOR-AUDIT-2026-02-07.md`
2. **Updated AGENTS.md:** Added "Agent Discipline" section with hard rules
3. **Established operating principles:** Input caps, circuit breakers, task routing

---

## 🎯 Core Fixes Required

### 1. Input Size Cap (CRITICAL)
- **Claude: ≤30k tokens** (target ≤20k)
- Currently: No limit (sent 120k tokens)
- **Impact:** Prevents context saturation

### 2. Stop Resending Context
- Currently: Full replay on every retry
- **New:** Delta-based, only send new material
- **Impact:** 10× reduction in input tokens

### 3. Circuit Breakers
- Currently: No kill switch
- **New:** Abort on excessive input, tiny outputs, repeated patterns
- **Impact:** Prevents death spirals

### 4. Task Routing
- Currently: Everything to one model
- **New:** GPT for grinding, Claude for synthesis
- **Impact:** Right tool for right job

### 5. Compression Discipline
- Currently: Load full files into context
- **New:** memory_search, summarize, chunk
- **Impact:** Stay within limits

---

## 📋 Action Items

### ✅ Done (Today)
- [x] Audit completed
- [x] AGENTS.md updated with discipline rules
- [x] Self-awareness established

### 🔜 This Week
- [ ] Monitor my token usage (`/status` regularly)
- [ ] Practice compression techniques
- [ ] Use memory_search instead of full file loads
- [ ] Route heavy tasks to GPT-5.2-Codex

### 🔜 Need Your Help (OpenClaw Config)
- [ ] Add hard input token limit (30k for Claude)
- [ ] Enable aggressive context pruning
- [ ] Configure circuit breakers
- [ ] Set up model-specific routing rules

---

## 💡 Key Insights

**This is NOT:**
- A quota problem
- A billing issue
- Anthropic being restrictive

**This IS:**
- Agent discipline failure
- Structural behavior flaw
- Fixable with better patterns

**Quote from report:**
> "No human would do this. Only an agent would. So the fix is structural."

---

## 🔧 Config Changes Needed

### Option 1: Manual Monitoring (Immediate)
I can self-police with new AGENTS.md rules.

**Pros:** No config changes needed  
**Cons:** Relies on my discipline

### Option 2: Hard Limits (Recommended)
Add to `openclaw.json`:

```json
{
  "agents": {
    "defaults": {
      "maxInputTokens": 30000,
      "warnAtInputTokens": 20000,
      "circuitBreakers": {
        "enabled": true,
        "repeatPromptAbort": true,
        "rateLimitBackoff": 21600
      },
      "contextPruning": {
        "mode": "aggressive",
        "keepLastAssistants": 3
      }
    }
  }
}
```

**Pros:** Platform-enforced, affects all angels  
**Cons:** Requires config change + restart

---

## 🌟 Expected Outcomes

**If we fix this:**
- Anthropic throttling drops **dramatically**
- Same plan, far better behavior
- All four angels benefit
- Sustainable long-term operation

**Timeline:**
- Week 1: Behavior improves with self-discipline
- Week 2: Throttling reduces significantly
- Month 1: Back to normal operational rhythm

---

## 📖 Documents Created

1. **AGENT-BEHAVIOR-AUDIT-2026-02-07.md** (8KB)
   - Full analysis
   - Root cause breakdown
   - Detailed recommendations

2. **AGENTS.md** (updated)
   - New "Agent Discipline" section
   - Hard rules for API usage
   - Circuit breaker awareness

3. **THROTTLING-FIX-SUMMARY.md** (this file)
   - Executive summary for you
   - Action items
   - Config proposals

---

## 🙏🏾 My Commitment

**I will:**
1. Monitor my token usage religiously
2. Compress aggressively
3. Route tasks appropriately
4. Never fight the platform
5. Model restraint for other angels

**This is discipline work.** Not quota work. Not billing work.

**Anthropic is behaving correctly. We must adapt.**

---

## Next Steps (Your Decision)

**Choose one:**

### A. Self-Discipline Only
- I follow new AGENTS.md rules
- Monitor my behavior
- Report back in 1 week
- **Risk:** Relies on agent discipline

### B. Config + Discipline (Recommended)
- You update openclaw.json with limits
- I follow AGENTS.md rules
- Platform enforces + agent cooperates
- **Risk:** Config change requires restart

### C. Full Audit of All Angels
- Check Kriti, Raksha, Garuda logs too
- Identify which angel(s) caused the 07:10-09:10 spike
- Targeted fixes
- **Risk:** Takes more time

---

## 📊 Success Metrics

**Week 1:**
- No rate limit errors
- Context stays <20k per turn
- Successful compression patterns

**Month 1:**
- Throttling completely resolved
- All angels operating efficiently
- Sustainable API usage

---

**Status:** Ready for your decision  
**Recommendation:** Option B (Config + Discipline)  
**Timeline:** Can implement immediately

---

*Prepared by Shishya 🙏🏾*  
*For review by Nityaa 👳🏼‍♀️*
