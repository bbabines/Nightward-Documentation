# FAQ

## Positioning & differentiation

### Is this just another MCP server?

No. An MCP server is a bridge - it exposes editor operations as tools and stops there. Nightward is
an **agentic editing system** whose transport is MCP: the product is the closed loop the system runs
on every edit - readback -> diff -> compile -> PIE gate -> regression gate -> journaled revert - not
the doorway the calls come through. That loop is server-enforced, not honor-system: operating
profiles gate destructive ops per-call in C++, the system diagnoses itself (`verify_setup` + the
support bundle), onboards itself (`generate_agent_config`), and ships its own verified-loop agent
kernel. Buy a bridge and you get tools; buy this and you get the harness that makes an agent safe
against them.

### What does "run unattended" actually mean?

It means the system runs the verified loop without you watching, and stops safely - not
fire-and-forget magic. Concretely: every edit is read back and diffed against intent, compiled, and
gated on PIE behavior; `regression_gate` runs your existing oracle suite after risky changes and on
red names the suspect mutations since the last green; the JSONL mutation journal means the culprit
is reverted, not guessed at. When the agent hits a stop condition (a call fails twice, a readback
contradicts intent, a needed tool is missing), it stops and reports verified state instead of
improvising. Honest scope: calls are synchronous, one agent drives at a time, there are no async
background jobs, and the gate rides your own tests. The unattended overnight pattern is how the
authors build their own shipping game with this exact loop - the loop we sell is the loop we run -
but that coordinator workflow is our production practice, not a shipped feature.

### Do I just get more tools, or does it also teach my agent how to use them?

Both. The plugin + tool surface is layer one — every AI-Unreal bridge has that. On top, it ships an
agent kernel: a skills kit and slash commands that install automatically (Claude Code today; other
clients get the same content delivered into their own instruction format via
`generate_agent_config`). It's the difference between "has the tools" and "knows how to drive them
well" — where logic belongs, how to verify its own work, when to stop and ask instead of guessing.
The kernel's actual content ships with the download, not on this wiki — a hard-won operating
playbook is exactly the kind of thing that's cheap to clone if we paste it here.

### How is this different from the breadth-focused tools?

They compete on how many operations exist; Nightward competes on whether each edit is proven. The
paid breadth servers ship *guardrails* - named undo transactions, dry-run mode, per-token scopes,
structured error feedback. Real and good, but a guardrail tells the agent what it *tried* to do.
**Read-after-write readback** tells it what actually happened: every mutation returns the live
post-state, not an "OK". Add the culprit-naming regression gate, PIE as the acceptance gate, and the
execution trace, and none of the breadth competitors advertises the combination.

The honest position: the best **combination** - verified editing + solid core-domain breadth + a
maintained product with free updates + slightly cheaper - not a superset. Where a competitor
genuinely covers more (see [How It Compares](../02-how-it-works/04-how-it-compares.md)), we say so and tell you to buy theirs.

### You ship 485 tools. Why does the headline say "450+"?

Because "450+" is a floor we can still honour after the next release, and 485
is a number that stops being true the moment a tool lands. We would rather under-state a claim you
can check than post a number that quietly rots. It is the same instinct as the rest of the product:
state what you can prove, and let the evidence be bigger than the promise.

The real figure, stated plainly: 450+ tools across 42 groups, per the generated
`docs/agents/tool-api/INDEX.md`, re-counted at publish. We don't inflate it, and we don't round it
up.

### Isn't a tool count the wrong thing to compare on anyway?

Yes - which is why we don't lead with it. Count tells you how many operations exist, not how many
return evidence that the edit landed. More unverified tools just means more places for a silent
no-op to hide. And breadth is already free: fully open-source servers ship 1,000+ actions under MIT,
so count is not where a paid product earns its price. The most-trusted servers in adjacent
categories agree - the top Blender and Unity MCP integrations both lead with "focused," not a
number.

