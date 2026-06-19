# GTM Superintelligence: what we pulled and why

Curated extract from [attentiontech/gtm-superintelligence](https://github.com/attentiontech/gtm-superintelligence) (Apache-2.0, open source, made by Attention). Pulled June 12, 2026. We kept only the pieces relevant to our roadmap, not the full repo (the rest is a Python CLI we don't need).

## Why this repo matters to us

It's the same post-call engine we're building, already designed, and it shares our principles: every claim tied to a verbatim quote (no fabrication), draft-only outputs that go to the rep not the customer, a mandatory humanizer pass, and no em dashes. Crucially, Attio is a first-class CRM option, and Slack / Gmail / Google Calendar are the defaults, so it maps onto our stack with config, not a rewrite.

It does NOT replace our phase 1 (summary to Slack). Our loop is simpler and already wired to the Attio MCP. This is a reference and a parts bin for phases 2 and 3, plus our separate lead-scoring and pipeline-health work.

## What's in this folder

### rubrics/ (highest value for us)
- `deal-health.yaml` — MEDDPICC/SPICED scoring of an opportunity across all its calls: qualification coverage, multithreading, compelling event, next-step hygiene, into a win-likelihood band and slip risk. Direct input to our **pipeline health dashboard** goal.
- `account-health.yaml` — the CSM counterpart: adoption, value realized, sentiment, churn risk, renewal readiness. Direct input to our **account health** work (pairs with the Mixpanel health digests).

Both are plain editable YAML. Even if we never run their code, these are a strong starting methodology to lift into our own scoring.

### scorecards/ — one rubric per call type (discovery, demo, negotiation, renewal, QBR, etc.). Reference for if/when we add call coaching.

### agents/ — curated subset of the 30 templates, each with a readable spec (`.md`) and a ready-to-adapt Claude subagent build (`.claude-subagent.md`)

Mapped to our phases:

| Agent | What it does | Our use |
|---|---|---|
| `email-generator` | Drafts a short follow-up in the rep's voice, locks one next step, DMs it to the rep as a draft | **Phase 2** follow-up email. Closest match to what we designed. Same draft-only guardrail. |
| `validate` | Turns a call into proposed CRM field updates, DMs the owner a before/after card, writes back only approved fields | **Phase 2 to 4** Attio auto-fill. This is exactly our "eventually automate Attio updates" with a human gate. |
| `multi-thread-detector` | Builds a stakeholder map vs MEDDPICC roles, scores single-thread risk, alerts when a deal leans on too few people | **Phase 3** risk signal. Ties to won/lost analysis. |
| `deal-stage-clarity` | Audits call evidence vs CRM stage, flags over/under-staged and stale deals, quantifies forecast adjustment | **Pipeline health dashboard** input. |
| `revenue-sentry` | Daily scan scoring open deals for risk, tiers them RED/ORANGE/YELLOW with an intervention each | **Pipeline health dashboard** input. |
| `ae-handoff` | On Closed-Won, produces a structured handoff (what sold, stakeholders, promises, scope, risks, kickoff steps) | **Phase 3** contracting/handoff branch. |
| `risk-watch` | Per-call risk check vs the account baseline, tiered alert to CS only when real | **Account health**. |
| `churn-alert` | Weekly evidence-backed read of at-risk customers with one retention action each | **Account health**. |
| `lost-deal-intel` | Weekly post-mortem of lost deals: why, turning-point call, patterns | Feeds our won/lost pattern analysis. |
| `pre-call-prep` | Morning per-meeting briefing from calendar + CRM + prior calls, DM'd to the rep | A separate high-value workflow worth its own build. |

### Supporting files
- `agents/AGENTS_INDEX.md` — the full list of all 30 agents with one-line goals and trigger types.
- `agents/config.yaml` — the integration config. Note: set `crm: attio`, `communication: slack`, `email: gmail`, `calendar: google_calendar` to match us.
- `coaching-system-prompt.md` — their system prompt, worth reading for how they enforce the evidence-bound, no-fabrication rule.

## What needs adapting before any of it runs for us

1. **Runtime.** It's built for the Anthropic API / a Python CLI / Attention's native builder, not our Cowork scheduled tasks. We reuse the YAML and the agent prompt logic, not their runner.
2. **Attio I/O.** Attio is a supported CRM in their config, but their adapters expect transcript files. Our transcripts live in Attio. We'd point the "get the call" step at the Attio MCP tools we already use, and map CRM writes to Attio fields.
3. **Caveats.** Beta, and vendor-flavored (Attention recommends itself as the recorder throughout). The rubrics are genuinely neutral; ignore the recorder pitch since we record natively in Attio.

## License
Apache-2.0. We can fork, modify, and ship inside our own stack.
