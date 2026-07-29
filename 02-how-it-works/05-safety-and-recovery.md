# Safety & Recovery

Most of what your agent does day to day is automatically protected by the editor's own undo, no
setup required. Three mechanisms layer together to cover the rest — this page is the skim version:
what exists, when to reach for it, how to trigger it. For exact command lists, edge cases, and a
step-by-step recovery recipe, see `safety-and-recovery-reference.md` in the plugin's shipped docs
(`docs/product-docs/guides/`).

## The three mechanisms

| | Native editor undo | Mutation-journal revert | Checkpoint / restore |
|---|---|---|---|
| **What it is** | Unreal's own undo stack — the same as Ctrl+Z | A logged changelog entry, replayed in reverse | A binary snapshot of the asset file, outside the undo stack |
| **Use it when** | You want to step back through recent Blueprint/UMG/material/Niagara/PCG edits | You want to undo one specific mutation without touching anything after it | You're about to touch anything else — any asset type, any domain |
| **How to trigger** | `undo_last(count)` | `revert_mutation({seq: mcp_seq})` — `mcp_seq` is on the original mutation's own response | `checkpoint()` before the risky edit, then `restore_checkpoint(checkpoint_id, confirm=true)` if needed |
| **Setup needed** | None — automatic for ~130 eligible commands | None to log; reverting is a call you make | Call `checkpoint()` yourself first — except `delete_asset`/`rename_asset`/`move_asset`, which auto-checkpoint |

## Which one applies to me?

- **Editing a Blueprint graph, UMG widget, material, Niagara system, PCG graph, or animation/montage?**
  You're almost certainly covered automatically by native undo — no setup needed.
- **Want to undo just one edit without losing everything after it?** `revert_mutation` is the
  surgical option, for the commands it covers.
- **Doing anything else** — StateTree, Behavior Tree, Sequencer, GAS, Landscape sculpting, Foliage,
  Mesh Editing, MetaSound, Dataflow/Sim, or deleting/renaming/moving an asset — **call `checkpoint()`
  first.** That's the only mechanism that reaches those domains.
- **If your agent breaks something and you're not sure which of the above applies:**
  `safety-and-recovery-reference.md` has a step-by-step recovery recipe.

## What has no safety net at all

Anything outside the cases above, with no `checkpoint()` taken beforehand, is not recoverable — say
so plainly rather than guessing at a fix. See `safety-and-recovery-reference.md` for the exact
boundary.

## See also

- `safety-and-recovery-reference.md` (shipped alongside this page) — exact command lists, the
  recovery recipe, and every edge case.
- `catalog/bp-author.md` / `catalog/restore.md` — tool signatures.
- `configuration.md` — `destructive_ops` / `checkpoint_cadence` knobs.
- `faq.md` / `gaps-ledger.md`

*Synced from repo 3ed8353 - 2026-07-29*
