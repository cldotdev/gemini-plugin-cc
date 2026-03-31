---
description: Show active and recent Gemini jobs in this workspace
argument-hint: '[job-id] [--wait] [--timeout-ms N] [--all]'
allowed-tools: Bash(node:*)
---

Run:

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/gemini-companion.mjs" status $ARGUMENTS
```

Present the output verbatim to the user.
