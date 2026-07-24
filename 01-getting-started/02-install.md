# Install

Two ways to get the plugin into an engine. Pick one — both are fully supported.

## Install to Engine (recommended)

In the Epic Games Launcher, find NightwardMCP under your Fab library and choose **Install to
Engine** for the UE version you use. It lands at `<Engine>/Plugins/Marketplace/NightwardMCP/` —
shared across every project on that engine version, so you do this **once per engine version**,
not once per project. No `Plugins/` folder to create, no files to drop into your project, nothing
to rename.

## Project-relative drop (alternative)

Prefer a self-contained per-project copy, or on an engine version that predates engine-level
Marketplace support? Drop the plugin folder into your project's `Plugins/` directory (create it if
it doesn't exist), so the `.uplugin` file lives at
`<ProjectRoot>/Plugins/NightwardMCP/NightwardMCP.uplugin`. This layout auto-enables with no
`.uproject` edit needed. Placement reference: Epic's
[Working with Plugins](https://dev.epicgames.com/documentation/en-us/unreal-engine/working-with-plugins-in-unreal-engine).

## Either way

The Fab download is a precompiled binary per engine version — it just loads, no C++ toolchain
needed. Only a source checkout or a custom engine build prompts to build modules; allow it if
asked. Confirm the plugin is enabled any time: **Edit > Plugins**, search "Nightward" — the next
step ([Quickstart](../01-getting-started/03-quickstart.md)) adds it to your project's `.uproject` for you either way, so you don't need
to tick this yourself first.

## Requirements

- Unreal Engine 5.4–5.8, Windows (Win64) editor. Newer engines expose more tools; 5.8 is the full
  product.
- One MCP-spec client: Claude Code, Cursor, Cline, Codex, or Claude Desktop.
- **Python installed on your computer.** The setup step is itself a small Python program, so
  Python has to already be there to run it — the plugin can't install its own prerequisite. If
  you're not sure whether you have it, don't worry: your agent checks for you as the first thing
  it does, and offers to install it if it's missing (explaining what it's about to do first). See
  [Agent Setup Protocol](../05-for-ai-agents/02-setup-protocol.md) for exactly what "installs `uv`" (a helper tool) means if you're curious.

Next: [Quickstart](../01-getting-started/03-quickstart.md) — connect your agent to the editor with one command.

## Manual setup, step by step

For readers doing this without an agent, or who want every step spelled out. Prefer to just paste
a prompt and let your agent handle it? See [Quickstart](../01-getting-started/03-quickstart.md) instead.

### Connect a client

One command from your project root: `python bootstrap.py --project-path "<YourProject>.uproject"
--client <name>`. It installs deps, registers your client, launches the editor, and prints GREEN or
a specific RED with the fix. Full technical detail (registration internals, manual fallback):
[Agent Setup Protocol](../05-for-ai-agents/02-setup-protocol.md) — written for an agent, but every step is a plain command you can run
yourself too.

### Verify: `doctor`, then `verify_setup`

Ask your agent to call `doctor` first — the liveness check. It raw-socket pings the editor listener
and reports each named check green/red with a one-line fix (`plugin_not_loaded`, `port_in_use`,
`bridge_unreachable`, `stale_log_confusion`, `settings_missing`).

`verify_setup` is **not** a liveness check — it reads your config posture only and never contacts
the editor. Run it after `doctor` is green to confirm your operating profile (defaults to `solo`)
and see each behavioral knob's value.

### First visible win

Ask your agent to:

1. Call `spawn_actor` — a plain StaticMeshActor (e.g. a cube) at a clearly visible location.
2. Read the transform `spawn_actor` returns — it echoes the actor's actual post-spawn state, so the
   mutation is its own read-after-write proof.
3. Call `take_screenshot` so you can see the actor in the viewport.

That's the whole product in miniature: a mutation, a genuine read-after-write readback, then visual
proof. When done, ask your agent to `delete_actor` to clean up — fully reversible (deletes need
`confirm=true`, a consent gate, not a bug). Once you start Play-In-Editor, `get_actor_properties`
gives the same live-transform readback for a running actor.

## Updating an existing install

An in-place update needs the same client restart as a first install — your MCP client loaded the
old server code once at launch and won't notice the files changed under it. Confirm you're on the
new version: compare the updated `.uplugin` `VersionName` against `server_meta.plugin_version` on
any tool response. If they don't match, fully quit and reopen the client (not just reload).

Questions? Join the community: **Nightward MCP Discord** — https://discord.gg/rTwHxcGnHg
(`#support` channel — run `export_support_bundle` and attach the zip).
