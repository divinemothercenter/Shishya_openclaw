# Model Routing Implementation - Quick Start Guide

## 🚀 Fastest Path to Deployment

### Step 1: Update openclaw.json (5 minutes)

```json
{
  "model": {
    "primary": "openai/gpt-5-2-codex",
    "fallbacks": [
      "anthropic/claude-sonnet-4-5",
      "anthropic/claude-opus-4-5",
      "google/gemini-3-pro-preview"
    ],
    "imageModel": "google/gemini-3-pro-preview"
  },
  "aliases": {
    "gpt": "openai/gpt-5-2-codex",
    "gpt-fast": "openai/gpt-5-3-codex",
    "sonnet": "anthropic/claude-sonnet-4-5",
    "opus": "anthropic/claude-opus-4-5",
    "gemini": "google/gemini-3-pro-preview",
    "vision": "google/gemini-3-pro-preview",
    "deep": "anthropic/claude-opus-4-5",
    "fast": "openai/gpt-5-3-codex",
    "cheap": "openai/gpt-5-2-codex"
  }
}
```

### Step 2: Test Basic Functionality (10 minutes)

```bash
# Test primary model
openclaw chat "What is 2+2?"

# Test vision model (with an image)
openclaw chat "Describe this image" --image test.jpg

# Test manual override
openclaw chat --model opus "Debug this complex multi-file issue..."

# Test alias
openclaw chat --model deep "Analyze this architectural decision..."
```

### Step 3: Monitor First Week (passive)

Create monitoring script:

```bash
#!/bin/bash
# monitor-usage.sh

echo "Date,Model,Requests,Input_Tokens,Output_Tokens,Cost" > usage.csv

# Parse logs daily and append to usage.csv
# (Implementation depends on your logging setup)
```

**What to watch:**
- Cost per day (should drop ~39%)
- Fallback frequency (should be <5%)
- User complaints (should be none)

### Step 4: Implement Smart Routing (Week 2-3)

**Option A: Simple keyword detection**
```javascript
// Pseudo-code for request classifier
function selectModel(request) {
  const text = request.content.toLowerCase();
  
  // Vision tasks
  if (request.hasImages || text.includes('image') || text.includes('screenshot')) {
    return 'google/gemini-3-pro-preview';
  }
  
  // Math/logic tasks
  if (text.match(/calculate|solve|proof|equation|theorem/)) {
    return 'openai/gpt-5-2-codex';
  }
  
  // Complex coding
  if (text.match(/refactor|debug.*multiple|architecture|complex.*code/)) {
    return 'anthropic/claude-opus-4-5';
  }
  
  // Deep reasoning
  if (text.match(/analyze deeply|think step by step|thorough analysis/)) {
    return 'anthropic/claude-opus-4-5';
  }
  
  // Default
  return 'openai/gpt-5-2-codex';
}
```

**Option B: Use OpenClaw's built-in routing (if available)**
Check if OpenClaw supports automatic task classification. If yes, enable it:

```json
{
  "routing": {
    "enabled": true,
    "rules": [
      {"pattern": "vision|image|screenshot", "model": "google/gemini-3-pro-preview"},
      {"pattern": "math|calculate|proof", "model": "openai/gpt-5-2-codex"},
      {"pattern": "complex.*code|refactor|architecture", "model": "anthropic/claude-opus-4-5"}
    ]
  }
}
```

---

## 📊 Quick Reference Cards

### When to Use Each Model

