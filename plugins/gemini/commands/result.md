---
description: Show the stored final output for a finished Gemini job
argument-hint: '[job-id]'
allowed-tools: Bash(node:*)
---

Run:

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/gemini-companion.mjs" result $ARGUMENTS
```

Present the output verbatim to the user. Do not summarize or add commentary.
