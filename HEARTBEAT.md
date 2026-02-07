# HEARTBEAT.md

# Keep this file empty (or with only comments) to skip heartbeat API calls.

# Add tasks below when you want the agent to check something periodically.

## Daily Checks (Rotate Through)

### Capability Testing (2-3x/week during heartbeats)
- Check TODO.md for next capability to test
- If untested capability available and time permits, test it
- Update CAPABILITIES_MATRIX.md with results

### System Health (1x/day)
- Check disk space (warn if >80%)
- Check memory usage (warn if >90%)
- Check angelbus connectivity

Note: Daily projects have dedicated cron jobs:
- Magic Johnson Outreach: 14:00 UTC daily
- Red Light Therapy Research: 15:00 UTC daily
