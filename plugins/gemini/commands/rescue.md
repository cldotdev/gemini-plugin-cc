---
description: Delegate a complex rescue task to the gemini-rescue subagent
argument-hint: '[--background] [--wait] [--model <pro|flash|flash-lite>] [--resume] [--fresh] [prompt]'
allowed-tools: Bash(node:*), Agent(gemini-rescue)
---

**REQUIRED SUB-SKILL:** Use `superpowers:gemini-cli-runtime` to call the companion.

Route to the `gemini-rescue` subagent with the user's arguments.

Raw slash-command arguments:
`$ARGUMENTS`

Launch the gemini-rescue agent with the full task description from `$ARGUMENTS`.
