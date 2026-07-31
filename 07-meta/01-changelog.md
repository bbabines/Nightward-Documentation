# Changelog - Nightward MCP

All notable changes to Nightward MCP, newest first. Dates are Fab release dates. One entry per shipped
version; the release process that maintains this file is in the product docs (post-launch release runbook).

## Updating an existing install

Copying a new version's files over an old install requires restarting BOTH long-running processes that
cache the old code independently - your MCP client's Python bridge subprocess, and (if the DLL changed)
the Unreal Editor itself. Neither notices the files changed under it and keeps serving its OLD code with
no error or warning until it restarts.

Run `doctor` after any update - its `version_drift` check compares the freshly-copied
`NightwardMCP.uplugin` `VersionName` on disk against both `server_meta.bridge_version` (the client's
Python subprocess, cached at its own startup - always present, even on a connection failure) and
`server_meta.plugin_version` (the live editor - present whenever the call reached it and got a
readable reply, absent if the call never reached it at all, e.g. a connection failure), and names
which one is stale: an editor mismatch means restart the Unreal Editor, a bridge mismatch means fully
quit and relaunch your MCP client. A match on one does not imply a match on the other - `doctor`
checks both so you don't have to diff them by hand.

## v1.0 - Initial release (release date TBD - fill at Fab publish)

First public release. An agentic editing system for Unreal Engine 5.4-5.8 (Win64): a verification
harness around the UE + LLM workflow - every mutation is read back from the live editor, diffed,
compiled, and gated through Play-In-Editor before it counts as done, with a regression gate that names
the culprit and reverts it. Works with Claude Code, Cursor, Cline, Codex, and Claude Desktop.

- Fixed a shipped FAQ dev-path leak (reworded the FAQ header note).
- Fixed `install.py` raising a raw traceback on a BOM'd `.uproject` file.
