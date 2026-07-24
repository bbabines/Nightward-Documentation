# Troubleshooting Protocol

For AI agents diagnosing a setup or connection problem on a human's behalf. Written as a runbook,
not marketing copy for a human to read — if you're the human, [Troubleshooting](../01-getting-started/04-troubleshooting.md) is the page you
want (it tells you to point your agent here).

Most first-run problems are one of a handful of setup issues, not a bug in your edit. Work top to
bottom - the list is ordered by how often each one is the real cause.

## Failure modes (most common first)

### 1. Client can't connect / no tools listed

The client shows Nightward as disconnected, or its tool list is empty.

- **The editor is closed, or the plugin isn't loaded.** The bridge can only reach a listener inside a
  running editor with the plugin enabled. Open the project in Unreal Engine (5.4-5.8), confirm the
  plugin is enabled (Edit > Plugins, search "Nightward"), then reconnect the client.
- **The bridge process didn't start, or the client didn't re-read its config.** Restart the client so
  it re-registers the server, then re-check its MCP/server list (Claude Code: `/mcp`).
- Ask your agent to call `doctor` before doing anything else - it raw-socket pings the listener and
  names the exact failure: `plugin_not_loaded` (port refuses / editor not running), `port_in_use`
  (something else owns the port), `bridge_unreachable` (connected but the server thread is hung),
  `stale_log_confusion`, or `settings_missing`. It also prints the `host:port` it probed.

### 2. Wrong port

The bridge starts but never reaches the editor, even with the editor open and the plugin loaded.

- The editor listens on TCP **55557** by default. If the bridge is pointed at a different port, every
  call times out with nothing arriving in the editor.
- `doctor` prints the exact `host:port` it probed - compare that against
  `Config/DefaultEditor.ini`'s `ServerPort` (under
  `[/Script/NightwardMCP.NightwardMCPProjectSettings]`) or an `UNREAL_MCP_PORT` env var override in
  your client's MCP entry. Align them, restart the bridge, re-run `doctor`.

### 3. Stale deferred schema (harness-side, not a Nightward bug)

A tool call fails validation with something like a required field missing, even though your call
looks correct - typically after a tool was added, renamed, or its parameters changed.

- This is the client's cached tool schema going stale, not a code gap in the tool. The symptom is a
  parameter/shape error, not a connection error.
- Reconnect the client (in Claude Code, re-run `/mcp`) so it re-fetches the current tool schemas,
  then retry the call. Only a genuinely new or renamed command needs the fresh reconnect; a
  same-shape tool does not.
- **Do NOT** conclude "the tool is broken / missing" from a single validation error. Re-fetch the
  schema and retry first - a stale schema is far more common than a real gap.

### 4. Hallucinated UE API

The agent calls a tool with a class, node, function, property, or asset path that does not exist in
your project or engine version, and gets a not-found / unresolved error.

- The tool is working; the argument is invented. Agents confidently guess UE symbol names that look
  plausible but aren't real in UE 5.8 or in your project.
- Resolve the real symbol before mutating: read the actual Blueprint/asset, or use the reflection
  lookup, and feed the exact name back into the call. Read-after-write readback then confirms the
  edit hit the intended target.
- **Do NOT** retry the same guessed name in a loop. Each retry burns tokens and editor round-trips
  without changing the result. Look up the real name once, then call.

### 5. `os error 3` / `ENOENT` - MCP server won't spawn

The config points at a directory or interpreter that no longer exists - the bridge cannot self-report
this, since the missing files are its own.

- **You moved or renamed the plugin folder** - the hardcoded
  `--directory .../NightwardMCP/Content/Python` in your client config is stale.
- **You uninstalled the plugin** - that directory is gone.
- **`python`/`uv` was removed after a working install.**
- Fix for all three: re-run `python bootstrap.py` (or `python install.py`) to reinstall `uv`/deps
  and re-register (CLI clients update in place; others re-merge the fresh `.sample`), restart the
  client.

### 6. Desktop/global config shadowing the project config

You edited the project's MCP config, reconnected, and still get the old server, the old command, or
the old directory.

- A client's global/desktop-level MCP config can override the project-local one. The client silently
  uses the global entry, so your project edits appear to do nothing.
