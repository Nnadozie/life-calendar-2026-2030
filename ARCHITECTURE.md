# Architecture — Life Calendar 2026–2030

_Verified by inspection on 2026-09-21. This describes what actually runs, not what was intended._

## TL;DR

The calendar is a **local-first web app**. State lives in plain JSON files on one box.
GitHub holds a **one-way published copy** of the plan (for reading/printing) — the box never
pulls from it. AWS is **not** in the runtime path today.

```
  WORKSPACE (authoring)              BOX (runtime)                 PUBLIC
  ─────────────────────              ─────────────                 ──────
  calendar/                          systemd user service          calendar.fronte.io
    gen.py  ──compiles──►            fronte-calendar.service           ▲
    knowledge.py                     node dist/server.js               │ Apache
    opportunities.json               listens 127.0.0.1:8794      ──────┘ proxy
    commitments.json                       │
    AGENT.md                               ├─ data/state-dozie.json   (ticks + notes)
       │                                   └─ data/chat-dozie.json    (chat transcript)
       │  gen.py output
       ▼
  calendar-2026-2030.json  ──sync_calendar_web.py──►  planner-web/calendar.json
                                                          │
                        deploy_calendar.py (SSH, push) ───┘──► /home/ubuntu/fronte-calendar/
                                                                npm install → tsc → esbuild → restart
```

## 1. Where the plan is authored

Everything is authored in the workspace under `calendar/`:

| File | Role |
| --- | --- |
| `gen.py` | Deterministic generator — compiles the inputs into the calendar JSON + MD docs |
| `knowledge.py` | Click-to-expand knowledge layer (per-lane/per-habit detail, links, contacts) |
| `opportunities.json` | Opportunities feed (deadlines `null` until verified — never invented) |
| `commitments.json` | Agent-owned obligations → backward-planned action slices |
| `AGENT.md` | The planner agent's operating brief (it has no system prompt in config) |

`gen.py` emits a single **`calendar-2026-2030.json`** — 197 weeks (W001 2026-09-21 → W197
2030-06-30), four lanes per week plus a Weekly Time Budget, phases, glossary, opportunities,
and commitments. **That JSON *is* the calendar.** Everything downstream just renders it.

## 2. How it reaches the web

Push-based, from the authoring sandbox over SSH. Two scripts:

- **`tools/sync_calendar_web.py`** — copies the generated JSON + the three docs (PRIMER, GUIDE,
  PHASES) into the web app source (`planner-web/`).
- **`tools/deploy_calendar.py`** — SSHes to the box, writes `/home/ubuntu/fronte-calendar/`,
  runs `npm install`, `tsc --noEmit`, the esbuild bundle, restarts the service, and wires the
  Apache vhost. Idempotent; does **not** touch the separate `planner.fronte.io` product on :8792.

## 3. What runs live

- **systemd user service** `fronte-calendar.service`
  → `ExecStart=/usr/bin/node /home/ubuntu/fronte-calendar/dist/server.js`
  → binds `127.0.0.1:8794`, `Restart=always`, logs to `/tmp/fronte-calendar.log`.
- **Apache vhost** `calendar.fronte.io.conf` proxies the domain → `127.0.0.1:8794`
  (`X-Robots-Tag: noindex`).
- **Stack:** TypeScript + React + Node, esbuild bundle, zero runtime npm deps beyond React.

### API surface (`src/server.ts`)

| Route | Auth | Purpose |
| --- | --- | --- |
| `GET /api/calendar` | none | the plan JSON |
| `GET/POST /api/state` | none | ticks + notes |
| `GET/POST /api/chat` | `x-chat-key` (fail-closed) | talk to the planner agent |
| `GET /api/doc/<name>` | none | PRIMER / GUIDE / PHASES / calendar |

## 4. Where state lives — the correction

Your ticks and notes are **plain JSON on the box's local disk**:

```
/home/ubuntu/fronte-calendar/data/state-dozie.json   ← ticks + notes
/home/ubuntu/fronte-calendar/data/chat-dozie.json    ← chat transcript
```

Writes are **atomic** (temp file + `renameSync`). There is **no AWS SDK dependency** in the app
(grepped), **no database, no S3, no DynamoDB**. One owner, one file per surface.

## 5. Where GitHub fits — also half-wrong

`Nnadozie/life-calendar-2026-2030` (public) holds a **one-way published copy**:

```
README.md          (the full plan as rendered Markdown, ~443 KB)
planner.pdf        (printable weekly planner)
commitments.json   (mirror of the agent-owned input)
PRIMER.md / GUIDE.md / PHASES.md
```

Pushed via the GitHub REST API. **The box never pulls from it** — no `git` binary on the box,
no CI, no webhook. It is a publishing target, not a source of truth.

## 6. AWS — real resources, currently unused by the app

A scoped IAM user **`calendar-agent`** (policy `CalendarAgentAccess`, 8 statements incl. a Deny
guardrail) exists, with real resources provisioned so the policy governs something concrete:

- S3 bucket `fronte-calendar-state`
- DynamoDB table `fronte-calendar-state` (PK `pk`, PAY_PER_REQUEST)
- CloudWatch log group `/fronte/calendar`
- SSM namespace `/fronte/calendar/*`

The agent reaches AWS **only** via `tools/awsx.py` (hard-wired to its own key; never the ambient
chain). **But the running calendar does not touch any of these.** They are an empty shell today.

## 7. The honest gap: durability

State is **one unbacked-up JSON file per owner**. If the box dies, your ticks and notes are gone.

Three shapes for how state should live:

| Option | Shape | Trade-off |
| --- | --- | --- |
| **A — Local (today)** | App writes local JSON | Fast, simple, zero AWS. **No backup.** |
| **B — Local + AWS backup** | Local writes; agent mirrors to S3 nightly via its identity | Adds durability; AWS becomes meaningful. Small change. **Recommended.** |
| **C — AWS-native** | App reads/writes DynamoDB directly | Durable, multi-device; adds AWS runtime dep + SDK to the app. |

Separate axis — **delivery**: keep push-from-sandbox (today) vs make the box pull from GitHub
via webhook/CI.

## 8. Open decisions

1. State durability: A, B, or C above?
2. Delivery: push (today) vs pull-from-GitHub?
3. Two AWS loose ends: the inert leftover probe IAM user; and moving the admin key out of the
   agent-visible mount so the scoped-user boundary becomes technical, not just instructional.

---

_Generated by inspection of the running system, 2026-09-21. If the runtime changes, this file is
stale until updated._
