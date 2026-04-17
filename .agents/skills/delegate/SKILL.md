---
name: delegate
description: >-
  Use when user explicitly requests agent delegation with
  /delegate. Spawns an appropriate subagent to handle the work.
---

# Delegate to Agent

Delegate the task to a subagent. Do not execute work directly.

1. Parse everything after `/delegate` as the task description
2. Select a subagent role from the table below
3. Invoke the subagent immediately — no preliminary reads or
   commands
4. Report results

## Agent Selection

Route to the subagent that best matches the task. The roles
below are descriptions, not agent names — use your platform's
actual agent names.

| Task Type                          | Subagent Role                    |
| ---------------------------------- | -------------------------------- |
| Find code/files, trace deps        | Exploration / investigation      |
| Design approach, architecture      | Planning                         |
| Commands, multi-step work, refactor| General-purpose / workhorse      |
| Code review \*                     | Review specialist                |
| Web research \*                    | Research specialist               |

\* Fall back to your platform's general-purpose / workhorse
agent if no specialist is available.

## Anti-patterns

- Reading files or running commands before delegating
- "Let me quickly check..." before spawning the subagent
