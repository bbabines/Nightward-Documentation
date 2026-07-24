# Gaps ledger - honest known limits

A living record of confirmed limits, kept because a visible limits section is engineering
credibility for a dev tool, not a liability. Entries here agree with `faq.md`'s "Honest limits"
section and this doc set's "What this is NOT" statements - if you spot a contradiction across the
three, treat it as a doc bug and check the newest one.

Status key: **DEFERRED** = a real, known limit, not currently planned to close. **CAN'T FIX** = an
engine or industry-wide architecture constraint, not fixable by this plugin at all.

## Deferred

### Bridge round-trip latency floor

Per-call latency in a hidden/background editor settles around ~300-330ms per call (engine-tick
bound, not TCP handshake). A persistent-connection mode exists as an opt-in for advanced setups,
but does not materially beat the per-call floor in this state, so per-call stays the default.
Practical effect: shape work around composite tools and fewer round trips rather than expecting
sub-300ms polling.

**Remedy for long/latency-sensitive runs:** most of that floor is Unreal's own not-foreground CPU
throttle, not the bridge. A backgrounded editor ticks at ~3 FPS (one game-thread frame per ~333ms);
foregrounding the window, or unchecking **Editor Preferences → Performance → "Use Less CPU when in
Background"**, restores full tick and drops the floor roughly 3.3x (to ~100ms). Unchecking the
setting is best for unattended/headless runs (full tick while backgrounded); the ~100ms foreground
floor is still a real per-call cost, so still prefer composites and batching for heavy loops.

### GAS needs a host-provided ability system

The GAS tools operate on an ability system that already exists in your project - there is no tool to
create an AbilitySystemComponent pawn or register a gameplay tag at runtime. An AttributeSet class can
be scaffolded with the `create_attribute_set_class` codegen tool (it writes C++ that needs an editor
build), but the runtime wiring is yours: bring an ASC-equipped pawn (register any tags via
`DefaultGameplayTags.ini`); the GAS tools then grant / activate / apply gameplay effects against it.
Covered per-tool on the `gas` catalog page.

### An empty Experimental sim asset can pass in-session then crash on reload

Some `create_*_asset` tools for Experimental Chaos / HairStrands sim types (groom / flesh / cloth /
outfit) will create and read back fine in one session, then **crash the editor on the next reload**
if the empty asset violates a load-time invariant (e.g. an empty groom asserts on zero hair groups
at load). It is per-asset-type — some empty sim assets reload fine, some do not. If you author one of
these via the MCP, the load-bearing check is **create → save → restart the editor → read back**;
an in-session create+readback alone does not prove the asset survives a reload.

### No comment/organize tool outside Blueprint graphs

The `graph-readability` extra's comment-box tooling (`add_node node_type="Comment"`,
`set_node_property`) targets `UEdGraph` - Blueprint EventGraphs, function graphs, and AnimBP graphs
only. Material, Niagara, and PCG graphs have no comment or layout tool through the MCP; organize
those with node position alone.

## Can't fix

### A few C++ getters have no reflected function

Some plain inline C++ getters (no `UFUNCTION` reflection) cannot be wired directly via node
authoring - there is no reflected function for the tool to find. Workaround exists case-by-case
(e.g. reach the same data through a different reflected path); this is an engine reflection
constraint, not a bridge bug.

### A detached/relaunched editor can come up degraded

After certain relaunch paths, the editor can come up in a degraded state (blank world, zero-size
viewport) where Python calls silently no-op. Detectable via a state-readback tool before trusting
any further mutation in that session; recoverable with a normal editor relaunch.

### No concurrent multi-agent editing / parallel PIE sessions

Confirmed industry-wide, not specific to this plugin: no Unreal MCP server - including Epic's own
official embedded MCP server - supports two agents mutating Blueprints at once, or more than one
PIE session per editor instance. One editor process is one transaction/undo stack, one Blueprint
compiler, one PIE slot. The only proven workaround anywhere in the ecosystem is separate editor
processes per agent, coordinated externally - not something any MCP server manages internally.
Drive one agent against one editor instance at a time.

## Token / context cost

We shape tool responses (field selection, omit-empty, compact JSON, pagination) and measured a
~77% payload reduction on one real Blueprint read - a real, structural reduction, not a headline
percentage we'd stand behind as a universal law (the number that matters is the one your own
project produces). A catalog / tool-search dispatch mode (the "few thousand instead of tens of
thousands of tokens" pattern some competitors market) is honest roadmap, not shipped.

## What else does it not do

See `faq.md` "Honest limits" for the full buyer-facing list (asset import scope, source-control
submit, synchronous-only calls, no synthetic PIE input injection, PCG/Chaos scope, engine version
range) - kept in one place there to avoid the ledger and the FAQ drifting apart.

*Synced from repo e314734 - 2026-07-24*
