---
name: debug
description: Debug production and development issues in the API
---

## When to use this Skill

Help debug issues in the API, job queue, or database layer.

## Ask for:

1. **Issue description** (what's happening, what's expected?)
2. **Error messages** (logs, stack traces, error output)
3. **Steps to reproduce** (how to trigger the issue)
4. **Environment** (dev, staging, production; Node version, etc.)

## Debugging Approach

1. Reproduce the issue locally if possible
2. Examine error logs and stack traces
3. Check recent code changes
4. Verify configuration and environment variables
5. Test database connections and queries
6. Review job queue status (BullMQ)
7. Check for resource issues (memory, CPU, connections)
8. Propose fix with test case

## Debugging Tools

- Node.js debugger
- Console logging and structured logs
- Database query analysis
- Job queue status and logs
- Docker logs and container status
- Performance profiling tools
- Network request inspection

## Requirements

- Always read `.github/copilot-instructions.md` in the current working directory first to understand the project's architecture, conventions, and commands
- Follow project's documentation standards from `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/javascript-camm-bac.instructions` and `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/conventions-camm-bac.instructions`
- Provide minimal reproducible example
- Include comprehensive logs
- Suggest both immediate fix and long-term solution
- Include tests to prevent regression
- Document findings for knowledge base
