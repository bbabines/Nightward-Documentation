# Nightward MCP - Privacy Policy

Nightward MCP runs entirely on your machine. By default it collects **nothing** - telemetry is OFF
until you take an explicit action to turn it on. This document describes what the optional,
opt-in telemetry stream contains if you enable it, and your rights over it.

## What we collect (opt-in only)

Only if you opt in. Each event carries exactly these fields and nothing else (this is the live
allowlist in `Python/helpers/telemetry.py`, `TELEMETRY_SCHEMA_VERSION`):

| Field | What it is |
| --- | --- |
| `schema_version` | Version of this telemetry event format. |
| `install_id` | A random, locally generated, anonymous ID for your install. Not tied to you. |
| `engine_version` | The Unreal Engine version in use (e.g. 5.8). |
| `ts` | Timestamp of the event. |
| `tool` | The name of the MCP tool that ran (e.g. `compile_blueprint`). |
| `result_class` | Coarse outcome class (e.g. success / failure). |
| `error_taxonomy` | Category of error when one occurred; null on success. |
| `duration_ms` | How long the call took, in milliseconds. |
| `plugin_version` | The Nightward MCP plugin version. |
| `client_version` | The connected MCP client version. |
| `pie_active` | A coarse boolean: was Play-In-Editor active. |

## What we never collect

File contents, file or asset paths, project names, source code, prompt or prompt text, API tokens or
credentials, account/email/username, or any machine fingerprint beyond the random `install_id`.

This is a strict **allowlist**, not a filter: a future field cannot leak because it was never added
to the sent payload in the first place - not because something stripped it out afterward. The
transport is a hand-built envelope, not an SDK's auto-capture, so there is no stack trace, host
context, or user context attached.

## How to opt in / opt out

- **Default is OFF.** Zero data leaves your machine until you opt in.
- **Opt in** with the environment variable `UNREALMCP_TELEMETRY=on` (or the editor settings-panel
  knob - coming in a future update).
- **Opt out** at any time by removing that opt-in. Additionally, `DO_NOT_TRACK=1` or `NO_TELEMETRY=1`
  disables telemetry unconditionally and overrides any opt-in.

## Who processes it

If enabled, events are sent to **Sentry** (sentry.io), which acts as the data processor. Sentry
receives ONLY the allowlisted fields above - no stack trace, no host or user context.

## Retention

Events are retained for **30 days** by our processor, Sentry (the retention window for Sentry's
Developer plan, the plan this telemetry project is on), then deleted.

## Your rights (GDPR / CCPA)

- **Right to know.** This document is the disclosure. The local spool files and the
  `export_support_bundle` tool let you inspect exactly what would be sent, before anything is sent.
- **Right to opt out.** Always available, and it is the default state - one environment variable.
- **Right to erasure.** Email bbabines@protonmail.com with your install ID (found in
  `Saved/MCPTelemetry/install_id.txt`) to request deletion of matching events. Deletion is
  best-effort by ID, since no other identity is stored.
- **No sale of data, ever.** Your data is anonymized and is never sold or shared beyond the processor
  named above.

## Contact

Nightward MCP is published by Brad Babines (sole proprietor). Reach support through the Nightward MCP
community channel (Discord) or the seller contact on the Fab listing.
