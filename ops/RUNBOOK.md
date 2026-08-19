# RUNBOOK

## 1. Before the first production run — resolve these

Each is marked `verified: false` in `config/sources.yaml`. Several are load-bearing.

### Blocking (a wrong answer here breaks the pipeline)

| # | Question | How to resolve |
|---|---|---|
| 1 | **Miami-Dade tax deeds: `realtaxdeed` or `realforeclose`?** AO-2013-05 specifies `miamidade.realtaxdeed.com`; the Clerk's *current* Property Tax Deeds page directs registration to `miamidade.realforeclose.com`. Realauction has been consolidating tenants. | Open both in a browser, register once, record the canonical host in `sources.yaml`. |
| 2 | **Collier ShowCase Web REST endpoints.** Vue SPA over an undocumented backend — the single most promising automation target in Collier. | Open `cms.collierclerk.com/showcaseweb/calendar`, filter to "Foreclosure Sale", capture the XHR calls in devtools, record them with a `discovered_at` date. Re-verify monthly. |
| 3 | **GovEase Terms of Use.** `govease.com/terms-of-use` returns 404; only Termly privacy/cookie policies are linked. | Capture from the registration flow reached via `ttc.lacounty.gov/auction-general-information/` → `liveauctions.govease.com` before any automated interaction. |
| 4 | **Do LA County and SF assessor open data include owner *mailing address*?** CA assessors commonly redact it in bulk releases. | Pull a sample; if redacted, the absentee-owner signal must be licensed (ATTOM / CoreLogic / Regrid) and Agent 2's Tier 0 filter needs rebuilding. |
| 5 | **Regrid USPS vacancy — is "please don't use for direct mail" a request or a contract term?** | Ask Regrid in writing. This determines whether the highest-value non-imagery signal can drive a mail funnel at all. |
| 6 | **LA venue set.** Is the Norwalk DoubleTree Vineyard Ballroom still active in 2026? | Query Prime Recon and TMLF filtered to LA County and enumerate distinct `Place Of Sale` strings. One pass gives the authoritative current venue set. |

### Non-blocking but resolve within 30 days

7. Nearmap AI Pack availability per county; whether explicit green-pool / tarp classes exist. Get a trial AOI in each of the four.
8. Nearmap / Vexcel / EagleView contract language on **model training and derived-attribute retention**. All three are quote-only; none publish ToS.
9. Miami-Dade Code Violations FeatureServer schema and refresh cadence — query `?f=json` directly; the Hub pages render client-side.
10. Collier code enforcement bulk access — CityView is captcha-gated; likely a public-records-request relationship.
11. SF Vacant/Abandoned Building Registration roster — published as a dataset? File a Sunshine Ordinance request.
12. SF Empty Homes Tax appellate posture (struck down at trial court late 2024, collection suspended).
13. SF recorder portal hostname and coverage year (sf.gov says 1990→, sfassessor.org says 2000→).
14. Florida FTSA **HB 761 (2023)** — autodialer redefinition and 15-day text cure period, against current statute text.
15. Whether the HUD *Cityscape* abandoned-house model/dataset actually shipped open-source.
16. Florida municipal utility shutoff data in practice — what MDWASD and Collier actually return to a Ch. 119 request.

### Standing quarterly review — set a reminder

| Item | Next review |
|---|---|
| **Cal. Civ. Code §2079.26** — sunsets **2027-01-01**. Extended? Expanded to other disaster ZIPs? | 2026-11-01 |
| **Measure ULA** thresholds (CPI-indexed) + any Nov 2026 ballot change | 2026-11-15 |
| **CA AB 1850** (wholesaling licensure) — held in committee as of May 2026 | 2026-11-01 |
| **LA RSO relocation amounts** — current schedule runs to 2026-06-30 | 2026-06-01 |
| **Civ. Code §2924m** — sunsets 2031-01-01 | annual |
| Market stats in `config/cost-model.yaml` | quarterly |

---

## 2. Scheduling

Use **scheduled tasks** (the Claude Code Remote MCP `create_trigger` tools), not in-process cron —
in-process schedules die with the session.

