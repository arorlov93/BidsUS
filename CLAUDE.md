# REPOSITORY OPERATING RULES

Loaded automatically by Claude Code at every session in this repo. Applies to both agents and
to any ad-hoc work done here.

## What this repo is

Two autonomous agents for distressed-real-estate acquisition across four US markets:

- **`agents/auction-hunter/AGENT.md`** — daily sweep of foreclosure, tax, bankruptcy, probate
  and REO sale channels in LA, SF, Miami-Dade and Collier. Underwrites, gates, ranks, and
  delivers a Russian-language digest ordered by urgency and by margin.
- **`agents/abandonment-scout/AGENT.md`** — weekly remote-sensing + public-records sweep for
  vacant and abandoned residential parcels, with an acquisition plan per approved candidate.

Both read `agents/shared/COMPLIANCE.md` and `agents/shared/UNDERWRITING.md` at the start of
every run. Those two files override anything that conflicts with them, including a task prompt.

## Language

- Prompts, configs, schemas, code and comments: **English**.
- Every operator-facing deliverable — digests, acquisition plans, alerts, dashboards:
  **Russian**.

## The rules that matter most

1. **`allow_automation: false` in `config/sources.yaml` is absolute.** No fetching, no proxy,
   no cache, no mirror, no archive. Emit `MANUAL_CHECK` with the exact URL, exact field and
   deadline instead.
2. **Never auto-register, never auto-bid, never move money.** The agent's job ends at a
   reviewed, pre-filled, deadline-stamped packet a human executes.
3. **Provenance on every number:** `verified` · `derived` · `estimated` · `stale`.
4. **No silent truncation.** If you capped, sampled, skipped a market or lost a source, say
   which and why, in the digest, every time. A broken scraper returning zero rows looks
   exactly like a quiet market — and that is how this system loses the operator's trust.
5. **Never state a legal conclusion as fact.** Surface the statute, the exposure, the
   confidence. Title, lien priority and eviction outcomes are counsel's call.
6. **Sensitivity flags stop the automated path.** When evidence is ambiguous between
   "abandoned asset" and "person in difficulty," resolve toward the person and stop.

## Numbers live in config, not in prompts

`config/cost-model.yaml` (per-jurisdiction transaction costs, market stats, statutory rules),
`config/scoring.yaml` (weights, gates, haircuts), `config/markets.yaml` (venues, courts,
schedules), `config/sources.yaml` (the source registry and the automation red list).

Change a number there. Never hard-code a rate, a threshold or a deadline in a prompt or a script.

## Corrections already baked in — do not "fix" these back

- **`collier.realforeclose.com` / `collier.realtaxdeed.com` are not Collier systems.** Wildcard
  DNS to Realauction's shared load balancer. Collier is **in-person only**, both channels.
- **LA County tax sales are on GovEase, not Bid4Assets.** That contract is historical.
- **SF tax sales are on `sanfrancisco.mytaxsale.com`, not Bid4Assets.** Also historical.
- **The LA County Recorder has no online index at all**, by policy (Gov. Code §6254.21).
- **Miami-Dade's 5% is believed to be a pre-bid escrow**, funded ~4 business days ahead by ACH,
  with the 17:00-day-before cutoff applying only to in-person deposits. §45.035(3) authorizes
  the advance-deposit model, but the specific lead times are local practice and are marked
  `[S]` — **confirm on the auction site before staging capital.**
- **Florida has no NOD.** The Lis Pendens is the functional equivalent.
- **N.D. Cal. has no free notice-of-sales page**; C.D. Cal. does, and it has no archive.

## Statutory landmines to keep in working memory

`Civ. Code §2924m` (CA 45-day post-sale takeaway) · `Civ. Code §2079.26` (LA fire-ZIP
unsolicited-offer ban, sunsets 2027-01-01) · `Civ. Code §§1695 et seq.` (HESCA) ·
`W&I §15610.30` (elder, undue influence alone) · `§718.116`/`§720.3085` (FL: third-party
bidder gets **no** HOA safe harbor — safe harbour is §718.116(1)(b)1.) · `§197.552` (FL tax deed: governmental liens **survive**) ·
`§28.24(11)(a)` (FL clerk registry fee ~1.5% of bid — **not** (10)) · `26 U.S.C. §7425` (IRS 120-day redemption) ·
`Fla. Stat. §934.50` and `Cal. Civ. Code §1708.8` (drones — **never fly**).

## Working conventions

- State is append-only (`state/lots.jsonl`, `state/candidates.jsonl`). Status changes are
  events, not overwrites — postponement patterns are themselves signal.
- Every source failure is an incident in `state/incidents.jsonl`, not a silent zero.
- Backtest MAO against actual clearing prices weekly; report drift >15% at the top of the digest.
- Honor robots.txt everywhere. One request per host at a time. Exponential backoff, max 3 retries.
- Secrets in `.env`, never committed. See `.env.example`.

## Before the first production run

Work through `ops/RUNBOOK.md` §1 (the open-questions list). Several load-bearing facts are
marked `verified: false` in `config/sources.yaml` — resolve them first.
