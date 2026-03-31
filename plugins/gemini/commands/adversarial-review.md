---
description: Run an adversarial Gemini code review that challenges design decisions
argument-hint: '[--wait|--background] [--base <ref>] [--scope auto|working-tree|branch] [focus text]'
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Bash(node:*), Bash(git:*), AskUserQuestion
---

Run an adversarial Gemini review. This challenges design decisions, not just finds bugs.

Raw slash-command arguments:
`$ARGUMENTS`

Core constraint:
- This command is review-only.
- Do not fix issues, apply patches, or suggest changes.
- Return Gemini's output verbatim.

Execution mode rules: Same as `/gemini:review`.

Foreground flow:
```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/gemini-companion.mjs" adversarial-review $ARGUMENTS
```

Background flow:
```typescript
Bash({
  command: `node "${CLAUDE_PLUGIN_ROOT}/scripts/gemini-companion.mjs" adversarial-review $ARGUMENTS`,
  description: "Gemini adversarial review",
  run_in_background: true
})
```
- Tell the user: "Gemini adversarial review started. Check `/gemini:status` for progress."
