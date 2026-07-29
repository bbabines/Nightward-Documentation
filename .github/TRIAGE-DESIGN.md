# Buyer gap-report triage - design

No Anthropic API key anywhere in this pipeline - Brad reviews via his own Claude subscription
(Claude Code), not a pay-per-token integration. That rules out an unattended GitHub Action calling
the Messages API; review is a pull, prompted step instead.

## Intake
`ISSUE_TEMPLATE/tool-gap.yml` - a structured GitHub Issue Form (not free text): tool/feature name,
client, versions, what you tried, what happened, expected. Labels `gap-report` + `needs-triage`
are declared in the form and auto-created by GitHub on first use.

## Notification
GitHub's native repo webhook posts every new issue to Discord in real time - no code. One-time
setup in repo Settings -> Webhooks (Discord side generates the URL, paste it into GitHub, tick the
Issues event).

## Review - pull, then prompt Claude
`NightwardDev/tools/pull_gap_issues.py` shells out to `gh` (GitHub CLI, Brad's own `gh auth login`
- no token ever touches a file this tooling writes) and pulls every open `gap-report` issue plus
enough context (other open/closed issue titles) to judge duplicates. It prints a structured brief,
nothing more - no verdict, no labeling. A Claude Code turn (this subscription, not the API) reads
that brief and judges each report: fake/spam, misunderstood (user error, not a real gap), duplicate
of an existing issue, or a real gap - then applies labels and posts a comment via `gh issue edit` /
`gh issue comment`.

Run modes:
- **On demand** - ask Claude Code to "pull and triage gap reports" whenever.
- **Scheduled** - wire the same prompt into a recurring scheduled task (the `schedule` skill /
  `CronCreate`) at whatever cadence Brad picks. Discord already gives instant visibility, so the
  triage pass itself doesn't need to be real-time - daily or a few-times-a-day is plenty and keeps
  subscription usage light.

## Promotion into the private ledger
Still a separate, human-gated step. This repo is public and has no write access to the private
`NightwardDev` repo where `docs/MCP-GAPS.md` and the real BP-GAP numbering live (gap-ID collisions
have bitten before). Confirmed-gap issues get promoted into the ledger by Brad, not auto-merged.
