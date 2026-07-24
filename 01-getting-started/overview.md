# Overview

**AI agents help you edit. Nightward proves it worked — with a safety net.**

Nightward is an agentic editing system for Unreal Engine 5.4–5.8 — a verification harness around the
UE + LLM workflow — whose transport is MCP. The MCP server is the doorway. The product is the closed
loop it runs on **every** edit.

Every rival tells you to watch it work. Nightward proves what it did while you didn't.

## What it actually is

A paid editor plugin + Python bridge that your AI coding agent drives — Claude Code, Cursor, Cline,
Codex, Claude Desktop, or any MCP-compatible client. You describe what you want built; the agent
works directly in your live editor. You don't call the tools.

Three layers:

1. **The tool surface** — 450+ tools across 42 groups, across the editor domains real work needs.
2. **The agentic runtime** — the closed loop that proves and reverts every edit.
3. **The agent kernel** — shipped know-how, distilled from the authors' own unattended production
   workflow on a real shipping game.

Layer 1 is what every AI-Unreal bridge has. Layers 2 and 3 are the product.

## The loop, in order

Not five features — one loop, run on every edit:

**Mutate → read back → diff → compile → PIE gate → regression gate → journaled revert.**

- **Read back, don't assume.** Every mutation returns the actual post-state read from the live
  editor, not an "OK". Silent no-ops and wrong-target edits surface the moment they happen.
- **Diff + compile.** A diff against an exported baseline names the exact changed node; compiles
  return structured errors.
- **PIE is the acceptance gate.** Declaring is not working. "Done" means the edit actually runs.
- **The regression gate names the culprit.** On a red gate it identifies the suspect mutation since
  the last green and reverts to green — catch, name, revert, not blind undo.
- **Journaled revert.** Every mutation lands in a journal with before/after state, so any step can
  be rolled back by name.

See [The Verified Edit Loop](../02-how-it-works/verified-edit-loop.md) for how each stage works, and [Honest Limits](../02-how-it-works/honest-limits.md) for what it openly
does not do.

## Who it's for

Solo developers and small teams who want an agent doing real editor work without babysitting every
call. If you'd rather watch an agent work than read evidence of what it did, the free bridges cover
that. This is for when you want to walk away and still trust the result.

## Where to start

- [Quickstart](../01-getting-started/quickstart.md) — install, connect, and make your first verified edit.
- [What's Included](../02-how-it-works/whats-included.md) — the domains you can build in.
- [How It Compares](../02-how-it-works/how-it-compares.md) — positioning, pricing, and what it cedes to other servers.

*Synced from repo 473b165 - 2026-07-24*
