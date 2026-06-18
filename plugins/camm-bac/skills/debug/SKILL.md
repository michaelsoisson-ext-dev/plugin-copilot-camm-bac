---
name: debug
description: Debug production and development issues in the API, job queue, and database. Use this skill whenever encountering API errors, job queue failures, database connection issues, performance problems, memory leaks, Docker container issues, or any backend system failure. Covers systematic debugging workflows, database query analysis, job queue inspection, performance profiling, and resource monitoring.
---

## When to use this Skill

Systematically debug issues in the API, job queue (BullMQ), or database layer. Use for production outages, development errors, intermittent issues, and performance problems.

## Quick Triage

1. **Issue description** — What's happening vs. what's expected?
2. **Error messages** — Full logs, stack traces, error output
3. **Steps to reproduce** — How to trigger the issue consistently
4. **Environment** — Dev/staging/production, Node version, recent changes

## Debugging Workflow

**Phase 1: Information Gathering**
- Collect logs and error messages with full context
- Identify environment (production, staging, dev)
- Note recent code or configuration changes

**Phase 2: Local Reproduction**
- Try to reproduce locally (most reliable for root cause analysis)
- If production-only, examine logs carefully and compare configs

**Phase 3: Systematic Investigation**
- Examine error messages and stack traces
- Check recent code changes (git history)
- Verify configuration and environment variables
- Test database/queue connectivity
- Check for resource issues (memory, CPU, connections)

**Phase 4: Solution & Prevention**
- Propose fix addressing root cause, not symptoms
- Add test case to prevent regression
- Document findings

## Key Tools & Resources

**Debugging Reference Files:**
- `references/debugging-guide.md` — Full debugging workflow, database debugging, job queue issues, performance debugging, common patterns
- `references/debugging-tools.md` — Commands for Docker, MariaDB, Redis/BullMQ, Node.js profiling, Git bisect, health checks

## Requirements

- Read `.github/copilot-instructions.md` first to understand project architecture and conventions
- Provide reproducible example (minimal, focused)
- Include comprehensive logs with full context
- Suggest both immediate fix and long-term prevention
- Add test case to prevent regression
- Document debugging findings for knowledge base
