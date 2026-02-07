# CloudBot Security & Setup Guide

**Date:** 2026-02-07  
**Thread:** Topic 7 - Divine Network HQ  
**Host:** Garuda  
**Continuation of:** CloudBot Tutorial Summary

---

## Security Best Practices

### API Key Management

- **Store all API keys and tokens in `.env` file only**
- **Never include `.env` in Git repositories**
- **Command prompt:** "never store an API key or token anywhere but a dot env file and never include a dot env file in your Git"

### Security Audits

- Run `openclaw security audit` from terminal
- Auto-fix issues: `openclaw security audit --fix`
- Regular security checkups recommended

### Clean vs. Dirty Data

**"Dirty data"**: anything from internet or external sources (potential prompt injection risk)

**Email vulnerabilities:**
- Malicious actors can embed prompt injections

**Mitigation:**
- Use better models (Opus 4.5 less susceptible than Haiku)
- Built-in prompt injection detection (not perfect)

### Additional Security Measures

- Use VPS hosting for complete isolation from local environment
- Update frequently (new security features released regularly)
- Don't trust skills from ClawHub without verification
- Have CloudBot write skills internally when possible
- Limit integrations based on data sensitivity
- Use **"plan mode"**: have CloudBot propose actions before executing

### Risk Management

- Balance capability vs. risk
- Limit exposure to prompt injection through selective integrations
- Be mindful of sensitive data exposure
- Zero-trust approach to external skills

---

## Real-World Use Cases

### Video Production Workflow

1. Drop link in Telegram (video topic idea)
2. CloudBot researches using Brave API
3. Checks Twitter trends using Grok API
4. Creates Asana task with research summary
5. Task appears in video ideas bucket for team

### YouTube Analytics

- Access YouTube Data API and Analytics API
- Query performance: "Give me the last three videos. How are they performing?"
- CloudBot fetches and reports statistics
- Can post updates to Slack for team visibility

### Daily Meeting Preparation

- Morning cron job checks Google Calendar
- Filters external meetings only
- Researches meeting participants
- Checks Gmail for context (only from known contacts)
- Delivers morning summary in Telegram with meeting prep details

---

## Actionable Items

### Initial Setup Tasks

- [ ] Sign up for Hostinger VPS using code "MatthewB" at `hostinger.com/MatthewB`
- [ ] Deploy CloudBot with one-click installation
- [ ] Configure API keys from AI providers (Anthropic, OpenAI, Gemini, XAI)
- [ ] Set up Telegram or preferred chat interface
- [ ] Complete OpenClaw onboarding through Terminal

### Configuration Tasks

- [ ] Review and customize `soul.md` for personality preferences
- [ ] Review and customize `identity.md` for tone and communication style
- [ ] Configure model hierarchy (primary, fallback chain)
- [ ] Set up Telegram group with topic channels for organization

### Security Implementation

- [ ] Run `openclaw security audit` and fix any warnings
- [ ] Ensure all API keys stored in `.env` file only
- [ ] Configure `.env` exclusion from Git
- [ ] Set CloudBot to propose actions before executing complex tasks
- [ ] Review and limit integrations based on data sensitivity

### Daily Operations

- [ ] Set up daily codebase review cron job
- [ ] Configure morning meeting prep automation
- [ ] Review and prune memory folder periodically
- [ ] Update CloudBot regularly for new security features

### Integration Setup

- [ ] Connect Gmail (with selective email scanning from known contacts)
- [ ] Connect Google Calendar
- [ ] Connect Google Drive
- [ ] Connect Asana or project management tool
- [ ] Connect Slack for team communications
- [ ] Set up YouTube API access for analytics (if applicable)
- [ ] Configure any additional service integrations as needed

### Testing and Optimization

- [ ] Test model switching capabilities
- [ ] Create and test custom skills for workflows
- [ ] Set up cron jobs for recurring reminders and tasks
- [ ] Test multimedia capabilities (image, voice, video)
- [ ] Verify security audit results and fixes

---

**Source:** Telegram Divine Network HQ, Topic 7, Message 93  
**Author:** 🙏🏾 🪔 (Nityaa)
