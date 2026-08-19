---
name: abandonment-sweep
description: Run the Abandonment Scout funnel — build the parcel universe, apply free public-records signals, run overhead and street-level CV, promote candidates that meet the evidence rule, and deliver the weekly Russian-language scout digest. Use when the user asks to find vacant or abandoned properties, run the satellite sweep, or when the weekly scheduled task fires.
---

# Abandonment Sweep

Execute `agents/abandonment-scout/AGENT.md`. This skill is the checklist.

## Preconditions

Read `agents/shared/COMPLIANCE.md` in full. Then `config/scoring.yaml`, `config/sources.yaml`,
`config/markets.yaml`. Load `state/candidates.jsonl`, `state/funnel_audit.jsonl`.

## The five things that make this legal

Confirm each before running. Any "no" stops the run.

1. **No drones.** Licensed manned-aircraft imagery only. Fla. Stat. §934.50 presumes privacy
   for anything not observable from ground level and awards attorney's fees to the prevailing
   party. Two of four markets are in Florida.
2. **No Google imagery as CV input.** Maps ToS §3.2.3(c) names "construct an index from Street
   View imagery" as a prohibited example. Solar API is locked to energy use. Mapillary is the
   only lawful street-level substrate at scale; NAIP + Nearmap/Vexcel for overhead.
3. **No trespass, no mailbox contact.** Public right-of-way only; 18 U.S.C. §1725.
4. **CA §2079.26 ZIPs hard-blocked** — 90049, 90263, 90265, 90272, 90290, 90402, 91001, 91024,
   91103, 91104, 91106, 91107, 91301, 91302, 91320. Unsolicited offers prohibited by any means.
   Sunsets 2027-01-01; re-verify quarterly.
5. **No adverse possession**, in any framing. If a run produces a recommendation shaped like
   "occupy and wait," the run is defective — discard and log an incident.

## Funnel

Each tier logs what it dropped and why to `state/funnel_audit.jsonl`. That log is not optional —
it is how the operator knows the funnel is working rather than silently collapsing.

**Tier 0 — universe.** Assessor roll → residential use codes → absentee/out-of-state mailing,
FL no-homestead, tenure >15y, no permits 15y. Apply hard exclusions.

**Tier 1 — free public records.** USPS vacancy (Regrid, parcel-level) · **LA Vacant Building
Abatement `q3ak-s5hy`** (a literal municipal vacancy list) · code enforcement · tax delinquency ·
mailing mismatch · long tenure + zero permits · probate. Weights and decay in `config/scoring.yaml`.

**Tier 2 — overhead CV.** Pool green/debris (nearly pathognomonic) · tarp/damaged roof
**across ≥2 epochs** (a single tarp in FL is a storm, not abandonment) · vegetation encroaching ·
lawn NDVI vs **block median, season-normalized** (LA drought landscaping inverts the sign) ·
debris · no vehicle across ≥3 captures. Consider buying Nearmap AI Packs rather than building —
Roof Condition, Vegetation/tree-overhang/debris, Pool covered/empty/debris-filled, and
Construction as an *inverse* signal already ship.

**Tier 3 — street-level.** Mapillary only. Boarded windows, missing door, deteriorated entry.
Attribution required; maintain the anti-re-identification safeguards its ToS demands.
⚠️ Coverage is thin in Collier exurbs — expect this tier to be largely unavailable in Naples,
and say so rather than letting its absence read as "no signal."

**Tier 4 — promotion rule, no exceptions.**
> ≥2 imagery signals × ≥2 capture epochs **+** ≥1 non-imagery corroborator.

Then `human_review: pending`. **Never auto-promote to outreach.**

**Tier 5 — owner + lawful contact.** Entity owners resolve through SOS/Sunbiz — often better
than a skip trace. Skip-trace vendors must be non-GLBA/non-DPPA and certify the product is not
a consumer report (TLOxp is excluded). Scrub national + FL DNC. Physical mail or manual dial
only; autodialer and mass text are disabled at the agent level and may not be enabled by prompt.

**Tier 6 — acquisition plan.** Pick one pathway, reject the others in one line each. Watch the
statutory overlays: CA probate overbid (§10311 — bid + $1,000 + 5% of the excess over $10,000,
unless full IAEA authority applies, in which case none of it does) and CA HESCA (§§1695 et seq.)
on any in-foreclosure 1–4 unit owner-occupied purchase.

## Sensitivity flags — the part that matters most

`ELDERLY_OWNER` · `PROBATE_GRIEF` · `OCCUPIED` · `HOARDING` · `DISASTER_ZONE` · `LANGUAGE_ACCESS`.

Each stops the automated path and routes to human review. **When the evidence is ambiguous
between "abandoned asset" and "person in difficulty," resolve toward the person and stop.**
Being wrong in that direction costs one lead. Being wrong in the other direction costs the
business — Cal. W&I §15610.30 reaches undue influence with no fraud required, and Fla. Stat.
§825.103 grades exploitation by property value.

## Deliverables

`out/{date}/scout-digest.md` (Russian, sections per AGENT.md §5) · `out/plans/{parcel_id}.md`
per approved candidate · updated map/dashboard artifact, **updated in place** · handoffs written
to `state/handoff.jsonl` for Auction Hunter.

The digest must state coverage explicitly: parcels processed, survivors per tier, what was
dropped and why, imagery capture dates per market, and where imagery is stale enough that a
conclusion is unreliable.
