# Changelog - Nightward MCP

All notable changes to Nightward MCP, newest first. Dates are Fab release dates. One entry per shipped
version; the release process that maintains this file is in the product docs (post-launch release runbook).

## Updating an existing install

Copying a new version's files over an old install requires restarting your MCP client's connection,
same as a first install. Your client's connection is a long-running process that only loads the
server code once - it will keep serving the OLD version's tools with no error or warning until
restarted. Confirm you're actually running the new version by comparing the `VersionName` in the
updated `NightwardMCP.uplugin` against `server_meta.plugin_version` reported on any tool
response (success or error); a mismatch means the connection is stale.

## v1.0 - Initial release (release date TBD - fill at Fab publish)

First public release. An agentic editing system for Unreal Engine 5.4-5.8 (Win64): a verification
harness around the UE + LLM workflow - every mutation is read back from the live editor, diffed,
compiled, and gated through Play-In-Editor before it counts as done, with a regression gate that names
the culprit and reverts it. Works with Claude Code, Cursor, Cline, Codex, and Claude Desktop.
