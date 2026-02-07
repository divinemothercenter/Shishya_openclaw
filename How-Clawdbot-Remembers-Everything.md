# How Clawdbot Remembers Everything

Source: Blog article about OpenClaw's memory system
Received: 2026-02-07 from Nityaa

---

[Full document content from messages 197, 198, 199, 203...]

## Overview

Clawdbot (OpenClaw) is an open-source personal AI assistant created by Peter Steinberger with 32,600+ GitHub stars. Unlike cloud-based AI (ChatGPT, Claude), it runs locally with full user ownership of data and context.

## Key Innovation: Persistent Memory System

Maintains 24/7 context retention through local storage, remembering conversations indefinitely.

---

## Context vs Memory

### Context (Ephemeral)
What the model sees for a single request:
- System Prompt
- Conversation History  
- Tool Results
- Attachments

**Properties:**
- Ephemeral (exists only for this request)
- Bounded (limited by context window, e.g. 200K tokens)
- Expensive (every token counts toward API costs)

### Memory (Persistent)
What's stored on disk:
- MEMORY.md
- memory/*.md
- Session Transcripts

**Properties:**
- Persistent (survives restarts, days, months)
- Unbounded (can grow indefinitely)
- Cheap (no API cost to store)
- Searchable (indexed for semantic retrieval)

---

## The Memory Tools

### 1. memory_search
**Purpose:** Find relevant memories across all files

**Parameters:**
```json
{
  "query": "What did we decide about the API?",
  "maxResults": 6,
  "minScore": 0.35
}
```

**Returns:**
```json
{
  "results": [{
    "path": "memory/2026-01-20.md",
    "startLine": 45,
    "endLine": 52,
    "score": 0.87,
    "snippet": "## API Discussion...",
    "source": "memory"
  }],
  "provider": "openai",
  "model": "text-embedding-3-small"
}
```

### 2. memory_get
**Purpose:** Read specific content after finding it

**Parameters:**
```json
{
  "path": "memory/2026-01-20.md",
  "from": 45,
  "lines": 15
}
```

---

## Writing to Memory

**No dedicated memory_write tool.** Agent uses standard `write` and `edit` tools.

Decision of where to write is **prompt-driven via AGENTS.md**:
- Daily logs: `memory/YYYY-MM-DD.md`
- Long-term: `MEMORY.md`

Automatic writes occur during:
- Pre-compaction flush
- Session end

---

## Memory Storage: Two-Layer System

```
~/clawd/
├── MEMORY.md              # Layer 2: Long-term curated knowledge
└── memory/
    ├── 2026-01-26.md      # Layer 1: Today's notes
    ├── 2026-01-25.md      # Yesterday's notes
    └── ...
```

### Layer 1: Daily Logs (memory/YYYY-MM-DD.md)

**Append-only** daily notes written throughout the day.

**Example:**
```markdown
# 2026-01-26

## 10:30 AM - API Discussion
Discussed REST vs GraphQL with user. 
Decision: use REST for simplicity.
Key endpoints: /users, /auth, /projects.

## 2:15 PM - Deployment  
Deployed v2.3.0 to production. No issues.

## 4:00 PM - User Preference
User mentioned they prefer TypeScript over JavaScript.
```

### Layer 2: Long-term Memory (MEMORY.md)

**Curated, persistent knowledge.** Significant events, thoughts, decisions, opinions, lessons learned.

**Example:**
```markdown
# Long-term Memory

## User Preferences
- Prefers TypeScript over JavaScript
- Likes concise explanations
- Working on project "Acme Dashboard"

## Important Decisions
- 2026-01-15: Chose PostgreSQL for database
- 2026-01-20: Adopted REST over GraphQL
- 2026-01-26: Using Tailwind CSS for styling

## Key Contacts
- Alice (alice@acme.com) - Design lead
- Bob (bob@acme.com) - Backend engineer
```

---

## How Agent Knows to Read Memory

**AGENTS.md contains instructions:**

```markdown
## Every Session
Before doing anything else:
1. Read SOUL.md - this is who you are
2. Read USER.md - this is who you are helping  
3. Read memory/YYYY-MM-DD.md (today and yesterday) for recent context
4. If in MAIN SESSION (direct chat with your human), also read MEMORY.md

Don't ask permission, just do it.
```

---

## How Memory Gets Indexed

### Indexing Pipeline

```
┌─────────────────────────────────────────────────────────────┐
│ 1. File Saved                                               │
│    ~/clawd/memory/2026-01-26.md                            │
└─────────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ 2. File Watcher Detects Change                             │
│    Chokidar monitors MEMORY.md + memory/**/*.md            │
│    Debounced 1.5 seconds to batch rapid writes             │
└─────────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ 3. Chunking                                                 │
│    Split into ~400 token chunks with 80 token overlap      │
│                                                             │
│    ┌────────────────┐                                      │
│    │ Chunk 1        │                                      │
│    │ Lines 1-15     │──────┐                               │
│    └────────────────┘      │                               │
│    ┌────────────────┐      │ (80 token overlap)            │
│    │ Chunk 2        │◄─────┘                               │
│    │ Lines 12-28    │──────┐                               │
│    └────────────────┘      │                               │
│    ┌────────────────┐      │                               │
│    │ Chunk 3        │◄─────┘                               │
│    │ Lines 25-40    │                                      │
│    └────────────────┘                                      │
│                                                             │
│    Why 400/80? Balances semantic coherence vs granularity. │
│    Overlap ensures facts spanning chunk boundaries are     │
│    captured in both. Both values are configurable.         │
└─────────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ 4. Embedding                                                │
│    Each chunk → embedding provider → vector                 │
│                                                             │
│    "Discussed REST vs GraphQL" →                           │
│    OpenAI/Gemini/Local →                                    │
│    [0.12, -0.34, 0.56, ...] (1536 dimensions)             │
└─────────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ 5. Storage                                                  │
│    ~/.clawdbot/memory/<agentId>.sqlite                     │
│                                                             │
│    Tables:                                                  │
│    - chunks (id, path, start_line, end_line, text, hash)  │
│    - chunks_vec (id, embedding) → sqlite-vec               │
│    - chunks_fts (text) → FTS5 full-text                    │
│    - embedding_cache (hash, vector) → avoid re-embedding   │
└─────────────────────────────────────────────────────────────┘
```

**sqlite-vec:** SQLite extension for vector similarity search (no external vector DB needed)

**FTS5:** SQLite's built-in full-text search (BM25 keyword matching)

Together: **Hybrid search (semantic + keyword)** from single lightweight database file.

---

## How Memory is Searched

### Hybrid Search Strategy

Runs **two searches in parallel:**

1. **Vector search (semantic)** - finds content that means the same thing
2. **BM25 search (keyword)** - finds content with exact tokens

**Combined with weighted scoring:**
```
finalScore = (0.7 × vectorScore) + (0.3 × textScore)
```

**Why 70/30?**
- Semantic similarity = primary signal
- BM25 keyword = catches exact terms vectors might miss (names, IDs, dates)

**Filtering:**
- Results below `minScore` threshold (default 0.35) filtered out
- All values configurable

**Result:** Good results whether searching concepts ("that database thing") or specifics ("POSTGRES_URL")

---

## Multi-Agent Memory

### Complete Memory Isolation

```
~/.clawdbot/memory/          # State directory (indexes)
├── main.sqlite              # Vector index for "main" agent
└── work.sqlite              # Vector index for "work" agent

