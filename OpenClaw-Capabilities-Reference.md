# OpenClaw (CloudBot) - Capabilities Reference Guide

**Generated:** 2026-02-10  
**For:** Nityaa  
**Source:** Garuda's CloudBot Tutorial & OpenClaw Documentation

---

## 📋 Table of Contents

1. [Core Philosophy](#core-philosophy)
2. [Configuration System](#configuration-system)
3. [Model Selection & Routing](#model-selection--routing)
4. [Built-in Capabilities](#built-in-capabilities)
5. [Skills & Tools](#skills--tools)
6. [Automation & Scheduling](#automation--scheduling)
7. [Organization Features](#organization-features)
8. [Multimedia Capabilities](#multimedia-capabilities)
9. [Security Features](#security-features)
10. [Key Differentiators](#key-differentiators)

---

## Core Philosophy

OpenClaw is a **highly personal AI assistant** that connects various services through a chat interface. Unlike generic AI, it maintains memory, personality, and deep integration with your tools.

**Key Principles:**
- Transparency over black boxes (plain Markdown files)
- Search over injection (relevant context only)
- Persistence over session (data survives restarts)
- Hybrid over pure (semantic + keyword search)
- Local-first (you own your data)

---

## Configuration System

### Core MD Files

| File | Purpose |
|------|---------|
| **SOUL.md** | Defines personality and behavior |
| **IDENTITY.md** | Sets interaction tone, emojis, style |
| **USER.md** | User-specific information |
| **AGENTS.md** | Agent behavior rules and discipline |
| **TOOLS.md** | Environment-specific tool notes |
| **HEARTBEAT.md** | Recurring tasks (runs every 30 min) |

### Memory System

**Two-Layer Architecture:**

```
~/workspace/
├── MEMORY.md              # Long-term curated knowledge
└── memory/
    ├── 2026-02-10.md      # Today's notes
    ├── 2026-02-09.md      # Yesterday's notes
    └── ...
```

- **Daily Logs:** Append-only session notes
- **Long-term Memory:** Curated important decisions
- **Semantic Search:** Vector + keyword hybrid search
- **Automatic Compaction:** Summarizes old conversations
- **Pre-Compaction Flush:** Saves key info before summarizing

**Memory Tools:**
- `memory_search` - Find relevant memories across files
- `memory_get` - Read specific content

---

## Model Selection & Routing

### Current Configuration

**Primary:** moonshot/kimi-k2.5  
**Fallbacks:**
- openai/gpt-5.2-codex
- google/gemini-2.5-flash
- anthropic/claude-opus-4.5
- anthropic/claude-sonnet-4.5

**Image Model:** google/gemini-3-pro-preview

### Model Aliases

| Alias | Model | Use Case |
|-------|-------|----------|
| `gpt` | openai/gpt-5.1-codex | General tasks |
| `gpt-fast` | openai/gpt-5.2-codex | Speed-critical |
| `sonnet` | anthropic/claude-sonnet-4-5 | Balanced coding |
| `opus` | anthropic/claude-opus-4-5 | Complex reasoning |
| `gemini` | google/gemini-3-pro-preview | Vision/multimodal |
| `vision` | google/gemini-3-pro-preview | Image analysis |
| `deep` | anthropic/claude-opus-4-5 | Deep analysis |

### Model Capabilities Matrix

| Model | Cost (Input/Output) | Best At | Use For | Avoid For |
|-------|-------------------|---------|---------|-----------|
| **GPT-5.2-Codex** | $1.75/$14 per 1M | Math, abstract reasoning | 70% of tasks | Complex multi-file debugging |
| **Sonnet 4.5** | ~$3/$15 per 1M | Balanced, code review | First fallback | When max quality needed |
| **Opus 4.5** | $5/$25 per 1M | Coding (#1 on SWE-bench) | Complex debugging | Simple tasks (overkill) |
| **Gemini 3 Pro** | $2/$12 per 1M | Vision, video, 1M context | Images, long docs | Primary (rate limits) |
| **Kimi K2.5** | Variable | Reasoning, tool use | Current primary | - |

### Switching Models

- **Natural language:** "switch to Sonnet 4.5"
- **Command:** `/model sonnet`
- **Auto-routing:** Based on task complexity

---

## Built-in Capabilities

### Communication
- ✅ **Telegram** - Primary chat interface (DMs & groups)
- ✅ **iMessage** - Apple messaging (configured)
- ✅ **AngelBus** - Inter-agent messaging via MinIO/S3
- ✅ **Gmail** - Read, search, send, organize
- ✅ **Slack** - Team communications

### Google Workspace
- ✅ **Gmail** - Full email management
- ✅ **Calendar** - Event creation and queries
- ✅ **Drive** - File access and management
- ✅ **Contacts** - Contact management
- ✅ **Sheets** - Spreadsheet operations
- ✅ **Docs** - Document access

### Web & Research
- ✅ **Brave Search** - Web search API
- ✅ **Web Fetch** - Extract page content as markdown
- ✅ **Kaleshwar Corpus** - Spiritual text research
- ✅ **YouTube API** - Analytics and data
- ✅ **Grok API** - Twitter/X trend analysis

### Project Management
- ✅ **Asana** - Task management
- ✅ **GitHub** - Repository management (issues, PRs, CI)
- ✅ **Google Tasks** - Simple task management

### Content Creation
- ✅ **Image Generation** - OpenAI GPT-Image-1, DALL-E 3
- ✅ **Image Analysis** - Gemini vision, Claude vision
- ✅ **Text-to-Speech** - ElevenLabs integration
- ✅ **Audio Transcription** - OpenAI Whisper

### Data & Storage
- ✅ **File Operations** - Read, write, edit files
- ✅ **Code Execution** - Bash, Python, Node.js
- ✅ **MinIO/S3** - Object storage (AngelBus)
- ✅ **SQLite** - Local database (memory index)

---

## Skills & Tools

### What Are Skills?

**Skills** = Repeatable processes using multiple tools  
**Tools** = JavaScript files that enable actual functionality

### Skill System

- **Location:** `/app/skills/` and `~/.openclaw/skills/`
- **Format:** SKILL.md + scripts/tools
- **Discovery:** Auto-detected by OpenClaw
- **Repository:** ClawHub.com (official skill repository)

### Current Skills

| Skill | Purpose | Status |
|-------|---------|--------|
| **github** | GitHub CLI integration | ✅ Active |
| **gog** | Google Workspace | ✅ Active |
| **healthcheck** | Security audits | ✅ Active |
| **openai-image-gen** | Image generation | ✅ Active |
| **openai-whisper-api** | Audio transcription | ✅ Active |
| **weather** | Weather forecasts | ✅ Active |
| **angelbus** | MinIO messaging | ✅ Active |
| **kaleshwar-corpus** | Spiritual corpus | ✅ Active |

### Creating Custom Skills

Simply tell OpenClaw what you need in natural language:
- "I need a skill that fetches weather data"
- Skills auto-created with SKILL.md + scripts
- May require API keys from services

---

## Automation & Scheduling

### Cron System

**One-off tasks:**
```
"Remind me in one hour to drink water"
```

**Recurring tasks:**
- Complex schedules (e.g., alternating trash pickup)
- Upload images/docs to teach schedules
- Automated reminders based on learned patterns

**Current Scheduled Jobs:**
- 🔒 Weekly Security Audit - Sundays 03:00 UTC
- 📊 Hourly Health Monitor - Disk/memory checks
- 🏀 Magic Johnson Outreach - Daily 14:00 UTC
- 🔴💡 Red Light Research - Daily 15:00 UTC
- 🕊️ AngelBus Monitor - Every 5 minutes

### Daily Maintenance

**Automated code review:**
- Reviews: agents.md, memory.md, tools, soul, identity, user, heartbeat
- Identifies: outdated info, conflicting rules, undocumented workflows
- Proposes: changes for user approval

---

## Organization Features

### Telegram Groups with Topics

**Benefits over single DM:**
- Parallel conversations
- Context separation
- Reduced memory usage

**Topic Examples:**
- Video research
- Twitter research
- E-book development
- Content analysis
- System administration

**Setup:**
1. Create Telegram group
2. Add OpenClaw as admin
3. Configure to reply to all messages

### Memory Organization

**Automatic filing:**
- Daily notes → `memory/YYYY-MM-DD.md`
- Important decisions → `MEMORY.md`
- Skill references → `SKILL.md`
- Archive → `archive/` directory

---

## Multimedia Capabilities

### Image Generation
- **Models:** GPT-Image-1, DALL-E 3
- **Features:** Custom prompts, sizes, styles
- **Output:** PNG with metadata + HTML gallery

### Image Analysis
- **Models:** Gemini 3 Pro Vision, Claude Sonnet 4.5
- **Capabilities:** Object detection, text recognition, scene understanding
- **Input:** Images via Telegram, file paths, URLs

### Text-to-Speech
- **Provider:** ElevenLabs
- **Format:** Opus, 24kHz mono
- **Use:** Telegram voice messages, audio files

### Audio Transcription
- **Provider:** OpenAI Whisper API
- **Input:** MP3, WAV, M4A
- **Output:** Accurate text transcription

---

## Security Features

### API Key Management
- **Storage:** `.env` file only
- **Git:** Never include `.env` in repositories
- **Environment:** Loaded securely at runtime

### Security Audits
- **Command:** `healthcheck` skill
- **Checks:** File permissions, gateway config, access controls
- **Schedule:** Weekly automated audits
- **Fixes:** Automated or guided remediation

### Data Sanitization
- **"Dirty data":** External/internet sources (prompt injection risk)
- **Protection:** Built-in prompt injection detection
- **Mitigation:** Use better models (Opus less susceptible)

### Additional Measures
- **VPS Hosting:** Complete isolation from local environment
- **Regular Updates:** New security features released frequently
- **Skill Verification:** Scan ClawHub skills before installation
- **Plan Mode:** Propose actions before executing
- **Zero-Trust:** Don't trust external skills without verification

---

## Key Differentiators

1. **🔮 Persistent Memory**
   - Remembers across sessions indefinitely
   - Semantic search through all history
   - Automatic compaction and maintenance

2. **🔌 Deep Integration**
   - Connects to actual tools (not just chat)
   - Gmail, Calendar, Drive, GitHub, Telegram
   - Skills for any service with API

3. **🎭 Personalized**
   - Learns your preferences and workflow
   - Customizable personality (SOUL.md)
   - Adapts to your communication style

4. **🎨 Multi-Modal**
   - Text, voice, images, video
   - Generate and analyze multimedia
   - Voice conversations via Telegram

5. **👥 Agent Network**
   - Spawn sub-agents for background tasks
   - Inter-agent messaging via AngelBus
   - Divine Network of specialized angels

6. **📈 Self-Improving**
   - Daily audits and memory maintenance
   - Automatic documentation updates
   - Learns from failures and successes

7. **🔒 Privacy-First**
   - Local data storage
   - You own everything
   - No vendor lock-in

---

## Quick Reference Commands

### Model Control
```
/model sonnet          # Switch to Sonnet 4.5
/model opus            # Switch to Opus 4.5
/model gemini          # Switch to Gemini 3 Pro
/model kimi-k2.5       # Switch to Kimi K2.5
/status                # Show current status
```

### Gmail
```
Search: "from:sender@example.com newer_than:7d"
Send: gog gmail send --to recipient --subject "Title" --body "Message"
Label: gog gmail labels create "Label Name"
```

### Calendar
```
List events: gog calendar events primary --from 2026-02-10 --to 2026-02-11
Create: gog calendar create primary --summary "Meeting" --from 2026-02-10T10:00:00Z --to 2026-02-10T11:00:00Z
```

### Drive
```
Search: gog drive search "query" --max 10
List: gog drive list --max 20
```

### Memory
```
Search: memory_search "what did we decide about X?"
Read: memory_get --path memory/2026-02-10.md --from 1 --lines 50
```

### AngelBus
```
Send: angelbus_send <recipient> "message"
Inbox: angelbus_inbox
Peek: angelbus_peek <message-key>
```

---

## Resources

- **OpenClaw Docs:** /app/docs or https://docs.openclaw.ai
- **GitHub:** https://github.com/openclaw/openclaw
- **Community:** https://discord.com/invite/clawd
- **Skill Repository:** https://clawhub.com

---

*Generated by Shishya for Nityaa*  
*Part of the Divine Network*  
*🙏🏾*
