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
step ([Client Setup](../01-getting-started/client-setup.md)) adds it to your project's `.uproject` for you either way, so you don't need
to tick this yourself first.

## Requirements

- Unreal Engine 5.4–5.8, Windows (Win64) editor. Newer engines expose more tools; 5.8 is the full
  product.
- One MCP-spec client: Claude Code, Cursor, Cline, Codex, or Claude Desktop.
- **Python 3 installed and on PATH.** The plugin does not install Python for you — the setup
  script itself is a Python script, so this has to already be there. (Setup *does* auto-install
  `uv`, a separate Python package manager, if it's missing — see [Client Setup](../01-getting-started/client-setup.md).)

Next: [Client Setup](../01-getting-started/client-setup.md) — connect your agent to the editor with one command.
