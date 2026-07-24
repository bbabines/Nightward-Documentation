# How It Compares

## The choice you shouldn't have to make

A working Unreal dev shopping for an MCP server usually picks a side:

- **The verified, narrow option.** A server that is careful about what it changes — guardrails,
  undo, dry-run — but covers only a slice of the editor, so you outgrow it on real work.
- **The broad, blind option.** A server that exposes hundreds of tool actions across every domain —
  but hands the agent one more tool and hopes. When an edit silently no-ops or hits the wrong
  target, you find out later, by hand.

Nightward is built to be the best **blend** of the two, not a bigger version of either. Both sides
of that choice ship a *tool surface* (a bridge); Nightward ships the *system* that runs it — an
agentic editing system whose transport is MCP: a server-enforced closed loop on every edit
(readback → diff → compile → PIE gate → regression gate → journaled revert), operating profiles
enforced per-call in C++, self-diagnosis (`verify_setup` + `export_support_bundle`), and its own
agent kernel — the verified-loop skill.

- **Verified editing** — every mutation is read back from the live editor and returned as
  evidence, a regression gate names the mutation that broke a test, and PIE behavior is the
  acceptance gate.
- **Solid breadth** across the domains most real work needs (485 tools across 42 groups) — enough to do
  the job, deliberately not a count race.
- **A maintained product** — a maintenance cadence keeps it current with the engine, with free
  updates.
- **A one-time perpetual license**, priced below the breadth incumbents on purpose.

This is a **combination** claim, not a superset claim. Nightward does not have every tool. It
openly cedes several specialist domains — see below — and tells you which product to use instead.

**Relationship to open source, stated plainly.** Nightward began as a fork of an open-source
Unreal MCP (the flopperam/Aura lineage; MIT/permissive) and has since diverged substantially — the
verification loop, regression gate, execution trace, composite tools, and support bundle are our
own additions. The open-source base is good work and remains free; we are not disparaging it. What
you pay for is the verified loop on top, the maintenance cadence, and support — not the base tools
anyone can get for free.

## Why tool count is the wrong metric

The category races on raw tool counts. Nightward ships 485 tools across 42 groups (per the generated
`docs/agents/tool-api/INDEX.md`) and does not try to win that race, for three reasons:

1. **A larger tool that silently fails is worse than a smaller one that proves itself.** A count
   tells you how many operations exist, not how many return real evidence that the edit did what
   the agent intended. Breadth without verification just means more places for a silent no-op to
   hide.
2. **Breadth is already free.** MIT-licensed servers already set the floor at "broad and free," so
   a paid product justified by count alone competes with free.
3. **The most-trusted MCP servers in adjacent categories lead with "focused," not "maximal."**
   "Focused" reads as a virtue, not an apology.

None of this says the breadth incumbents are bad — where a competitor genuinely covers more, this
page says so below. Count is not the axis that tells you whether you can hand an agent real work
and trust the result. Depth of verification is.

## What this loop actually verifies (the mechanism, not the adjective)

1. **Read-after-write post-state.** Every mutation returns the affected object's actual state read
   back from the live editor — a no-op or wrong-target edit shows in the payload immediately.
2. **Culprit-naming regression gate.** `regression_gate` runs your existing test suite; on red,
   `list_mutations` + `diff_blueprint` isolate the suspect mutation since the last green pass and
   `revert_mutation` rolls it back.
3. **PIE as the acceptance gate.** "Done" means the edit actually runs in Play-In-Editor. Honest
   scope: this rides *your own* test harness — it does not invent tests (see [Honest Limits](../02-how-it-works/honest-limits.md)).
4. **Execution trace.** `instrument_blueprint_trace` arms, triggers, and reads per-node hit counts
   in one call; a mutation journal records every tool call.

## Where competitors are the better choice

If your work centers on any of the following, buy the tool that leads it — Nightward openly does
not cover these, and saying so is how you know the rest of this page is honest.

- **Biome-scale procedural generation.** Nightward ships a verified PCG graph floor
  (create/connect/execute/readback); it does not go to biome/city-scale procedural depth.
- **Chaos destruction, fields, fracture, and advanced GeometryScript modeling.** Nightward has
  basic mesh ops only; the UE 5.8 Dataflow simulation family (cloth, parametric outfits, flesh) is
  covered, but destruction/fields/fracture stay out of scope.
- **Control Rig, IK rigs, PoseSearch, or animation retargeting.** Nightward ships BlendSpace,
  AnimBP state-machine, and montage/notify floors; rigging and retargeting are specialist,
  feel-heavy authoring it does not cover.
- **Deep trace analysis through MCP.** Nightward's perf tooling is a capture floor (frame timing,
  Insights trace start/stop/bookmarks/regions, thread-level top-offenders), not an analysis suite.
- **Long-running async or background tasks with live progress.** Every Nightward tool call is
  synchronous.
- **Raw input injection during PIE** (synthetic key/mouse playtesting). Nightward drives gameplay
  through the game's own console commands (`exec_console_command`) and observes state, screenshots,
  and logs; it does not synthesize input events.
- **Engine-provider source-control workflows** (Perforce/SVN submit, resolve, history). Nightward
  is read-only status + checkout; git projects commit through the agent's own shell, outside MCP.
- **Networking or replication authoring/inspection.** Nightward does not author or inspect
  networking/replication; multiplayer is not the launch thesis.
- **C++ class authoring, live coding, or building the project from MCP.** Nightward ships
  engine/build diagnostics (reflection, module-dependency suggestions, build-error parsing) — it is
  an editor-automation product, not a C++ IDE.
- **A docked in-editor chat or onboarding panel.** Nightward has no in-editor UI by design — it
  works inside your existing agent client (Claude Code, Cursor, Cline, Codex, Claude Desktop), with
  `generate_agent_config` and a five-minute quickstart carrying onboarding.
- **Absolute maximum tool breadth for free.** If breadth-at-zero-cost is the whole requirement, a
  free/open-source server wins that comparison outright.

## Pricing

One-time perpetual license, free updates, full C++ source included, sold on Fab (Epic). $34.99
founder price at launch, rising to $49.99 once the first reviews land — the discount is off the
real $49.99, never an inflated list price.

## Summary

Not the biggest toolbox. The best combination of a server-enforced verification loop (readback,
diff/compile, PIE gate, culprit-naming regression gate, journaled revert) and solid core-domain
breadth, at a price below the incumbents — honest about what it cedes (see above), for a dev who
wants to hand an agent real editor work and trust what comes back.

*Synced from repo 473b165 - 2026-07-24*