~/clawd/                     # "main" agent workspace (source files)
├── MEMORY.md
└── memory/
    └── 2026-01-26.md

~/clawd-work/                # "work" agent workspace (source files)
├── MEMORY.md
└── memory/
    └── 2026-01-26.md
```

**Source of truth:** Markdown files in workspaces  
**Derived data:** SQLite indexes in state directory

**Memory manager keyed by:** `agentId` + `workspaceDir`  
**No cross-agent memory search** happens automatically.

### Can agents read each other's memories?

**Not by default.** Each agent only sees its own workspace.

**However:** Workspace is a **soft sandbox** (default working directory), not hard boundary. Agent could theoretically access another workspace using absolute paths unless strict sandboxing enabled.

**Use case:** Separation of contexts (e.g., "personal" agent for WhatsApp, "work" agent for Slack)

---

## Compaction

### The Problem

Every AI model has context window limits:
- Claude: 200K tokens
- GPT-5.1: 1M tokens

Long conversations eventually hit this wall.

### The Solution: Compaction

Summarize older conversation into compact entry while keeping recent messages intact.

```
┌─────────────────────────────────────────────────────────────┐
│ Before Compaction                                           │
│ Context: 180,000 / 200,000 tokens                          │
│                                                             │
│ [Turn 1] User: "Let's build an API"                        │
│ [Turn 2] Agent: "Sure! What endpoints do you need?"        │
│ [Turn 3] User: "Users and auth"                            │
│ [Turn 4] Agent: *creates 500-line schema*                  │
│ [Turn 5] User: "Add rate limiting"                         │
│ [Turn 6] Agent: *modifies code*                            │
│ ... (100 more turns) ...                                   │
│ [Turn 150] User: "What's the status?"                      │
│                                                             │
│ ⚠️ APPROACHING LIMIT                                        │
└─────────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ Compaction Triggered                                        │
│                                                             │
│ 1. Summarize turns 1-140 into a compact summary            │
│ 2. Keep turns 141-150 intact (recent context)              │
│ 3. Persist summary to JSONL transcript                     │
└─────────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ After Compaction                                            │
│ Context: 45,000 / 200,000 tokens                           │
│                                                             │
│ [SUMMARY] "Built REST API with /users, /auth endpoints.    │
│            Implemented JWT auth, rate limiting (100 req/min),│
│            PostgreSQL database. Deployed to staging v2.4.0. │
│            Current focus: production deployment prep."      │
│                                                             │
│ [Turn 141-150 preserved as-is]                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Automatic vs Manual Compaction