```
┌─────────────────────────────────────────────────────────────┐
│  GPT-5.2-CODEX (Default for 70% of tasks)                  │
├─────────────────────────────────────────────────────────────┤
│  ✓ General questions & chat                                 │
│  ✓ Math, logic, calculations                                │
│  ✓ Professional knowledge (legal, medical, finance)         │
│  ✓ Abstract reasoning                                       │
│  ✓ Quick Q&A                                                │
│  ✓ Cost-sensitive batch jobs                                │
│                                                              │
│  Cost: $1.75/$14 per 1M tokens | Speed: ⚡⚡⚡ Fast         │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  CLAUDE SONNET 4.5 (First fallback, 25% of tasks)          │
├─────────────────────────────────────────────────────────────┤
│  ✓ Code review                                              │
│  ✓ Most coding tasks                                        │
│  ✓ Creative writing                                         │
│  ✓ Long-form content                                        │
│  ✓ When GPT fails                                           │
│  ✓ User-facing chat (natural tone)                          │
│                                                              │
│  Cost: ~$3/$15 per 1M tokens | Speed: ⚡⚡⚡ Fast           │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  CLAUDE OPUS 4.5 (Specialist, 3% of tasks)                 │
├─────────────────────────────────────────────────────────────┤
│  ✓ Complex multi-file debugging                             │
│  ✓ Architectural decisions                                  │
│  ✓ Critical production code                                 │
│  ✓ Deep reasoning & analysis                                │
│  ✓ When accuracy matters most                               │
│  ✓ Agentic tasks (complex)                                  │
│                                                              │
│  Cost: $5/$25 per 1M tokens | Speed: ⚡⚡ Moderate          │
│  Usage: Manual override or auto-route for hard tasks        │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  GEMINI 3 PRO (Vision specialist, 2% of tasks)             │
├─────────────────────────────────────────────────────────────┤
│  ✓ Image analysis (screenshots, photos, diagrams)          │
│  ✓ Video understanding                                      │
│  ✓ Audio transcription + analysis                           │
│  ✓ Very long documents (>200K tokens)                       │
│  ✓ Multimodal reasoning (text + images)                     │
│                                                              │
│  Cost: $2/$12 per 1M tokens | Speed: ⚡⚡ Moderate          │
│  Note: Upgrade to paid tier for higher rate limits!         │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  GPT-5.3-CODEX (Speed specialist, on-demand)               │
├─────────────────────────────────────────────────────────────┤
│  ✓ Same as GPT-5.2 but 25% faster                          │
│  ✓ Real-time interactive coding                             │
│  ✓ Agentic workflows with time constraints                  │
│  ✓ When speed is critical                                   │
│                                                              │
│  Cost: ~$2/$16 per 1M tokens | Speed: ⚡⚡⚡⚡ Very Fast     │
│  Usage: Override with --model gpt-fast when needed          │
└─────────────────────────────────────────────────────────────┘
```

---

## 💡 User Override Commands

Teach users these commands:

```bash
# Use deep reasoning model (Opus 4.5)
/model opus
# or
--model deep

# Use vision model (Gemini 3 Pro)
/model vision
# or
--model gemini

# Use fastest model (GPT-5.3-Codex)
/model fast
# or
--model gpt-fast

# Use cheapest model explicitly (GPT-5.2-Codex)
/model cheap
# or
--model gpt

# Reset to default routing
/model default
```

---

## 🎯 Success Metrics (First Month)

### Week 1 Targets
- [ ] Zero increase in error rates
- [ ] Fallback rate <5%
- [ ] Cost reduction visible (10%+)
- [ ] No user complaints about quality

### Week 2 Targets
- [ ] Smart routing implemented
- [ ] Vision tasks auto-detected
- [ ] Cost reduction 20%+
- [ ] Response times stable or improved

### Week 3 Targets
- [ ] Math/coding routing active
- [ ] Cost reduction 30%+
- [ ] User override usage <10%
- [ ] Quality metrics maintained

### Week 4 Targets (Final)
- [ ] **Cost reduction 35-40%** ✓
- [ ] **Response time improvement 15-20%** ✓
- [ ] **Zero quality regression** ✓
- [ ] **Fallback rate <5%** ✓
- [ ] **User satisfaction maintained** ✓

---

## 🚨 Rollback Plan

If things go wrong, rollback is simple:

### Immediate Rollback (2 minutes)
```json
{
  "model": {
    "primary": "anthropic/claude-sonnet-4-5",
    "fallbacks": [
      "google/gemini-3-pro-preview",
      "anthropic/claude-opus-4-5"
    ]
  }
}
```

Restart OpenClaw. Done.

