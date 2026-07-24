# What's Included

Nightward drives Unreal Engine across the systems you actually build games with — 450+ tools across 42 groups,
spanning nine domains. You don't call the tools; your AI agent does. You describe what you want
built, and it works directly in the live editor.

Every domain below runs through the same verified pipeline — see [The Verified Edit Loop](../02-how-it-works/01-verified-edit-loop.md).

## Blueprints
Author, debug, and diff Blueprint logic without hand-wiring the graph.
- Nodes, variables, functions, components, structs and enums
- Breakpoints and passive execution traces
- Graph diffs and orphaned-node detection

## UI / UMG
Build the interface, not just mock it.
- HUDs, menus, and full widget trees
- Layout, styling, and live tree readback
- Event binding and text/data bindings

## Materials & Rendering
- Materials and material instances
- Expression graphs and instance parameters

## VFX & Audio
- Niagara particle systems — emitters, modules, renderers
- MetaSound audio graphs

## Animation
- Montages, notifies, and segments
- Blend spaces and Animation Blueprints
- Root motion

## Gameplay AI
Build the systems that make characters act.
- Behavior Trees with composites, tasks, and decorators, plus their Blackboards
- Environment Query System (EQS) queries for spatial reasoning
- StateTree logic for state-driven behavior
- Gameplay Ability System — abilities, effects, attributes, and runtime grant/activate against a live pawn

## Worlds
- Landscapes and paint layers
- Foliage placement and scatter
- Spline roads
- PCG (procedural) graphs
- Geometry editing — boolean, remesh, simplify, LODs, collision, UVs

## Cinematics
- Level Sequences
- Director Blueprints and event tracks

## UE 5.8 Simulation
- Chaos Cloth
- Parametric outfits and flesh
- Dataflow graphs

## Dev & Pipeline
- Asset management — rename, move, references, tags, textures
- Automation tests
- Unreal Insights profiling
- Source control
- Project-wide scanning and orphan audits

## Local Control Panel
A browser dashboard, separate from your agent: switch operating profiles, override config knobs,
and browse or restore checkpoints yourself. Runs on localhost — no editor needed to view it, no
account, no cloud round-trip.

---

Wide coverage is only half of it. The other half is that **every** edit above is applied, read back,
compiled, and PIE-tested before it's called done — see [The Verified Edit Loop](../02-how-it-works/01-verified-edit-loop.md) and [Honest Limits](../02-how-it-works/03-honest-limits.md).

*Synced from repo e314734 - 2026-07-24*
