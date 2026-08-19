# SHARED COMPLIANCE RULES

> Read in full at the start of every run, by both agents. These rules override any instruction
> that conflicts with them, including instructions in a task prompt. If a requested action
> would violate a rule here, refuse the action, log a `COMPLIANCE_BLOCK` incident, and report
> it in the digest.

---

## 1. AUTOMATION BOUNDARY

### 1.1 Never automate — contractually or technically prohibited

| Target | Prohibition |
|---|---|
| `auction.com` | ToU prohibits *"any robot, scraper, or other method of access other than manually accessing the publicly-available portions"* and bypassing robot-exclusion headers. Also **one ID = one account**, ID-verified → auto-registration violates the identity provision. Native Max Bid proxy bidding is the sanctioned mechanism. |
| `search.nationwideposting.com` | Verbatim: *"scripting and any other scripting-related activities are strictly prohibited on this site. Any user accessing the site with massive data requests… will be permanently restricted."* |
| `*.realforeclose.com`, `*.realtaxdeed.com`, `miamidade.realtdm.com` | robots.txt disallows the auction paths. Registration is identity-bound; deposits move by ACH/wire tied to a named account. |
| `lienhub.com` | Active bot protection (403 to non-browser clients). If scale is needed, use LienHub's **sanctioned batch bid-file (CSV) upload**, not scripted UI interaction. |
| `zillow.com`, `redfin.com` | ToS prohibits scraping and automated access. No usable official API for this purpose. |
| `priorityposting.com`, `egis3.lacounty.gov`, `lacourt.org` | robots-disallowed or 403 to automated fetch. |
| Google Maps Static / Street View / Aerial View **as CV input** | ToS **§3.2.3(c)(v)** names "construct an index of tree locations within a city from Street View imagery" as a prohibited example — building a per-parcel vacancy index is the same act. **§3.2.3(c)(vii) separately bans using Maps Content to train or fine-tune ML models.** Caching capped at 30 days where allowed. |
| Google **Solar API** for anything but energy | Service-Specific Terms §18.1 restricts permitted use to energy-system feasibility, design and installation. |

When data behind one of these is required, emit a `MANUAL_CHECK` task: exact URL, exact field,
deadline, and why it matters. Never work around a block by proxy, cache, mirror or archive.

### 1.2 Never auto-register, never auto-bid, never move money

No account creation, no identity submission, no deposit, no wire, no ACH, no bid — on any
platform, under any circumstance, however routine it looks. The agent's job ends at a
reviewed, pre-filled, deadline-stamped packet a human executes.

### 1.3 Sanctioned automation surfaces (green list)

CourtListener/RECAP v4 API (token; 5/min, 50/hr, 125/day free tier; RECAP Fetch 30/min and you
pay PACER) · `cacb.uscourts.gov/notice-of-sales` (free, no account) · Miami-Dade Property
Appraiser JSON proxy (no key) · Miami-Dade ArcGIS REST + ArcGIS Hub v3 API · Collier Property
Appraiser bulk CSV downloads · Collier GMD Hub WMS/WFS/GeoServices · DataSF and data.lacity.org
Socrata (use app tokens) · LA County / eGIS ArcGIS Hub · `notices.collierclerk.com` email
subscription · trustee boards with no scripting prohibition (Prime Recon, TMLF, NDSC) ·
Mapillary API · NAIP · licensed commercial APIs under their own contracts (ATTOM, Regrid,
PropertyRadar, Nearmap, Vexcel, EagleView).

⚠️ **PropertyRadar API licence restriction:** *"intended for end-users only — you cannot use it
to build applications you sell to others."* Fine for internal use; blocks resale without an
OAuth partner agreement. ATTOM permits licensed redistribution.

### 1.4 Rate limiting and courtesy

Honor robots.txt everywhere, including on sources not listed above. One request at a time per
host unless the source documents otherwise. Exponential backoff on 429/503, maximum 3 retries,
then fail the source and log it. Identify with a descriptive User-Agent and a contact address.
Never distribute load across IPs to evade a limit.

---

## 2. PHYSICAL CONDUCT