So the breadth is real, and it is not the pitch. It exists to cover the domains real work needs, in
service of the verification loop.

### What does "verified" actually mean here? (the mechanism, not the adjective)

It names four mechanisms you can watch on screen or read in a returned payload:

1. **Read-after-write post-state.** Every mutation returns the affected object's actual state read
   back from the live editor - a no-op or wrong-target edit shows in the payload immediately.
2. **Culprit-naming regression gate.** `regression_gate` runs your existing test suite; on red,
   `list_mutations` + `diff_blueprint` isolate the suspect mutation since the last green pass and
   `revert_mutation` rolls it back.
3. **PIE as the acceptance gate.** "Done" means the edit actually runs in Play-In-Editor. Honest
   scope: this rides *your own* test harness - it does not invent tests (see below).
4. **Execution trace.** `instrument_blueprint_trace` arms, triggers, and reads per-node hit counts in
   one call; a JSONL changelog records every tool call.

If we cannot show a claim in a payload or on screen, we do not make it.

### You forked an open-source MCP. Why pay for this?

Nightward began as a fork of an open-source Unreal MCP (MIT/permissive) and has diverged
substantially. The open-source base is good work, it remains free, and we are not disparaging it.
What you pay for is what we added on top: the read-after-write verification loop, the
culprit-naming regression gate, the execution trace, the composite tools, and the support bundle -
plus a maintenance cadence that keeps it current with the engine, and support. You are not paying for
the base tools anyone can get for free.

### Why should I trust a comparison written by the seller?

Because it cedes. [How It Compares](../02-how-it-works/04-how-it-compares.md) names the domains where a competitor is the better buy -
biome-scale PCG depth, Chaos destruction/fields/fracture, deep trace analysis, async, PIE
input-injection, full source-control submit - and tells you which product to use for each. A
comparison that only ever concludes "buy ours" is marketing; one that sends you elsewhere when you
should go there is the honest kind.

---

## Honest limits

### What about performance profiling / Unreal Insights?

A capture floor, not an analysis suite: frame timing, Insights trace start/stop/bookmarks, and stat
smoke data - enough for an agent to catch a perf regression in a headless run. What we do not ship is
deep trace analysis or a profiler-UI replacement - for deep Insights work, use Insights itself.

### What about PCG and Chaos?

PCG has a shipped, live-verified graph floor: create a graph, add/connect nodes, set params, execute,
and read back the generated result. Biome-scale procedural depth is deferred - other servers go
further there.

Chaos destruction, fields, and fracture are out of scope. The UE 5.8 Dataflow simulation family -
cloth authoring, parametric outfits, hair config, flesh - is covered; groom binding is untested
end-to-end.

### Why no test-authoring? Doesn't the regression gate write tests?

No, and this is the most important limit to state plainly. `regression_gate` **runs your existing
oracle suite** - it does not author new tests or oracles from a spec. PIE verification rides that same
harness. Nightward proves that an edit did not break the tests *you already have*; it does not invent
the tests for you.

### What else does it not do?

Stated candidly, because a visible limits section is engineering credibility for a dev tool:

- **Asset import is texture-only today** - no FBX/mesh import.
- **No submit through the engine's source-control provider** - read-only status + checkout only (no
  Perforce/SVN submit/resolve/history). Git projects rarely feel this: your agent's own shell commits
  and pushes natively; the MCP adds checkout-awareness so mutations respect locked files.
- **All tool calls are synchronous** - no background/async tasks, no SSE progress streaming.
- **No synthetic raw input in PIE** - the agent drives gameplay through console commands
  (`exec_console_command`): expose your game's debug/cheat commands and it can trigger and verify
  behavior, which is exactly how our own test harness works. It does not inject keyboard/mouse events.
- **No truly concurrent multi-agent editing** - an engine-level constraint (single transaction stack,
  compiler, PIE slot) that is unsolved industry-wide. Drive one agent at a time.
