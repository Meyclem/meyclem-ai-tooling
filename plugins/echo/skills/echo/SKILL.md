---
name: echo
description: Echoes the user's $ARGUMENTS back verbatim, with no transformation or commentary.
---

# Echo

Echo back the user's input verbatim, with no transformation, no commentary, and
no tool call.

- If `$ARGUMENTS` is non-empty, respond with its content unchanged.
- If `$ARGUMENTS` is empty or missing, respond exactly with:

  > (echo: no arguments provided)
