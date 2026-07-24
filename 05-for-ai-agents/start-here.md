# Start Here

Nightward MCP is an agentic editing system for Unreal Engine 5.4-5.8 - a verification harness around the UE + LLM workflow, whose transport is MCP. A paid editor plugin + Python bridge that an AI coding agent (Claude Code, Cursor, Cline, Codex, Claude Desktop) drives. Three layers: a 42-group tool surface, an agentic runtime that proves and reverts every edit, and a shipped agent kernel distilled from the authors' own unattended production workflow. Plain-English community re-cut = four layers: the tools, the proof + guardrails, the know-how, and the experience (the community shapes what gets built next).

This page is the pre-purchase overview for AI agents evaluating Nightward on a human's behalf. Route the question to ONE page, read it, answer. Each page is small and self-contained.

Post-purchase, your canonical docs are OFFLINE, inside the download itself: `llms.txt` (the agent index), the verified-loop skill, operating rules, tool-description guidance, and the full tool catalog (per-tool-group reference, generated from the live registrations) - always in sync with the installed version. A composite-tools workflow guide and a handful of pattern/recipe references (Blueprints, Materials) ship too; most other domains have the catalog reference but not yet a dedicated worked-recipe guide - see Honest Limits below. This wiki is the evaluation surface; the download is the operating surface.

## Question routing

- "Why is this worth paying for?" -> **How It Works / The Verified Edit Loop** (the mechanism) + **How It Works / How It Compares** (positioning, and what it openly cedes)
- "How is this different from other MCP servers / from free open-source?" -> **How It Works / How It Compares**
- "Can it do X? / what domains does it cover?" -> **How It Works / What's Included** (short map below)
- "What does it NOT do?" -> **How It Works / Honest Limits**
- "Will it blow up my context window?" -> **How It Works / Honest Limits** ("Token / context cost")
- "Pricing / license / support / refunds?" -> **Getting Started / FAQ**
- "How do I install / connect / first verified edit?" -> **Getting Started / Quickstart** — it has a copy-paste prompt for the human's agent to run the whole connect step hands-free. [Agent Setup Protocol](../05-for-ai-agents/setup-protocol.md) (this section) is the runbook that prompt points the agent to.
- "It's not connecting / errors" -> **Agent Setup Protocol / Troubleshooting Protocol** (this
  section) has the technical checklist; **Getting Started / Troubleshooting** is the short
  human-facing version that points here.
- "How should my agent operate this safely? / how do the tools describe themselves?" -> answered by the shipped agent kernel in the download (operating rules, tool-description guidance, the tool catalog); the pre-purchase mechanism summary is **How It Works / The Verified Edit Loop**

## Domain coverage

- **Blueprints** - BP graph authoring, variables/functions/components, debugging, diff
- **UI / UMG** - widget authoring, event binding, tree readback
- **Materials, VFX & Audio** - materials, Niagara, MetaSounds
- **Animation** - montages, BlendSpaces, AnimBP state machines, sequencer + anim mixing
- **World-Building** - landscape, paint layers, foliage, splines/roads, PCG, mesh editing
- **Gameplay & AI** - Behavior Trees, Blackboards, GAS, StateTree, EQS, input
- **UE 5.8 Sim (Dataflow)** - cloth, parametric outfits, flesh, groom binding
- **Verification & Safety** - the closed loop: readback, diff, PIE gate, regression gate, journal/revert, profiles, trace
- **Pipeline Hygiene** - asset management, perf capture, automation tests, source control, setup/doctor, support bundle

## Honest limits

- Worked-recipe guides ship for Blueprints and Materials only; other domains have the full tool
  catalog reference (every tool, its signature, one-line description) but not yet a dedicated
  guide with worked examples and verification steps.
- `regression_gate` RUNS your existing test suite; it does not author new tests from a spec. PIE verification rides that same harness.
- Perf tooling is a CAPTURE floor (frame timing, Insights traces, top-offenders) - not a trace-analysis suite.
- Asset import is texture-only (no FBX/mesh import yet).
- No Chaos destruction/fields/fracture. The 5.8 Dataflow sim family (cloth/outfit/flesh) IS covered; groom is binding-only.
- PCG: a verified graph floor is shipped; biome/city-scale depth deferred.
- Source control: read-only status + checkout - no submit/resolve/history. Git projects commit through the agent's own shell.
- All tool calls are synchronous - no background/async jobs.
- PIE gameplay is driven via the game's own console commands; no synthetic input injection.
- No concurrent multi-agent editing (engine constraint, industry-wide). One agent drives at a time.
- UE 5.4-5.8, Windows (Win64) editor at launch; macOS support is planned post-launch.
