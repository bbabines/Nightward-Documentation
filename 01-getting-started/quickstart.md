# Quickstart

**What you need:** the plugin installed (see [Install](../01-getting-started/install.md)), and one AI coding agent already on your
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

    Full technical detail if you need it: https://github.com/bbabines/Nightward-Documentation/blob/main/05-for-ai-agents/setup-protocol.md

## Prefer to do it by hand, or don't have an agent yet?

### Step 1 — Install

Epic Games Launcher, **Install to Engine** for the UE version you use (or the project-relative drop
if you prefer). Full steps: [Install](../01-getting-started/install.md).

### Step 2 — Connect a client

One command from your project root: `python bootstrap.py --project-path "<YourProject>.uproject"
--client <name>`. It installs deps, registers your client, launches the editor, and prints GREEN or
a specific RED with the fix. Full technical detail (registration internals, manual fallback):
[Agent Setup Protocol](../05-for-ai-agents/setup-protocol.md) — written for an agent, but every step is a plain command you can run
yourself too.

### Step 3 — Verify: `doctor`, then `verify_setup`

Ask your agent to call `doctor` first — the liveness check. It raw-socket pings the editor listener
and reports each named check green/red with a one-line fix (`plugin_not_loaded`, `port_in_use`,
`bridge_unreachable`, `stale_log_confusion`, `settings_missing`).

`verify_setup` is **not** a liveness check — it reads your config posture only and never contacts
the editor. Run it after `doctor` is green to confirm your operating profile (defaults to `solo`)
and see each behavioral knob's value.

### Step 4 — First visible win

Ask your agent to:

1. Call `spawn_actor` — a plain StaticMeshActor (e.g. a cube) at a clearly visible location.
2. Read the transform `spawn_actor` returns — it echoes the actor's actual post-spawn state, so the
   mutation is its own read-after-write proof.
3. Call `take_screenshot` so you can see the actor in the viewport.

That's the whole product in miniature: a mutation, a genuine read-after-write readback, then visual
proof. When done, ask your agent to `delete_actor` to clean up — fully reversible (deletes need
`confirm=true`, a consent gate, not a bug). Once you start Play-In-Editor, `get_actor_properties`
gives the same live-transform readback for a running actor.

## Something not working?

See [Troubleshooting](../01-getting-started/troubleshooting.md) — ordered by how often each cause is the real one, from "client can't
connect" through the exact `doctor` failure signatures. Or ask your agent to run
`export_support_bundle`: a redacted, local-only diagnostic zip to attach when asking for help.

## Updating an existing install

An in-place update needs the same client restart as a first install — your MCP client loaded the
old server code once at launch and won't notice the files changed under it. Confirm you're on the
new version: compare the updated `.uplugin` `VersionName` against `server_meta.plugin_version` on
any tool response. If they don't match, fully quit and reopen the client (not just reload).

Questions? Join the community: **Nightward MCP Discord** — https://discord.gg/rTwHxcGnHg
(`#support` channel — run `export_support_bundle` and attach the zip).