| Task | When | Prompt |
|---|---|---|
| Full auction sweep | 04:30 PT daily (= 07:30 ET, ahead of MDC's 09:00 ET start) | "Run the daily-auction-sweep skill in the distressed-re-agents repo. Deliver the Russian digest." |
| Hot-deadline delta | 13:00 PT daily | "Re-check only lots with a binding deadline inside 48h. Alert only on material change — postponement, cancellation, new judgment amount, redemption." |
| Scout sweep | 06:00 ET Mondays | "Run the abandonment-sweep skill. Deliver the Russian scout digest." |
| Calibration | 07:00 ET Sundays | "Backtest predicted MAO against actual clearing prices from the CA AG §2924m registry and FL clerk sale results. Write state/calibration.jsonl. Flag drift >15%." |
| Source health | 12:00 ET daily | "Check last_success_at for every source in config/sources.yaml. Report anything stale beyond 2× its freshness SLA." |

Cron is UTC — convert from local, and shift day fields if the conversion crosses midnight.

---

## 3. Incident response

| Symptom | Likely cause | Action |
|---|---|---|
| A source returns zero rows | Broken selector, or a genuinely quiet day — **these look identical** | Compare against the trailing 30-day mean. If >2σ low, treat as `SOURCE_DEGRADED` until proven otherwise. Never let a broken scraper read as "no opportunities." |
| CACB notice-of-sales fetch fails | — | `SEVERITY: HIGH`. That page has **no archive** — a missed run is data lost permanently. Re-run within the hour. |
| CA lot sale date passed with no result | Verbal postponement at the sale, unpublished anywhere | Re-check the trustee board. Log the postponement as an event, not an overwrite. |
| 429 from a source | Rate limit | Exponential backoff, max 3 retries, then fail the source and log. **Never** distribute load across IPs to evade a limit. |
| Schema drift on an ArcGIS/Socrata layer | Publisher changed fields | `SCHEMA_DRIFT` incident; pin the field list and alert. |
| Two sources disagree on sale date or opening bid | Normal | Record both in `conflicts[]`, let the more authoritative win, **lower confidence**, surface in the digest. Never silently pick one. |
| MAO drift >15% over 30 days | Model stale, or the market moved | Say so at the **top** of the digest. Recalibrate `SellDiscount`, repair tables and hold months. |

---

## 4. Environment

```
CL_TOKEN=                 # CourtListener API token
PACER_USER=               # RECAP Fetch — password rotates every 180 days, MFA unsupported
PACER_PASS=
SOCRATA_APP_TOKEN_LACITY=
SOCRATA_APP_TOKEN_SF=
ATTOM_API_KEY=            # optional but recommended — fills the LA recorder gap
PROPERTYRADAR_TOKEN=      # CA trustee-sale + postponement tracking. NOT resellable.
REGRID_API_KEY=
NEARMAP_API_KEY=          # or VEXCEL_*, EAGLEVIEW_*
MAPILLARY_TOKEN=
OPS_EMAIL=                # goes in the User-Agent
```

Never commit `.env`. Never send `OPS_EMAIL` anywhere except the User-Agent header.

---

## 5. What "done" looks like for a run

- [ ] Every green-list source attempted; every failure logged as an incident
- [ ] Every CA lot selling within 7 days re-verified against a live trustee board
- [ ] Every lot has a determined lien position, or gate G1 fired and says so
- [ ] Every cost line carries a statutory or config citation and a provenance tag
- [ ] Every blocked lot names the gate and what would unblock it
- [ ] Every `MANUAL_CHECK` has a URL, a field, a deadline and a reason
- [ ] Critical path computed backwards from the binding deadline, not the sale date
- [ ] Digest states coverage and gaps explicitly — no silent truncation
- [ ] Self-audit appended: what I didn't see, what I'm least sure of, where the model is drifting
- [ ] Digest and dashboard delivered; dashboard artifact **updated in place**

---

## 6. Verification log — corrections applied 2026-08-19

An adversarial verification pass tried to refute 18 load-bearing claims against primary sources.
Fourteen held. These seven were corrected in place — do not revert them:

| Claim as originally drafted | Correction |
|---|---|
| Clerk registry fee at Fla. Stat. **§28.24(10)** | It is **§28.24(11)(a)**. (10) is the $1.00 indexing fee. Math was right, cite was wrong. Charged when the bid is accepted per §45.035(3); advance deposits exempt until then. |
| HOA safe harbour at **§718.116(1)(a)** | Safe harbour is **§718.116(1)(b)1.** — (1)(a) is the joint-and-several liability rule. Substance unchanged: a third-party bidder gets no safe harbour. |
| FTSA calling window at **§501.059** | The 08:00–20:00 window is **§501.616(6)(a)**. §501.059 has no time-of-day restriction. |
| §2924m contains the 60-day perfection window | The perfection window is **Civ. Code §2924h(c)** (default 21 days, extended to 60 where §2924m applies). |
| LA tax auctions at `govease.com/los-angeles` | TTC publishes **`liveauctions.govease.com`**. |
| Miami-Dade advance-deposit mechanics stated as fact | **Downgraded to `[S]`.** The statutory floor is §45.031(3) and §45.035(3) authorizes advance electronic deposits, but the ACH lead time, the 17:00 cutoff and the noon balance deadline are Realauction/local practice set by the final judgment — the site is robots-blocked and none of it could be verified from a primary source. Confirm before staging capital. |
| LA RSO relocation amounts hardcoded | **Amounts reset every 1 July.** The 2025–26 schedule has expired; LAHD Bulletin B already publishes 2026–27. Pull the current Chart A at runtime; the stored figures are a magnitude reference only. |

Two claims gained caveats rather than corrections:

- **§197.552** — governmental liens survive **only to the extent unsatisfied from sale proceeds
  under §197.582**, and the section opens *"Except as specifically provided in this chapter."*
  Never present a tax deed as an absolute clean-title rule.
- **Google Maps ToS** — the tree-index example is **§3.2.3(c)(v)**; **§3.2.3(c)(vii)** separately
  bans using Maps Content to train or fine-tune ML models, so Google is excluded as training
  data as well as as inference input.

Re-run this verification before each quarterly review. The statutes that move are §2079.26
(sunset), Measure ULA (indexed), the RSO schedule (annual), and the FL condo reserve rules.
