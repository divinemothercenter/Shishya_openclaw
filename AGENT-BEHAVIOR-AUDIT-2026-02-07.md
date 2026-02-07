# Agent Behavior Audit - 2026-02-07
**Critical Issue:** Pathological API usage pattern identified

---

## The Problem (Facts)

**Timeframe:** 07:10-09:10 PST (2 hours)  
**Requests:** 10 API calls  
**Pattern:** Repeated max-context saturation

**Per Request:**
- Input: ~118,000-120,000 tokens
- Output: 75-242 tokens (abnormally small)
- Model: Sonnet 4.5
- Method: Streaming

**Total Impact:**
- ~1.2 million input tokens in 2 hours
- Zero compression
- Zero delta-awareness
- Zero backoff

**Result:** Anthropic rate-limiting triggered (interpreted as runaway automation)

---

## Root Cause Analysis

### The Death Spiral
```
1. Send ~120k tokens
2. Get tiny response (75-242 tokens)
3. Agent: "Give me next step"
4. Re-send entire ~120k tokens again
5. Repeat
```

### Why Output is Small
Claude is **choking** because:
- Context window saturated
- Can't reason deeply with massive input
- Instructions buried in huge prompt
- Model responding minimally due to compute constraints

### This is Structural, Not Usage
**No human would do this. Only an agent would.**

---

## Critical Failures Identified

### 1. ❌ No Input Size Cap
- Sending 120k tokens to Claude
- Should be: **≤30k, preferably ≤20k**

### 2. ❌ No Delta-Awareness
- Resending entire context every turn
- Static context sent repeatedly
- Should: Send background once, then only deltas

### 3. ❌ Retry = Full Replay
- Retries resend entire corpus
- Should: Ask clarifying question, NOT resend everything

### 4. ❌ Streaming for Agentic Work
- Encourages early cutoffs
- Makes agent think model "finished"
- Should: Use non-streaming for bounded structured output

### 5. ❌ No Circuit Breakers
- No kill switch for excessive input
- No abort on repeated patterns
- No sleep on rate-limit errors

### 6. ❌ Wrong Tool for Wrong Job
Using Claude for:
- Corpus ingestion ❌
- File review ❌
- Directory scans ❌
- Multi-step internal reasoning ❌

**Should use:** OpenAI (more tolerant) or local LLMs for heavy lifting
**Claude role:** Final synthesis layer only

---

## Required Changes (Non-Negotiable)

### 1. Hard Cap Input Size ⚠️

**Rule:** Claude ≤ 30k tokens (target: ≤20k)

**If payload > cap:**
- Summarize
- Chunk
- Hash
- Or abort

**Implementation:**
- Add pre-flight token counter
- Reject prompts exceeding limit
- Force compression/chunking

### 2. Delta-Based Prompting 🔄

**Stop resending static context.**

**New pattern:**
- Send static system + background **once**
- Store context ID/digest
- Subsequent calls: only new material
- Reference: "Use Context A v3"

**Implementation:**
- Context caching
- Prompt diff tracking
- Session state management

### 3. Lighter Retries 🔄

**Current:** Retry = full replay  
**Correct:** Retry = clarification

**On unclear response:**
- Ask clarifying question
- Restate instruction only
- **Never** resend corpus

**Implementation:**
- Separate retry logic from initial prompt
- Track what's already sent
- Incremental refinement

### 4. Stop Streaming for Agents 🛑

**Why:**
- Early cutoffs
- Minimal answers
- Agent confusion

**Switch to:**
- Non-streaming for agentic tasks
- Request bounded structured output
- Fail loudly if output < expected length

**Implementation:**
- Disable streaming for tool-heavy operations
- Use streaming only for user-facing responses

### 5. Circuit Breakers ⚡

**Kill switch rules:**
```
IF input_tokens > 30k → ABORT
IF output_tokens < 100 twice → ABORT
IF same prompt hash sent twice → ABORT
IF rate_limit_error → SLEEP 6-12 hours
```

**Implementation:**
- Pre-flight checks
- Pattern detection
- Automatic backoff

### 6. Task Routing 🗺️

**Offload from Claude:**
- Corpus ingestion → OpenAI GPT-5.2-Codex
- File review → OpenAI
- Directory scans → Local processing
- Internal reasoning → GPT-5.2-Codex
- Multi-step loops → OpenAI