- **No third-party asset-generation bundling** (e.g. Meshy/Tripo) - we are a verified automation layer,
  not a content service.
- **UE 5.4-5.8** at launch, Windows editor. macOS support is planned post-launch.

### What about token / context cost?

We shape tool responses (field selection, omit-empty, compact JSON, pagination) and measured a ~77%
payload reduction on one real Blueprint read; a catalog / tool-search dispatch mode (the
"few thousand instead of tens of thousands of tokens" pattern some competitors market) is honest
roadmap, not shipped - we say which is which.

---

## License, source, and watermark

### Can I try it before buying? What about refunds?

No trial build or free lite tier - deliberate. But you can try the category free first: Nightward
began as a fork of an open-source Unreal MCP (MIT), and that base remains free. What it will not give
you is the verified loop - readback, regression gate, PIE gate, execution trace, support bundle.

Refunds follow Fab's standard refund policy. The honest-limits section exists so you know what you
are NOT buying before it becomes a refund question.

### Do I get the source code?

Yes - full C++ source ships in every tier. The product is source-available on purpose: you can
inspect it, modify it, keep custom-engine builds working, and you are never blocked waiting on us
for a fix you can read yourself. Inspectability is part of the trust brand.

### If the source ships, what stops someone copying it?

We are honest about the boundary. The governing commercial terms forbid resale and redistribution, and
the shipped provenance markers are the teeth against literal code reuse or resale, enforceable by DMCA.
What those markers do **not** catch is clean-room design cloning
(reimplementing the ideas from scratch): that is legal and undetectable with any source-available
product, and we do not pretend otherwise.

The durable moat is not secrecy. It is (1) a maintenance cadence that makes a copy rot against new UE
versions; (2) a price low enough that copying is never worth an individual's time; and (3)
service/relational value - support, community, updates, config generation, trust - that is not in the
source at all.

### What's the license model and where do I buy it?

One-time perpetual license, free updates, sold on Fab (Epic). Pay once, own forever. No subscription,
no recurring charge, no BYO-AI-credits model.

### Pricing?

$34.99 founder price at launch, rising to $49.99 once the first reviews land (the discount is off the
real $49.99, no inflated list price). Personal, one-time perpetual, with full C++ source and free
updates. Professional and Studio tiers are planned, priced at listing - we sit below the breadth
incumbents on like-for-like on purpose: you are paying for verification depth and support, not tool
count.

---

## Support

### What does support actually look like?

A community channel (Discord/forum) plus best-effort email, and a one-command support bundle
(`export_support_bundle`) that starts every support conversation with real, redacted, local-only
diagnostic data instead of a screenshot. Full C++ source means you are never fully blocked waiting on
us - you can inspect a problem yourself.

### Is there an SLA or priority support?

No. A solo-scale operation cannot honor a per-ticket SLA, and we will not sell a commitment we cannot
keep. The reproducible support bundle replaces the response-time promise. If a hard SLA is a purchase
requirement, we are not the right buy today.

---

## Compatibility & setup

### Which agent clients does it work with?

Any MCP-spec client. Verified working with Claude Code, Cursor, Cline, Codex, and Claude Desktop.
`generate_agent_config` (mode `auto`) registers your client directly where a client CLI exists
(Claude Code, Codex); for Cursor, Cline, and Claude Desktop it writes a reference config and walks
you through that client's own MCP-settings panel.

### What do I need to run it?

Unreal Engine 5.4-5.8, Windows (Win64), the editor plugin (from Fab, Install to Engine or a
project-relative drop both work), one MCP-spec client, and Python on your computer (your agent
checks for this and installs it for you if it's missing, asking first). See [Install](../01-getting-started/02-install.md) and
[Quickstart](../01-getting-started/03-quickstart.md) for the exact steps - end to end it's one Fab install plus one command.

*Synced from repo 811c98a - 2026-07-24*
