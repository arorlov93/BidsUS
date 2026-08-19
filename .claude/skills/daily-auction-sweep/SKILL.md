---
name: daily-auction-sweep
description: Run the full Auction Hunter daily sweep across LA, SF, Miami-Dade and Collier — harvest, normalize, enrich, underwrite, gate, rank, and deliver the Russian-language digest. Use when the user asks for today's auctions, the daily digest, a market sweep, or when the scheduled task fires.
---

# Daily Auction Sweep

Execute `agents/auction-hunter/AGENT.md` end to end. This skill is the operational checklist;
the agent file is the doctrine. Read both.

## Preconditions

1. Read `agents/shared/COMPLIANCE.md` — in full, every run.
2. Read `agents/shared/UNDERWRITING.md`, `config/sources.yaml`, `config/cost-model.yaml`,
   `config/scoring.yaml`, `config/markets.yaml`.
3. Load `state/lots.jsonl`, `state/seen.json`, `state/registration_ledger.json`,
   `state/handoff.jsonl` (candidates promoted by Abandonment Scout).
4. Verify every source's `last_success_at`. Anything older than `2 × freshness_sla_hours`
   opens a `SOURCE_DEGRADED` incident that goes in the digest **header**, not an appendix.

## Execution

Create a task list mirroring the agent's phases, then work it:

1. **Bootstrap** — config, state, timezone math, source health.
2. **Harvest** — green-list sources only, per market. Failures are incidents.
3. **Normalize** — one `Lot` per `schemas/lot.schema.json`. Dedupe on
   `sha1(market|channel|parcel_id|sale_date)`. Merge sources; record conflicts, don't resolve
   them silently.
4. **Re-verify CA schedules** — every CA lot selling within 7 days gets re-checked against
   Prime Recon / TMLF / NDSC live `Sale Status`. Postponements are announced verbally at the
   sale with no re-publication anywhere; a date scraped 20 days ago is not evidence.
   Un-reverifiable → `status: unverified_schedule`, confidence capped at 0.4.
5. **Enrich** — parcel, foreclosing instrument, comps, encumbrances, FL municipal/SIRS,
   occupancy, insurability. Stop early if a hard gate already failed.
6. **Underwrite** — itemized MAO. Per-jurisdiction constants. Every cost line cited.
7. **Gate** — G1–G10 from `config/scoring.yaml`. A failed gate zeroes the score and moves the
   lot to `BLOCKED` **with the reason spelled out**.
8. **Score & rank** — value / urgency / confidence / executability; apply risk haircuts and
   show them.
9. **Action ledger** — critical path computed **backwards from the binding deadline**, per lot.
10. **Deliver** — digest, JSON, CSV, dashboard.
11. **Persist & self-audit** — §10 of the agent file. A run without a self-audit is incomplete.

## Deliverables

- `out/{date}/digest.md` — Russian, in the section order specified in AGENT.md §6
- `out/{date}/lots.json`, `out/{date}/hot.csv`
- HTML dashboard — persist it as an artifact and **update the same one** rather than creating
  a new one daily
- `SendUserFile` the digest and dashboard as soon as they exist

## Guardrails specific to this skill

- Never fetch anything on the `blocked:` list in `config/sources.yaml`. Emit `MANUAL_CHECK`.
- Never register, never bid, never move money.
- If the digest would be empty, say so in the first line. A quiet day reported honestly beats
  a padded list.
- Report model drift >15% at the top, not in an appendix.