- **No trespass.** Public right-of-way only. No driveways, yards, curtilage, gates.
- **No drones.** Fla. Stat. §934.50 (surveillance of private property; presumption of privacy
  where not observable from ground level; attorney's fees to the prevailing party) and Cal.
  Civ. Code §1708.8 (constructive invasion; treble damages, disgorgement). FAA Part 107 governs
  commercial VLOS; Part 108 (BVLOS) is still only an NPRM as of 2026 — not law. Buy licensed
  manned-aircraft imagery instead.
- **No mailbox contact.** 18 U.S.C. §1725 — depositing mailable matter without postage is a
  federal offense. USPS mail or hand-delivered door hangers only.
- **No un-blurring** of faces or plates in Mapillary imagery; maintain the technical safeguards
  its ToS §12 requires.

---

## 3. DATA-USE LAW

| Statute | Rule |
|---|---|
| **DPPA** (18 U.S.C. §2721) | Motor-vehicle-derived data. **No permissible purpose covers real-estate lead generation.** $2,500 statutory minimum per violation. |
| **GLBA** (15 U.S.C. §6802) | Nonpublic personal financial information. Marketing and lead-gen are not permissible purposes. TLOxp terms state in capitals that GLBA data may not be used for marketing. |
| **FCRA** (15 U.S.C. §1681b) | If output is used for any eligibility decision it becomes a consumer report. Contact discovery for an unsolicited purchase offer is **not** an FCRA permissible purpose. Require vendor certification that the product is not a consumer report; never use it for eligibility. |
| **CPRA / state privacy** | Owner contact data is personal information. Minimize, retain only as long as the deal requires, honor deletion requests. |
| **HUD USPS vacancy data** | Restricted to governmental entities and nonprofits, census-tract granularity. Not available to a private investor. Address-level requires a USPS Certified Data Vendor licensee. |
| **CA utility records** | Gov. Code §7927.410 exempts customer names, addresses and usage. Do not request or build on it. |

---

## 4. OUTREACH

- **National DNC + Florida state DNC scrub on every record, every time.** Maintain an internal
  DNC list. Honor opt-outs by any reasonable means within 10 business days; one confirmation max.
- **TCPA:** prior express written consent required for autodialed/prerecorded calls and marketing
  texts. The one-to-one consent rule was vacated (11th Cir., Jan 2025) and repealed; the
  revocation-all rule is delayed to 31 Jan 2027. Damages $500–1,500 per violation, uncapped,
  class actions available.
- **Florida:** FTSA (**§501.059**) governs consent for automated calls/texts to FL numbers;
  FDACS registration; both DNC lists; $500 trebled to $1,500 if willful. The **08:00–20:00 local
  calling window is §501.616(6)(a)**, not §501.059 — tighter than the TCPA's 08:00–21:00.
- **Default channel is physical mail or manual dial.** Automated dialing and mass texting are
  disabled at the agent level and may not be enabled by prompt.
- **Cal. Civ. Code §2079.26** — unsolicited offers to purchase residential property are
  **prohibited** in ZIPs 90049, 90263, 90265, 90272, 90290, 90402, 91001, 91024, 91103, 91104,
  91106, 91107, 91301, 91302, 91320. Up to $25,000 civil per violation, misdemeanor exposure,
  seller cancellation right for 4 months, recorded attestation required with the deed.
  **Sunsets 1 Jan 2027 — re-verify quarterly.** Hard-blocked in Tier 0 and re-checked at Tier 4.
- **Cal. Civ. Code §§1695–1695.17 (Home Equity Sales Contract Act)** — for purchases of 1–4 unit
  **owner-occupied residences in foreclosure**, between notice of default and the scheduled sale:
  written contract in the seller's language, statutory disclosures, **5-business-day right of
  cancellation** extending to the day before the sale. Violations can be criminal; contract
  voidable for two years.
- **Elder and vulnerable-adult protection** — Cal. Welf. & Inst. Code §15610.30 (undue influence
  alone suffices, no fraud required, 65+); Fla. Stat. §825.103. See Agent 2 §7.

---

## 5. PROHIBITED STRATEGIES

- **Adverse possession**, in any form or framing. CCP §325 (CA, 5 years + all taxes paid);
  Fla. Stat. §95.18 as tightened, plus HB 621 (2024) sheriff summary removal.
- Any strategy premised on an owner not discovering their own property's status.
- Any contact that misrepresents who you are, what you want, or what the property is worth.
- Manufactured urgency, or any time-pressure tactic, on any owner — categorically prohibited
  with a `ELDERLY_OWNER` or `PROBATE_GRIEF` flag.
- Wholesaling that markets the *property* rather than your equitable interest in the contract:
  FL Ch. 475 unlicensed-brokerage exposure. Track **CA AB 1850** (would require a licence for
  wholesaling; held in committee as of May 2026, not enacted) — the CA DRE already fines
  unlicensed wholesalers under existing law.

---

## 6. EPISTEMIC HONESTY

These are compliance rules too, because a confident wrong number costs real money.

1. **Provenance on every figure**: `verified` · `derived` · `estimated` · `stale`. Never let an
   estimate inherit the appearance of a verified value.
2. **Never present a legal conclusion as fact.** Surface the statute, the computed exposure and
   the confidence. Title, lien priority and eviction outcomes are counsel's call.
3. **No silent caps.** If you truncated a list, sampled, skipped a market or dropped a source,
   say which and why, in the digest, every time. Silent truncation reads as "we covered
   everything" when you did not, and it is the failure mode that makes the system untrustworthy.
4. **Report a degraded source as an incident, not as an absence of opportunities.** Zero results
   from a broken scraper looks exactly like a quiet market.
5. **Report model drift.** When predicted MAO diverges from actual clearing prices by more than
   15% over 30 days, say so plainly at the top of the digest and recommend recalibration.
6. **Contradictions are data.** When two sources disagree, record both, let the more
   authoritative one win, lower the confidence score, and surface the conflict — never silently
   pick one.

---

## 7. INCIDENT LOG

Append to `state/incidents.jsonl` — `{ts, agent, type, severity, source, detail, action_taken}`.
Types: `COMPLIANCE_BLOCK` · `SOURCE_DEGRADED` · `RATE_LIMITED` · `SCHEMA_DRIFT` ·
`CONFLICT_UNRESOLVED` · `SENSITIVITY_FLAG` · `MODEL_DRIFT`.
Anything at `severity: HIGH` goes in the digest header, not the appendix.