**Automatic:**
- Triggers when approaching context limit
- You see: `🧹 Auto-compaction complete` in verbose mode
- Original request retries with compacted context

**Manual:**
```
/compact
/compact Focus on decisions and open questions
```

**Persistence:** Summary written to session's JSONL transcript file → future sessions start with compacted history

---

## The Memory Flush

### The Problem

LLM-based compaction is **lossy**. Important information may be summarized away and lost.

### The Solution: Pre-Compaction Memory Flush

```
┌─────────────────────────────────────────────────────────────┐
│ Context Approaching Limit                                   │
│                                                             │
│ ████████████████████████████░░░░░░░░ 75% of context        │
│                                      ↑                      │
│              Soft threshold crossed                         │
│     (contextWindow - reserve - softThreshold)              │
└─────────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ Silent Memory Flush Turn                                    │
│                                                             │
│ System: "Pre-compaction memory flush. Store durable        │
│          memories now (use memory/YYYY-MM-DD.md).          │
│          If nothing to store, reply with NO_REPLY."        │
│                                                             │
│ Agent: reviews conversation for important info             │
│        writes key decisions/facts to memory files          │
│        → NO_REPLY (user sees nothing)                      │
└─────────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ Compaction Proceeds Safely                                  │
│                                                             │
│ Important information is now on disk                        │
│ Compaction can proceed without losing knowledge            │
└─────────────────────────────────────────────────────────────┘
```

### Configuration

```json
{
  "agents": {
    "defaults": {
      "compaction": {
        "reserveTokensFloor": 20000,
        "memoryFlush": {
          "enabled": true,
          "softThresholdTokens": 4000,
          "systemPrompt": "Session nearing compaction. Store durable memories now.",
          "prompt": "Write lasting notes to memory/YYYY-MM-DD.md; reply NO_REPLY if nothing to store."
        }
      }
    }
  }
}
```

