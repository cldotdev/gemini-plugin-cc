---
description: Cancel a running Gemini job
argument-hint: '[job-id]'
allowed-tools: Bash(node:*)
---

Run:

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/gemini-companion.mjs" cancel $ARGUMENTS
```

Present the output verbatim to the user.