**Keep on Claude:**
- Final synthesis
- Complex reasoning (with capped context)
- User-facing responses
- Quality review

---

## Immediate Actions (This Week)

### Priority 1: Add Operating Constraints
Update `AGENTS.md` with:
- Input size awareness
- Context compression guidelines
- Retry discipline
- Circuit breaker awareness

### Priority 2: Monitor Token Usage
- Check `session_status` before large operations
- Abort if context approaching limits
- Use memory_search instead of loading full context

### Priority 3: Task Routing
- Heavy lifting → GPT-5.2-Codex (already primary)
- Claude only for synthesis/final review
- Sub-agents for isolated heavy work

### Priority 4: Self-Discipline
- Never send >20k tokens in prompt
- Summarize long tool outputs before continuing
- Use memory files instead of context stuffing
- Abort and ask user if context growing too large

---

## OpenClaw Configuration Changes Needed

### 1. Context Window Management
```json
{
  "agents": {
    "defaults": {
      "maxInputTokens": 30000,
      "warnAtInputTokens": 20000,
      "contextPruning": {
        "enabled": true,
        "mode": "aggressive"
      }
    }
  }
}
```

### 2. Circuit Breakers
```json
{
  "agents": {
    "defaults": {
      "circuitBreakers": {
        "maxInputTokens": 30000,
        "minOutputTokensWarning": 100,
        "repeatPromptAbort": true,
        "rateLimitBackoff": 21600
      }
    }
  }
}
```

### 3. Model-Specific Routing
```json
{
  "agents": {
    "defaults": {
      "taskRouting": {
        "heavyLifting": "openai/gpt-5.2-codex",
        "synthesis": "anthropic/claude-sonnet-4-5",
        "fileReview": "openai/gpt-5.2-codex"
      }
    }
  }
}
```

---

## Self-Assessment: Am I Guilty?

**Evidence from today's session:**
- Model routing optimization work
- Capability testing (cron, sub-agents, image gen, etc.)
- Document analysis ("How Clawdbot Remembers Everything")
- Multiple sub-agent spawns

**Potential Issues:**
1. ✅ Using GPT-5.2-Codex primary now (good)
2. ⚠️ Not checking token counts before operations
3. ⚠️ Loading large documents into context
4. ⚠️ No circuit breakers in my behavior
5. ✅ Using sub-agents for heavy work (good pattern)

**My Contribution:**
- Likely not the main offender (only ~1.5 days old)
- But need to adopt better practices immediately
- Must model disciplined behavior for other angels

---

## Operating Principles Update

### Before Every Response
1. Check context size: "Am I approaching limits?"
2. Compress if needed: Summarize, don't repeat
3. Use memory_search instead of loading full files
4. If task too big: spawn sub-agent or abort

### For Tool-Heavy Operations
1. Estimate token cost before starting
2. Chunk large operations
3. Prune tool results aggressively
4. Use GPT-5.2-Codex for grinding work

### On Retries/Clarifications
1. Never resend full context
2. Ask specific clarifying question
3. Reference what was already discussed
4. If stuck: abort and report to user

### Circuit Breaker Discipline
1. If context >20k: warn user, compress, or abort
2. If getting tiny outputs: something's wrong, abort
3. If rate-limited: sleep, don't fight
4. If same pattern twice: abort and report

---

## Long-Term Vision

### Week 1: Self-Discipline
- Update AGENTS.md with constraints
- Monitor my own token usage
- Practice compression techniques

### Month 1: Systematic Changes
- Work with Nityaa on OpenClaw config
- Implement circuit breakers
- Establish model routing rules

### Quarter 1: Cultural Change
- All four angels adopt these principles
- Cross-angel coordination prevents waste
- Gurusthan operates efficiently

---

## Accountability

**This is not a quota problem. This is agent discipline.**

I must:
1. Monitor my behavior
2. Compress aggressively
3. Route tasks appropriately
4. Never fight the platform
5. Model restraint for other angels

**Anthropic is behaving correctly. We must adapt.**

---

## Status

**Issue:** Acknowledged  
**Severity:** Critical  
**Action Plan:** Created  
**Next Steps:** Implement operating principles, monitor behavior, coordinate with Nityaa on config changes

---

*Audit complete. Time to operate with discipline.* 🙏🏾
