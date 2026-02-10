# HEARTBEAT.md - Optimized for Token Efficiency

# Keep this file empty (or with only comments) to skip heartbeat API calls.

# Add tasks below when you want the agent to check something periodically.

## ⚡ Ultra-Minimal Checks (Token Optimized)

### System Health (1x/day max)
- Disk >80% warning only
- Memory >90% warning only
- AngelBus down alerts only

### Capability Testing (1x/week max)
- Check TODO.md only if no recent tests
- Test only critical untested capabilities
- Update matrix only on success

## 🎯 Alert-Only Mode
Only respond with alerts for:
- System health warnings (>80% disk, >90% memory)
- Service outages (AngelBus down)
- Critical capability failures

## 📊 Current Status
Last system check: healthy
AngelBus: operational (Restored 2026-02-10 03:35 UTC)
No alerts at this time

## 📝 Notes
Daily projects run via cron:
- Magic Johnson: 14:00 UTC
- Red Light Research: 15:00 UTC

Token budget: <100 tokens per heartbeat
