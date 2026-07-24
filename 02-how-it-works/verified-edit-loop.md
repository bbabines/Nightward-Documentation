# The Verified Edit Loop

Every edit this server makes runs the same closed loop: **mutate → read back → compile → PIE gate
→ regression gate → journaled revert.** This is the mechanism behind the word "verified" — not an
adjective, concrete steps you can watch in a returned payload.

## 1. One mutation at a time

Each tool call makes one change (add a node, set a property, connect two pins). Calls are not
batched blind — each is followed by its own check before the next is issued.

## 2. Read-after-write readback

Every mutating call returns the object's actual post-mutation state read back from the live
editor — not an assumed `"success": true`. A no-op or wrong-target edit shows up in the payload
immediately, not later.

## 3. Compile / build check

Blueprint-shaped mutations are followed by a compile check. A red compile means stop and fix
before continuing — never push more mutations onto a broken graph.

## 4. PIE is the acceptance gate

Declaring is not working. "Done" means the edit actually runs in Play-In-Editor — not that a tool
call returned success.

## 5. The regression gate names the culprit

`regression_gate` runs your existing test/oracle suite after a risky change:

- **Green** — the change is safe, continue.
- **Red** — the response names the suspect mutation(s) since the last green pass (via
  `list_mutations` + `diff_blueprint`), so you fix or revert the actual culprit instead of
  guessing.

`regression_gate` rides *your own* test harness — it does not invent tests.

## 6. Journaled revert

Every mutation lands in a journal with before/after state. `revert_mutation` rolls back a named
mutation; `undo_last` and change groups cover the common cases without hunting for the right ID.

## Why this loop, not just "call the tool"

A larger tool surface that silently no-ops is worse than a smaller one that proves itself. The
verified-edit loop is what turns a raw MCP tool call into a claim you can trust without
re-checking by hand.

## Tools this loop is built from

- `regression_gate` — runs the oracle suite, culprit-naming on red.
- `list_mutations` — the mutation journal since the last known-good point.
- `diff_blueprint` — names the exact added/removed/changed node between two states.
- `revert_mutation` / `undo_last` — rolls back a mutation from the journal.
- `instrument_blueprint_trace` — arms, triggers, and reads per-node hit counts in one call, the
  execution proof behind the PIE gate.

See [Honest Limits](../02-how-it-works/honest-limits.md) for what rides your own test harness rather than being invented for you.