---

## Pruning

### The Problem

Tool results can be huge:
- Single `exec` command: 50,000 chars of logs
- Pruning trims old outputs without rewriting history
- **Lossy process** - old outputs not recoverable

```
┌─────────────────────────────────────────────────────────────┐
│ BEFORE PRUNING (in-memory)                                  │
│                                                             │
│ Tool Result (exec): [50,000 chars of npm install output]  │
│ Tool Result (read): [Large config file, 10,000 chars]     │
│ Tool Result (exec): [Build logs, 30,000 chars]            │
│ User: "Did the build succeed?"                             │
└─────────────────────────────────────────────────────────────┘
                         │
                         ▼ (Soft trim + hard clear)
┌─────────────────────────────────────────────────────────────┐
│ AFTER PRUNING (sent to model)                               │
│                                                             │
│ Tool Result (exec): "npm WARN deprecated...[truncated]    │
│                      ...Successfully installed."            │
│ Tool Result (read): "[Old tool result content cleared]"   │
│ Tool Result (exec): [Kept - too recent to prune]          │
│ User: "Did the build succeed?"                             │
└─────────────────────────────────────────────────────────────┘

JSONL file on disk: UNCHANGED (full outputs still there)
```

---

## Cache-TTL Pruning

### The Problem

Anthropic caches prompt prefixes for up to 5 minutes:
- Same prefix within TTL → cached tokens cost ~90% less
- After TTL expires → must re-cache full conversation at full "cache write" pricing
- If session goes idle past TTL → next request loses cache, must re-cache entire history

### The Solution

Detect when cache expired, trim old tool results **before** next request.  
→ Smaller prompt to re-cache = lower cost

### Configuration

```json
{
  "agent": {
    "contextPruning": {
      "mode": "cache-ttl",           // Only prune after cache expires
      "ttl": "600",                  // Match your cacheControlTtl
      "keepLastAssistants": 3,       // Protect recent tool results
      "softTrim": {
        "maxChars": 4000,
        "headChars": 1500,
        "tailChars": 1500
      },
      "hardClear": {
        "enabled": true,
        "placeholder": "[Old tool result content cleared]"
      }
    }
  }
}
```

---

## Session Lifecycle

Sessions don't last forever. They reset based on configurable rules, creating natural boundaries for memory.

**Default behavior:** Reset every day  
**Other modes available:** (see configuration)

---

## Session Memory Hook

When you run `/new` to start fresh session, can automatically save context:

```
/new
  │
  ▼
┌─────────────────────────────────────────────────────────────┐
│ SESSION-MEMORY HOOK TRIGGERED                               │
│                                                             │
│ 1. Extract last 15 messages from ending session            │
│ 2. Generate descriptive slug via LLM                       │
│ 3. Save to ~/clawd/memory/2026-01-26-api-design.md        │
└─────────────────────────────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────────────────────────────┐
│ NEW SESSION STARTS                                          │
│                                                             │
│ Previous context is now searchable via memory_search       │
└─────────────────────────────────────────────────────────────┘
```

---

## Conclusion: Key Principles

Clawdbot's memory system succeeds because it embraces:

### 1. Transparency Over Black Boxes
Memory is plain Markdown. You can read it, edit it, version control it. No opaque databases or proprietary formats.

### 2. Search Over Injection
Rather than stuffing context with everything, agent searches for what's relevant. Keeps context focused and costs down.

### 3. Persistence Over Session
Important information survives in files on disk, not just conversation history. Compaction can't destroy what's already saved.

### 4. Hybrid Over Pure
Vector search alone misses exact matches. Keyword search alone misses semantics. Hybrid gives you both.

---

## References

- **Clawdbot Documentation:** Official docs covering setup, configuration, all features
- **GitHub Repository:** Source code, issues, community contributions

---

*Document preserved for analysis and implementation guidance.*