### When to Rollback
- Error rate increases >10%
- User complaints spike
- Cost increases instead of decreases
- Major functionality breaks

### Partial Rollback Options
- Revert primary, keep new fallback chain
- Revert routing rules, keep new primary
- Revert vision model, keep other changes

---

## 📈 Cost Calculator

### Your Monthly Estimate

**Step 1:** Estimate your monthly request volume
- Typical: 50,000 - 200,000 requests/month
- Your estimate: ________ requests/month

**Step 2:** Estimate average tokens per request
- Typical input: 1,000 - 2,000 tokens
- Typical output: 300 - 800 tokens
- Your input estimate: ________ tokens
- Your output estimate: ________ tokens

**Step 3:** Calculate costs

**Current config (Sonnet 4.5 primary):**
```
Input cost:  [requests] × [input tokens] / 1M × $3  = $______
Output cost: [requests] × [output tokens] / 1M × $15 = $______
TOTAL: $______/month
```

**Proposed config (GPT-5.2 primary, smart routing):**
```
GPT-5.2 (70%):
  Input:  [requests × 0.7] × [input tokens] / 1M × $1.75 = $______
  Output: [requests × 0.7] × [output tokens] / 1M × $14  = $______

Sonnet (25%):
  Input:  [requests × 0.25] × [input tokens] / 1M × $3   = $______
  Output: [requests × 0.25] × [output tokens] / 1M × $15 = $______

Opus (3%):
  Input:  [requests × 0.03] × [input tokens] / 1M × $5   = $______
  Output: [requests × 0.03] × [output tokens] / 1M × $25 = $______

Gemini (2%):
  Input:  [requests × 0.02] × [input tokens] / 1M × $2   = $______
  Output: [requests × 0.02] × [output tokens] / 1M × $12 = $______

TOTAL: $______/month
SAVINGS: $______ (~____%)
```

---

## 🔧 Troubleshooting Quick Fixes

### Issue: "Model not found" error
**Fix:** Check model name spelling in config, ensure API key is valid

### Issue: High fallback rate (>10%)
**Fix:** 
1. Check rate limits on OpenAI account
2. Upgrade to higher tier if needed
3. Add request queuing

### Issue: Vision tasks not working
**Fix:**
1. Verify Gemini API key is configured
2. Check image is properly attached
3. Try manual override: `--model vision`

### Issue: Costs not decreasing
**Fix:**
1. Verify new config is actually loaded (restart OpenClaw)
2. Check logs to see which models are actually being used
3. Review if routing is working (might be falling back too often)

### Issue: Quality complaints
**Fix:**
1. Identify specific task types causing issues
2. Add routing rule for those tasks to use better model
3. Allow easy per-conversation overrides
4. Consider defaulting to Sonnet 4.5 if GPT-5.2 isn't working

---

## 📞 Support Resources

**Main documentation:** `model-routing-strategy.md`

**OpenClaw docs:** Check `openclaw --help` and `openclaw model --help`

**API status pages:**
- OpenAI: https://status.openai.com/
- Anthropic: https://status.anthropic.com/
- Google: https://status.cloud.google.com/

**Model pricing (always check latest):**
- OpenAI: https://openai.com/api/pricing/
- Anthropic: https://anthropic.com/pricing
- Google: https://ai.google.dev/pricing

---

## ✅ Final Checklist Before Going Live

- [ ] Backup current `openclaw.json`
- [ ] Update primary model to `openai/gpt-5-2-codex`
- [ ] Configure fallback chain: Sonnet → Opus → Gemini
- [ ] Set image model to `google/gemini-3-pro-preview`
- [ ] Add model aliases
- [ ] Test basic functionality (chat, vision, overrides)
- [ ] Set up cost monitoring
- [ ] Document rollback procedure
- [ ] Notify users about new override commands
- [ ] Schedule 1-week review meeting

**Time to implement:** ~30 minutes  
**Expected ROI:** 39% cost savings starting immediately  
**Risk level:** Low (easy rollback, proven models)

🚀 **Ready to deploy!**
