# Quickstart

Install the plugin, connect your MCP client, run `doctor` green, then make one visible edit and see
the real readback — about five minutes, no repo knowledge required.

Prefer to skip the manual steps? Hand this quickstart to your coding agent and it can run the whole
path itself (one command does the heavy lifting) — you only restart the client and approve the
editor launch. All five supported clients can run this path; only a cloud/browser session with no
local shell needs to follow the steps by hand.

## Step 1 — Install

Epic Games Launcher, **Install to Engine** for the UE version you use (or the project-relative drop
if you prefer). Full steps: **Install**.

## Step 2 — Connect a client

One command from your project root: `python bootstrap.py --project-path "<YourProject>.uproject"
--client <name>`. It installs deps, registers your client, launches the editor, and prints GREEN or
a specific RED with the fix. Full steps and per-client detail: **Client Setup**.

## Step 3 — Verify: `doctor`, then `verify_setup`

Ask your agent to call `doctor` first — the liveness check. It raw-socket pings the editor listener
and reports each named check green/red with a one-line fix (`plugin_not_loaded`, `port_in_use`,
`bridge_unreachable`, `stale_log_confusion`, `settings_missing`).

`verify_setup` is **not** a liveness check — it reads your config posture only and never contacts
the editor. Run it after `doctor` is green to confirm your operating profile (defaults to `solo`)
and see each behavioral knob's value.

## Step 4 — First visible win

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

See **Troubleshooting** — ordered by how often each cause is the real one, from "client can't
connect" through the exact `doctor` failure signatures. Or ask your agent to run
`export_support_bundle`: a redacted, local-only diagnostic zip to attach when asking for help.

## Updating an existing install

An in-place update needs the same client restart as a first install — your MCP client loaded the
old server code once at launch and won't notice the files changed under it. Confirm you're on the
new version: compare the updated `.uplugin` `VersionName` against `server_meta.plugin_version` on
any tool response. If they don't match, fully quit and reopen the client (not just reload).

Questions? Join the community: **Nightward MCP Discord** — https://discord.gg/rTwHxcGnHg
(`#support` channel — run `export_support_bundle` and attach the zip).