- Check the client's global config (for Claude Desktop, `claude_desktop_config.json`) for a
  Nightward/`unreal-mcp` entry and confirm its command and `--directory` point where you expect.
  Fix or remove the shadowing entry, then reconnect.

### 7. Protocol version mismatch (updated one half but not the other)

The bridge fails to start, or the client shows the server erroring, with a message like
`Protocol MISMATCH: the Unreal editor plugin is OUT OF DATE ...` or `... the Python bridge is OUT OF
DATE ...`.

- After an update, the C++ editor plugin and the Python bridge are speaking different wire-protocol
  versions - you updated one half but not the other. The connect-time handshake catches this on
  purpose, so a skew shows a clear "update X" message instead of surfacing as confusing downstream
  breakage mid-session.
- The message names the stale side. Update whichever it says - rebuild/reinstall the editor plugin,
  or update the Python bridge - so both are on the same release, then reconnect. (A same-version
  update needs the client restart covered in [Install](../01-getting-started/02-install.md) "Updating an existing install," not this
  fix.)

### 8. Plugin fails to load on UE 5.8 (`GetLastError=126`)

On UE 5.8 the outfit tools link the engine's `ChaosOutfitAsset` plugin. If a dependent engine module
isn't on the loader path, enable the **ChaosOutfitAsset** engine plugin (Edit > Plugins, tick it on)
and restart - shipped 5.8 binaries load it automatically, so this mainly applies to source builds or
edge hosts. UE 5.4-5.7 do NOT link this module (the outfit tools are gated to 5.8+ because
`ChaosOutfitAsset` is Experimental and does not load cleanly there), so they are unaffected.

### 9. `Outfit commands require UE 5.8+` on a call that has nothing to do with outfits

On UE 5.4 and 5.5 only. A dispatch bug mis-routes Animation state-machine authoring, blendspace
authoring and frame-timing capture to the outfit tools' refusal string.

- The tool you called is NOT version-gated - it is verified working on 5.6, 5.7 and 5.8. A fix is in
  progress.
- **Do NOT** rewrite the call, hunt for a different tool, or tell the human the feature is missing.
  The error names a feature you never invoked.
- On 5.4/5.5 those specific assets have to be authored by hand for now; everything else on the same
  engine is unaffected.

### 10. Editor crashed / PIE wedged / "Restore Packages" modal

An auto-save crash-recovery modal can hold the game thread (0x0 viewport, `start_pie` hangs on
`play_request_pending`, tools return a bare `NEEDS_RESTART`). Run
`python Plugins/NightwardMCP/Content/Python/tools/recover_editor.py` - it kills the wedged editor, clears the
crash-recovery manifest, relaunches clean, and waits for the listener.

## Anti-patterns (do NOT do these)

- **Do NOT trust a bare success without reading the payload.** Nightward returns the live post-state
  for a reason - read it. A returned "success" without the post-state is not the same evidence.
- **Do NOT run parallel graph mutations against the same editor.** Overlapping mutation calls are
  unsupported and can crash the editor - this is an engine-level constraint, industry-wide, not a
  Nightward limit. Drive one mutation at a time; one agent at a time.
- **Do NOT retry a failed call blindly.** Diagnose which failure mode above it is and fix that - a
  blind retry loop wastes tokens and editor round-trips.
- **Do NOT assume a timeout means the write didn't happen.** A response-side timeout can fire on a
  mutation that actually committed. Read the state back to confirm before you re-issue the write.
- **Do NOT keep editing after a stop condition.** If a step fails twice, or a readback contradicts
  intent and the cause is unclear, stop, capture the verified state and the exact error, and
  diagnose - don't pile more mutations on an unknown state.

## When you're still stuck: `export_support_bundle`

Before you write up a support request, run `export_support_bundle`. It assembles a redacted,
local-only diagnostic zip - the call log, a mutation slice, a log tail, version info, and a latency
snapshot - so the support conversation starts with real data instead of a screenshot and a
back-and-forth.

Written locally and redacted; the human attaches it (or the relevant part) themselves - nothing is
transmitted automatically. Attaching it is the fastest way to a real answer.

> Support is a community channel (Discord/forum) plus best-effort email - no per-ticket SLA. The
> bundle is what makes best-effort support fast: it removes the round-trip of "can you send me your
> setup?"
