# GTM Superintelligence — Datalogz Stack

A curated extract from [attentiontech/gtm-superintelligence](https://github.com/attentiontech/gtm-superintelligence) (Apache-2.0), adapted for the Datalogz GTM stack: **Attio + Slack + Gmail + Google Calendar**.

Built as part of the GTM Engineering system at Datalogz to automate post-call intelligence, pipeline health monitoring, and account-level risk detection across a $934K+ active pipeline.

---

## What's in this repo

```
gtm-superintelligence/
├── agents/              # 10 production agent templates (spec + Claude subagent builds)
│   ├── ae-handoff/
│   ├── churn-alert/
│   ├── deal-stage-clarity/
│   ├── email-generator/
│   ├── lost-deal-intel/
│   ├── multi-thread-detector/
│   ├── pre-call-prep/
│   ├── revenue-sentry/
│   ├── risk-watch/
│   └── validate/
├── rubrics/             # MEDDPICC/SPICED scoring rubrics (YAML)
│   ├── deal-health.yaml         # Opportunity scoring → win-likelihood band + slip risk
│   └── account-health.yaml     # CSM view: adoption, sentiment, churn risk, renewal readiness
├── scorecards/          # Per-call-type coaching rubrics (discovery, demo, negotiation, etc.)
├── coaching-system-prompt.md    # Evidence-bound, no-fabrication system prompt
└── MAP.md               # How this maps to our roadmap phases
```

---

## Our stack config

```yaml
crm: attio
communication: slack
email: gmail
calendar: google_calendar
agent_builder: claude  # Claude subagent / Cowork scheduled tasks
```

---

## The 10 agents

| Agent | Trigger | What it does | Our phase |
|---|---|---|---|
| `email-generator` | 📞 Per call | Drafts followup in rep's voice, locks one next step, DMs as draft | Phase 2 |
| `validate` | 📞 Per call | Proposes Attio field updates, rep approves before write-back | Phase 2–4 |
| `pre-call-prep` | 🗓 T-24h | Morning brief from calendar + CRM + prior calls → DM to rep | Phase 2 |
| `multi-thread-detector` | 📞 Per call | Stakeholder map vs MEDDPICC roles, flags single-thread risk | Phase 3 |
| `deal-stage-clarity` | 📞 Per call | Audits call evidence vs CRM stage, flags over/under-staged deals | Phase 3 |
| `revenue-sentry` | 🗓 Daily | Scores open deals RED/ORANGE/YELLOW with one intervention each | Phase 3 |
| `ae-handoff` | 🔁 Closed Won | Structured handoff: stakeholders, promises, scope, risks, kickoff | Phase 3 |
| `risk-watch` | 📞 Per call | Per-call risk check vs account baseline, tiered alert to CS | Phase 3 |
| `churn-alert` | 🗓 Weekly | Evidence-backed read of at-risk customers + retention action | Phase 3 |
| `lost-deal-intel` | 🔁 Closed Lost | Weekly post-mortem: why we lost, turning point, patterns | Ongoing |

---

## Guardrails (non-negotiable)

- **Draft-only outputs.** Drafted messages go to the rep, not the customer. The rep sends.
- **Evidence-bound.** Every claim tied to a verbatim quote from the call or CRM. No fabrication.
- **Humanizer pass.** All customer- or teammate-facing messages get a final humanizer pass before delivery.
- **No auto-send.** The agent drafts; the human decides.

See `coaching-system-prompt.md` for the full enforcement logic.

---

## Rubrics

The YAML rubrics are the highest-value piece — they're a strong starting methodology regardless of whether you run the agents:

- **`deal-health.yaml`** — MEDDPICC/SPICED scoring across all calls for an opportunity. Produces a win-likelihood band and slip risk. Direct input to our pipeline health dashboard.
- **`account-health.yaml`** — The CSM counterpart: adoption, value realized, sentiment, churn risk, renewal readiness. Pairs with Mixpanel health digests.

---

## What needs adapting for our stack

1. **Runtime.** Originally built for Anthropic API / Python CLI. We run these as Cowork scheduled tasks or Claude subagents — reuse the YAML and prompt logic, not the runner.
2. **Attio I/O.** Their adapters expect transcript files; ours live in Attio. Point the "get the call" step at the Attio MCP tools, map writes to Attio fields.
3. **Transcript source.** We record natively in Attio (and migrated Gong historical transcripts). Ignore the vendor recorder recommendations.

---

## Context: Datalogz GTM Engineering

This repo is one component of a broader GTM Engineering system built at Datalogz (Series A, BI governance/observability, April–June 2026):

- **Signal layer:** Warmly (website de-anon) + conference CSVs (Gartner DA + FabCon, ~600 leads) + LinkedIn migration signals + Webflow forms
- **Enrichment:** Apollo API enrichment pipeline (apollo_enrich.py, enrich_leads_apollo.py)
- **Lead scoring:** Python composite model (9 signals, 4 tiers: Hot/Warm/Interested/Cold), writes to Attio
- **ABM workflow:** Migration-triggered outbound (LISTEN → ENRICH → SCORE → PERSONALIZE → DEPLOY) — migration signals are the #1 predictor of new-logo wins
- **Post-call intelligence:** This repo — call summary → Attio, pre-call briefs, followup drafts
- **SEO + content:** DataForSEO baseline, LLM citation optimizer, competitor gap analysis
- **Product analytics:** Mixpanel health scoring, lifecycle cohorts, customer health digest

---

## License

Adapted from [attentiontech/gtm-superintelligence](https://github.com/attentiontech/gtm-superintelligence) under **Apache-2.0**.

Original repo: Apache-2.0 © Attention. This adaptation: same license.
