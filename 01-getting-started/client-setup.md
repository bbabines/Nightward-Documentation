# Client Setup

One command connects your agent to the editor (requires Python 3 on PATH — the bridge itself
depends on it anyway). From your project root (the folder holding your `.uproject`), have your
agent run:

    python "<Engine>\Plugins\Marketplace\NightwardMCP\bootstrap.py" --project-path "<YourProject>.uproject" --client <name>

Used the project-relative drop instead of Install to Engine? Drop `--project-path` — it auto-resolves:

    python .\Plugins\NightwardMCP\bootstrap.py --client <name>

`<name>` is one of `claude_code` (default), `cursor`, `cline`, `codex`, `claude_desktop`.

## What that one command does

Nothing in this plugin ever runs automatically on install or on editor startup. Running the
command above is a deliberate action you take, but it does chain the following steps with no
further prompts — full disclosure of what that one run does:

1. Adds NightwardMCP to your project's `.uproject` Plugins array (idempotent — a no-op if it's
   already there). You never hand-edit that file.
2. Detects `uv` (a Python package manager). If it's missing, **downloads and runs the official uv
   installer from astral.sh** (PowerShell on Windows, `curl | sh` on Mac/Linux) — the only step
   that pulls and executes something from the internet on its own. Then runs `uv sync` to install
   the Python bridge's own dependencies.
3. Registers your client — see "How registration works" below.
4. Copies the included skills/slash-commands into your project's `.claude/` folder (default-on;
   skip with `--no-skills` on `install.py`).
5. Launches your editor clean on the resolved MCP port, past any crash-recovery modal.
6. Runs `doctor` plus a smoke probe and prints **GREEN** with the single next step, or a specific
   **RED** with the exact fix.

Config only, no editor launch? `python install.py --client <name>` runs just the client-registration
step. Want to configure the editor yourself and skip the launch? Pass `--no-launch` to
`bootstrap.py`.

## How registration works, per client

- **Claude Code, Codex** — register directly through their own CLI (`claude mcp add` /
  `codex mcp add`). Nothing to hand-edit; just restart the client.
- **Cursor, Cline, Claude Desktop** — no reliable registration CLI exists for these clients, so a
  reference config is written next to where your client expects one (`*.sample`), and your agent
  walks you through that client's own MCP-settings UI panel instead. You never hand-edit the file
  yourself.

Fully manual, no script: `uv sync` in `Plugins/NightwardMCP/Python`, then ask your agent to run
`generate_agent_config` for your client and paste the returned block into your client's MCP
config. All five clients are verified working with this config shape.

## Have your agent walk you through the product

**Claude Code:** once connected, run **`/explain`**. **Every client:** ask your agent to run
`generate_agent_config` (mode `auto`) — its `instructions_install` list names the exact
destination and install step for the same walkthrough in your client's own instruction format
(a `.cursor/rules/` file for Cursor, `.clinerules/` for Cline, an `AGENTS.md` append for Codex),
so the tour reaches every client, not just Claude Code. Either way it covers what you bought, the
tool families now at your disposal, a first hands-on win, and a short Q&A to pick the
safety/config profile that fits how you work.

## After a GREEN result

Confirm the server is listed once you restart the client (Claude Code: `/mcp`; other clients show
their own MCP server list in settings).

Next: [Quickstart](../01-getting-started/quickstart.md) — verify the connection and make your first visible edit.
