# Quickstart

**What you need:** the plugin installed (see [Install](../01-getting-started/02-install.md)), and one AI coding agent already on your
computer — Claude Code, Cursor, Cline, Codex, or Claude Desktop. You'll also need Python, but don't
worry about that part: your agent checks for it and installs it for you if it's missing, explaining
what it's about to do first.

**What happens:** paste the prompt below into your agent's chat. It connects the editor to your
agent and confirms it worked — about two minutes, no manual file editing.

## Copy this to your agent

    I just installed the Nightward MCP plugin for Unreal Engine and want you to set it up for me.

    1. Check whether Python is installed and available from the command line (`python --version` or
       `python3 --version`). If it isn't, tell me exactly what you're about to install (e.g.
       `winget install Python.Python.3.12` on Windows, `brew install python@3.12` on Mac,
       `apt install python3` on Linux) and wait for me to say yes before running it.
    2. From my project root (the folder with the .uproject file), run:
       python Plugins/NightwardMCP/Content/Python/bootstrap.py --client <your client name, e.g. claude_code>
       (use the Install-to-Engine path instead if that's how I installed it)
    3. Tell me GREEN (done) or RED (show me the exact fix it printed) — don't guess past a RED.
    4. If it's GREEN, ask if I want a quick product walkthrough.

    Full technical detail if you need it: https://github.com/bbabines/Nightward-Documentation/blob/main/05-for-ai-agents/02-setup-protocol.md

## Prefer to do it by hand, or don't have an agent yet?

See [Install](../01-getting-started/02-install.md) — the full manual walkthrough (connect a client, verify, first visible win) and how
to update an existing install.

## Something not working?

See [Troubleshooting](../01-getting-started/04-troubleshooting.md) — your agent can usually diagnose and fix it directly.

Questions? Join the community: **Nightward MCP Discord** — https://discord.gg/Zqdvqx3XjE
(`#support` channel — run `export_support_bundle` first and attach the zip).
