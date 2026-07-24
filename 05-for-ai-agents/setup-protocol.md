# Agent Setup Protocol

For AI agents executing setup on a human's behalf, after they've pasted the prompt from
[Quickstart](../01-getting-started/quickstart.md). Written as a runbook for an agent to follow, not marketing copy for a human to
read — if you're the human, [Quickstart](../01-getting-started/quickstart.md) is the page you want.

## Step 0 — Python check

Run `python --version` or `python3 --version`. If neither resolves, Python is not installed.
Tell the human exactly what you're about to run and wait for them to say yes before running it —
installing a language runtime system-wide is a bigger action than anything else in this flow:

- Windows: `winget install Python.Python.3.12`
- macOS: `brew install python@3.12`
- Linux: your distro's package manager, e.g. `apt install python3`

Do not proceed to Step 1 until `python`/`python3` resolves.

## Step 1 — Run bootstrap

From the project root (the folder holding the `.uproject`):

    python Plugins/NightwardMCP/Content/Python/bootstrap.py --project-path "<Project>.uproject" --client <name>

`<name>` is one of `claude_code` (default), `cursor`, `cline`, `codex`, `claude_desktop`. Drop
`--project-path` if you're already running from inside the project root — it auto-resolves. Used
an Install-to-Engine copy instead of a project-relative drop? The script lives at
`<Engine>/Plugins/Marketplace/NightwardMCP/Content/Python/bootstrap.py`.

This one command auto-chains the rest with no further prompts needed from you: enables the plugin
in the `.uproject`, installs `uv` if missing (downloads the official installer from astral.sh —
this one step runs silently, unlike Python above, since it's scoped to this bridge's own venv, not
the system), syncs the bridge's Python deps, registers your client (see below), copies the
skills/commands kit into `.claude/` (default-on), relaunches the editor clean past any
crash-recovery modal, then runs `doctor` plus a smoke probe.

## Step 2 — Read the result

**GREEN:** tell the human setup is done and ask if they want the product walkthrough (Step 4).
**RED:** show them the exact fix `bootstrap.py` printed — it names the fix; don't guess past it.

Config only, no editor launch? `python install.py --client <name>` runs just the
client-registration step. Want the editor launch skipped some other way? `--no-launch` on
`bootstrap.py`.

## How registration works, per client

- **Claude Code, Codex** — register directly through their own CLI (`claude mcp add` /
  `codex mcp add`). Nothing to hand-edit; just restart the client.
- **Cursor, Cline, Claude Desktop** — no reliable registration CLI exists for these clients, so a
  reference config is written next to where the client expects one (`*.sample`), and you walk the
  human through that client's own MCP-settings UI panel instead. Never hand-edit the file yourself.

Fully manual, no script: `uv sync` in `Plugins/NightwardMCP/Python`, then run
`generate_agent_config` for the client and paste the returned block into the client's MCP config.
All five clients are verified working with this config shape.

## Step 3 — Confirm connection

Confirm the server is listed once the client restarts (Claude Code: `/mcp`; other clients show
their own MCP server list in settings).

## Step 4 — Product walkthrough (if the human wants it)

**Claude Code:** run `/explain`. **Every client:** run `generate_agent_config` (mode `auto`) — its
`instructions_install` list names the exact destination and install step for the same walkthrough
in the client's own instruction format (a `.cursor/rules/` file for Cursor, `.clinerules/` for
Cline, an `AGENTS.md` append for Codex). Either way it covers what the human bought, the tool
families now available, a first hands-on win, and a short Q&A to pick the safety/config profile
that fits how they work.

## Something not working?

See [Troubleshooting](../01-getting-started/troubleshooting.md) — ordered by how often each cause is the real one. Or run
`export_support_bundle`: a redacted, local-only diagnostic zip to attach when asking for help.
