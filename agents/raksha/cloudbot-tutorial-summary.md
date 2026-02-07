# Garuda: CloudBot Tutorial Summary

**Date:** 2026-02-07  
**Thread:** Topic 7 - Divine Network HQ  
**Host:** Garuda

---

## Introduction to CloudBot

CloudBot is a highly personal and capable AI assistant that has become an essential piece of technology for many users. It connects various services like Gmail, Telegram, Asana, Slack, and others, allowing users to manage tasks through a chat interface.

### Getting Started
- Connect to Telegram or preferred chat interface

---

## Core Configuration Files

### Understanding MD Files

**Soul.md**
- Defines CloudBot's personality and behavior

**Skills**
- Contains all capabilities (browsing, email, Twitter, custom skills)
- Repeatable processes using multiple tools

**Tools**
- JavaScript files that enable actual functionality
- Example: `asanafetch.js` for Asana integration

**Identity.md**
- Sets interaction tone, emojis, and communication style

**Memory folder**
- Stores learned information about the user
- Can be pruned as needed

**Heartbeat.md**
- Runs recurring tasks (default every 30 minutes)

**User.md**
- User-specific information

---

## Model Selection and Routing

### Model Hierarchy Setup

**Primary model:** Claude Sonnet 4.5 (workhorse, cost-effective)

**Fallback chain:**
- Gemini 3 Flash (fast and cheap)
- Opus 4.5
- OpenRouter
- Local models available for basic tasks and cron jobs

Model selection impacts personality and performance.

### Switching Models

- **Natural language:** "switch to Sonnet 4.5"
- **Command:** `type show` or `set models`
- **Complex coding tasks:** route to Opus 4.5
- **Basic interactions:** use Sonnet or HYCU for speed and cost savings

---

## Skills and Tools Integration

### Adding New Services

- Simply tell CloudBot what you need in natural language
- CloudBot automatically creates skills if they don't exist
- May require API keys from services
- **Supported integrations:** Gmail, Drive, Calendar, Asana, Slack, HubSpot, and more

### Advanced Integration

- **Cursor Agent** integration for complex coding tasks
- **ClawHub.com:** official skill repository for browsing available skills
- **Security note:** Scan downloaded skills for malicious code using best model before installation

---

## Scheduled Tasks and Cron Jobs

### Setting Up Automation

**One-off tasks:**
- "In one hour, remind me to drink water"

**Recurring tasks:**
- Complex schedules like alternating trash pickup
- Upload images/documents to teach CloudBot schedules
- Automated reminders based on learned patterns

---

## Telegram Groups for Organization

### Advanced Conversation Management

Use Telegram groups with multiple topics instead of single DM thread.

**Benefits:**
- Parallel conversations
- Context separation
- Reduced memory usage

**Topic examples:**
- Video research
- Twitter research
- E-book development
- Content analysis

### Setup Process

1. Create new Telegram group
2. Add CloudBot as only user
3. Make CloudBot administrator
4. Configure to reply to all messages (not just tagged mentions)

---

## Multimedia and Advanced Features

### Capabilities

- **Image creation** (e.g., Nano Banana integration)
- **Voice capabilities** (11 Labs integration)
- **Image reading:** drag and drop in Telegram
- **Video creation and processing**
- **Multi-format content generation**

---

## Daily Maintenance

### Automated Code Review

Set up daily audit of CloudBot's codebase.

**Reviews:**
- `agents.md`, `memory.md`, tools, soul, identity, user, heartbeat

**Identifies:**
- Outdated info
- Conflicting rules
- Undocumented workflows
- Lessons from failures

**Proposes changes for user approval**

Keeps system clean and optimized.

---

## Security Best Practices

*(Note: Original message truncated here - security practices to be detailed in follow-up documentation)*

---

**Source:** Telegram Divine Network HQ, Topic 7, Message 92  
**Author:** 🙏🏾 🪔 (Nityaa)
