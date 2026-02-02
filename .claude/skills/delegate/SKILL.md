---
name: delegate
description: Use when user explicitly requests agent delegation with /delegate. Spawns an appropriate agent via the Task tool.
---

# Delegate to Agent

## REQUIRED

You MUST use the Task tool to spawn an agent. Do NOT execute the work directly.

This is the entire purpose of `/delegate` - preserving top-level context by
offloading work to a subagent.

## Instructions

1. Parse everything after `/delegate` as the task description
1. Choose the appropriate agent type based on the task
1. **Immediately invoke the Task tool** - no preliminary work, no
   "let me first check..."
1. Report the agent's results back to the user

## Agent Selection

| Task Type          | Agent                 |
| ------------------ | --------------------- |
| Find code/files    | Explore               |
| Design approach    | Plan                  |
| Run commands       | Bash                  |
| Complex/multi-step | general-purpose       |
| Code review        | code-reviewer         |
| Web research       | web-search-researcher |

## Anti-patterns (DO NOT)

- Running Bash commands directly
- Reading files yourself first
- "Let me quickly check..." before delegating
- Any tool call that isn't Task
