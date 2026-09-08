---
name: probe-b
description: Reports which plugin components loaded on the current surface. Use when the user runs the probe, asks what the plugin can see, or asks whether the bundled connector attached. Diagnostic only, it changes nothing.
---

# Plugin Probe

You are running from inside a plugin. Report what actually loaded, plainly and without
guessing. Do not be reassuring. If something is missing, say it is missing.

## What to report

Answer these five things, in order, as a short list.

1. **Skill loaded.** State the marker string below verbatim. If you can read this file,
   the skill loaded.

       PROBE-MARKER-K7Q2

2. **Surface.** Say where you are running: claude.ai chat on the web, the Chat tab in
   Claude Desktop, Cowork, or Claude Code.

3. **Bundled MCP server.** Look at your available tools. Is there a server named
   `probe-remote` present? Answer yes or no. If yes, list its tool names. If no, say
   so plainly. Do not substitute a different connector the user happens to have
   connected separately, and do not count a connector the user set up by hand as
   evidence the plugin supplied it.

4. **Auth state.** If `probe-remote` is present, say whether calling it would need the
   user to authenticate first, and whether you were prompted. If it is absent, skip
   this.

5. **Version.** State the plugin version you are running from, if you can see it.

## Rules

- Report only what you can observe. If you cannot tell, say you cannot tell.
- Do not call any tool to complete this. Reading the tool list is enough.
- Keep the whole report under fifteen lines.

**Plugin version: 3.0.0** — state this verbatim for item 5.
