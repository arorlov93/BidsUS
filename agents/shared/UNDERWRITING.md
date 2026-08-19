# SHARED UNDERWRITING MODEL

> Numeric constants live in `config/cost-model.yaml` and `config/markets.yaml` so they can be
> updated without touching prompts. This file is the *method* and the *rules*; the config is
> the *numbers*. Where a figure appears here it is the documented default as of Aug 2026.
>
> Confidence key: `[V]` verified from a primary source · `[S]` secondary · `[E]` estimate.

---

## 1. THE MAO MODEL

**Do not bid on the 70% rule.** `MAO = ARV × 0.70 − Repairs` is a screening reject filter only,
and it is systematically wrong in both directions in these markets: closing plus holding is a
much smaller share of a $1.1M LA ARV than of a $200k midwest ARV, and it entirely misses Miami
condo assessment carry.

```
MAO = (ARV × SellDiscount)
    − Repairs × (1 + ContingencyPct)
    − BuyCosts        # transfer tax / doc stamps / registry fee / recording / escrow / SURVIVING liens
    − HoldCosts       # months × (debt service + tax + insurance + HOA + utilities + security)
    − SellCosts       # commission + seller-side transfer tax + closing
    − LegalCosts      # eviction, quiet title, permit close-out, estoppel, assessments
    − RiskReserve     # occupancy, no-interior-inspection, lien-uncertainty haircuts
    − TargetProfit
```

**Bid ceiling rule.** When buying a **junior** position, the ceiling is the *lesser* of MAO and
`(senior lien payoff + arrears + costs)`. If the senior payoff cannot be established, the lot is
gated, not discounted.

**Parameters:**

| Parameter | Value |
|---|---|
| `SellDiscount` | 0.95–0.98, anchored to actual list-to-sale: LA 99.7% (48 DOM) `[V]` · SF strong (25 DOM, +11% YoY) `[V]` · Miami-Dade SFR 96% of original list (88 DOM), **condo 93% (125 DOM)** `[V]` · Naples 94.5% (103 DOM, condo 115) `[V]` |
| `ContingencyPct` | **+15% with interior access · +30–40% without.** Without is the normal case at auction. |
| `TargetProfit` | 12–15% of ARV (CA flip) · 15–20% (FL, for insurance and assessment tail risk) · or a 25–35% annualized IRR hurdle on equity |
| `ExitCap` (rental/BRRRR) | trading cap **+50–75 bps**; **+100 bps more** for FL condos ≥3 stories with unresolved SIRS, or reject |

**Rental variant:** `MAO = (Stabilized NOI ÷ ExitCap) × 0.90 − Repairs − BuyCosts − HoldToStabilize`

---

## 2. TRANSACTION COSTS — never use one national constant

**Headline: FL judicial-sale buy costs (~2.3–2.8%) are 2–3× CA trustee-sale buy costs
(~0.8–1.2%)**, driven almost entirely by the clerk registry fee plus doc stamps.

### California trustee sale (LA, SF)

| Item | Amount |
|---|---|
| Payment terms | Full amount **immediately at the sale**, cash or cashier's check on a state/national bank or credit union (Civ. Code §2924h(b)) `[V]`. No financing, no contingency. Sale auto-rescinds if funds are not available for withdrawal. |
| Deed perfection | Retroactive to 08:00 on sale day **if the trustee's deed records within 21 calendar days**; extends to **60 days** where §2924m applies. The perfection window itself is **Civ. Code §2924h(c)**, not §2924m `[V]` |
| DTT — LA County | $0.55 / $500 = **0.11%** |
| DTT — City of LA | $4.50 / $1,000 = **0.45%** on net value → **City of LA base ≈ 0.56%** `[V]` |
| **Measure ULA (City of LA)** | **4.0%** on $5.40M–$10.90M, **5.5%** ≥$10.90M, on **gross** consideration, closings after 30 Jun 2026; thresholds CPI-indexed `[V]`. **ULA exempts deeds in lieu, actual foreclosures, and conveyances under federal bankruptcy cases** `[V]` — so a trustee's deed should sit outside it, **but your resale does not.** Any ≥$5.4M LA exit must price 4–5.5% ULA. Confirm the exemption with counsel; a Nov 2026 ballot measure may change it. |
| **SF transfer tax** (city = county), per $500 `[V]` | >$100–250k → $2.50 (**0.50%**) · >$250k–<$1M → $3.40 (**0.68%**) · $1M–<$5M → $3.75 (**0.75%**) · **$5M–<$10M → $11.25 (2.25%)** · **$10M–<$25M → $27.50 (5.50%)** · ≥$25M → $30.00 (**6.00%**). **The cliff at $5M is a 3× jump — SF underwriting must model the exit price against the $5M / $10M / $25M lines explicitly.** Customarily seller-paid, so it hits the exit. |
| DTT exemption | RTC §11926 exempts the transfer to the **beneficiary** up to the debt. A **third-party bidder gets no exemption** `[V]` |
| Recording | ~$100–250 + SB2 $75/title (capped $225/transaction) `[E]` |
| Title insurance | **None at the sale** — trustee's deed, no warranty. Insurable later, but underwriters commonly require the **§2924m 45-day window to run**, a foreclosure-procedure review, sometimes 3–6 months seasoning. Budget $1,500–6,000 and **assume no resale or refi for ~45–90 days** `[E]` |
| **All-in buy (City of LA, third party)** | **≈0.8–1.2%** before surviving liens |
| **All-in sell (City of LA)** | commission 4–5% + DTT 0.56% + escrow ≈ **5–6%**, **+4% ULA if ≥$5.4M** |
| **All-in sell (SF)** | ≈**5.5–6%** sub-$5M → **~7.5% at $5M+** |

### Florida clerk judicial sale (Miami-Dade, Collier)

| Item | Amount |
|---|---|
| Deposit | **5% of the bid** — statutory floor at Fla. Stat. **§45.031(3)** `[V]`; **§45.035(3)** expressly recognizes *advance electronic deposits* and exempts them from the registry fee until a bid is accepted `[V]`. **Miami-Dade is understood to pre-fund** — 5% of your *maximum intended bid* before bidding, ACH ~4 business days ahead, in-person (cash / cashier's / official bank / attorney trust check) by 17:00 the day before at 111 NW 1st St, no money orders or personal checks — **but these are Realauction/local-practice terms set by the final judgment and could NOT be verified from a primary source (the site is robots-blocked). `[S]` Confirm on the auction site before staging capital.** **Collier: 5% at the sale, cash / cashier's check / money order only — no ACH, no wire `[V]`.** |
| Balance | **MDC: 12:00 noon next business day. COL: 10:30 next business day.** Miss it → deposit forfeited, property re-advertised. |
| **Clerk registry fee (§28.24(11)(a))** | **3% of the first $500 + 1.5% of the balance ⇒ ~1.5% of the bid** `[V]`. On a $500k bid = **$7,507.50**. **The single most commonly missed FL auction cost.** Charged when the bid is accepted per **§45.035(3)**; advance electronic deposits are exempt until then. ⚠️ Do not cite §28.24(10) — that subsection is the $1.00 indexing fee. |
| Clerk sale charge (§45.035) | **$70** `[V]` + electronic-sale fee (~$60 in MDC) `[V]` |
| Doc stamps on certificate of title | Statewide **$0.70/$100 = 0.70%** `[V]`. **Miami-Dade: $0.60/$100 (0.60%) + $0.45/$100 surtax (0.45%) = 1.05% — but the surtax does NOT apply to a single-family dwelling** `[V]`. → **MDC SFR 0.60% · MDC condo/MF/commercial 1.05% · Collier 0.70%.** That 45 bps hits every Miami condo on the way in *and* on the way out. |
| Cert. of sale → cert. of title | Certificate of sale on payment; **10-day objection window** (§45.031(5)); title recorded on **day 11** absent objection or bankruptcy `[V]` |
| Title insurance | Not available at the sale. Requires underwriter review of **service of process on all defendants** and the §45.031 notice chain. **Defective service is the #1 FL foreclosure title defect.** Budget $1,000–3,500 `[E]` plus possible quiet title. |
| **All-in buy — MDC condo** | registry ~1.5% + stamps 1.05% + fees ≈ **2.6–2.8%** before surviving HOA/municipal liens |
| **All-in buy — Collier SFR** | ≈**2.3%** |
| **All-in sell (FL)** | commission 5–6% + stamps 0.60–1.05% + title (seller-paid by custom in MDC, **buyer-paid in Collier/SW FL**) ≈ **6.5–7.5%** |

---

## 3. REDEMPTION & FINALITY

| Jurisdiction | Rule |
|---|---|
| **CA non-judicial** | No statutory post-sale redemption — **but that is not finality.** Civ. Code §2924m (1–4 units): sale not final for **15 days** while eligible bidders file notice of intent; if filed, until **day 45** to submit a bid *exceeding* yours; perfection window extends to 60 days. Eligible bidders: eligible tenant buyer, prospective owner-occupant, tenant-member nonprofits, qualifying 501(c)(3)s, community land trusts, limited-equity co-ops, public entities. **Sunsets 1/1/2031.** `[V]` → Treat every CA 1–4 unit purchase as carrying a 45-day option written against it. |
| **CA judicial foreclosure** | 3 months if proceeds satisfy the debt; **1 year if a deficiency was sought** (CCP §729.030) `[S]`. Effectively uninvestable for a flip — gate it. |
| **FL judicial sale** | Redemption cut off by filing of the certificate of sale (§45.0315). Practical exposure = the **10-day objection window**. Objections on inadequate price or procedure do get granted; **spend nothing on the asset before the certificate of title records.** `[V]` |
| **FL tax deed** | Owner/certificate holder may redeem **any time until the deed actually issues** (§197.472) `[V]`. Redemption risk is pre-sale — expect a meaningful share of noticed sales to evaporate. |
| **CA tax-defaulted** | Redemption terminates **17:00 the last business day before the sale** (RTC §3707) `[V]`. Post-sale, parties of interest may claim excess proceeds within 1 year (§4675) **and sue to set the sale aside within 1 year** (§3725/3726) `[S]` → not cleanly insurable for a year. |
| **IRS** | Where an FTL was recorded ≥30 days before the sale and is junior: discharged **only if** the IRS received **25 days' written notice** (26 U.S.C. §7425(c)(1)). The US then holds a **120-day right of redemption** at your price + 6%/yr simple + payments to seniors + maintenance less rents `[V]`. Two flags: defective notice → the FTL survives entirely; good notice → no safe resale for 120 days. Search FTLs at the recorder **and** confirm the trustee's/plaintiff's §7425 notice. |

---

## 4. LIEN SURVIVABILITY

### Foreclosure of a first DoT / first mortgage

| Lien | CA | FL |
|---|---|---|
| Junior mortgages | Wiped | **Wiped only if named AND properly served.** An omitted junior **survives** and requires re-foreclosure. **Check the docket returns of service, not just the lis pendens.** |
| Property taxes / assessments | Survive | Survive (§197.122 superior to all) |
| Mello-Roos / CFD (CA) · CDD / special districts (FL) | Survive | Survive |
| **HOA / condo assessments** | Generally wiped if recorded after the DoT — **CA has no HOA super-lien.** CC&Rs survive. | ⚠️ **Wiped as a lien, but the liability is not.** §718.116(1)(a) / §720.3085(2)(b): a purchaser is **jointly and severally liable with the prior owner for ALL unpaid assessments that came due before transfer of title.** The **safe harbor** — **§718.116(1)(b)1.**, lesser of 12 months' regular assessments or 1% of the original mortgage debt — is available **only to the first mortgagee or its successor/assignee** — **NOT to a third-party bidder.** `[V]` **This is the single largest FL underwriting trap.** Worked example: $36,000 delinquency on a $400k original mortgage → the bank would pay $4,000; **you pay $36,000.** |
| **Municipal / code-enforcement liens** | Recorded junior liens generally wiped; the underlying violation runs with the land and the city will still make you cure | Recorded junior liens wiped if named — but **unrecorded municipal charges (water/sewer, trash, lot-clearing, unsafe-structure) attach to the property and are NOT in the official records.** `[V]` **Order a municipal lien search every time**, per municipality (City of Miami ≠ Miami Beach ≠ Hialeah ≠ unincorporated MDC). $85–175, 2–5 business days `[E]`. |
| Federal tax liens | §7425 rules above | Same |
| Mechanic's liens | Priority runs from **first work date**, not recording — can prime a later DoT | Ch. 713 relation-back to the notice of commencement |
| Easements, CC&Rs | Survive | Survive |
| Leases | Junior lease wiped, **but** CCP §1161b + federal PTFA give bona fide tenants **90 days** | Junior lease wiped; **PTFA 90-day notice applies** |

### FL tax deed — a different and harsher rule

**Fla. Stat. §197.552**, verbatim: *"no right, interest, restriction, or other covenant shall
survive the issuance of a tax deed, except that a lien of record held by a municipal or county
governmental unit, special district, or community development district, when such lien is not
satisfied as of the disbursement of proceeds of sale under s. 197.582, shall survive."* `[V]`

- **Extinguished:** mortgages, judgments, mechanic's liens, **HOA/condo liens**, IRS liens (with §7425 notice).
- **Survives:** governmental / municipal / county / special-district / CDD liens of record —
  trash, water/sewer, lot clearing, abatement, **code-enforcement fines**. Miami-Dade code fines
  routinely run **$250/day** and can exceed the property's value.
- ⚠️ Two limits on the "clean title" reading: §197.552 opens **"Except as specifically provided
  in this chapter"**, and governmental liens survive **only to the extent unsatisfied from the
  sale proceeds under §197.582**. §197.573 separately preserves certain restrictive covenants.
  **Never present a tax deed as an absolute clean-title rule.**
- **Title:** insurers will not write on a bare tax deed. Quiet title ~**$1,500–5,000+** and
  several months `[V]`; the deed is challengeable for **4 years** (§95.192) `[S]`.

---

## 5. OCCUPANCY & EVICTION

| | Timeline & cost |
|---|---|
| **CA former owner** | CCP §1161a — **3-day notice**, then UD. Uncontested ≈ **30 days** to sheriff lockout; **60–90 days if contested** `[V]` |
| **CA bona fide tenant** | PTFA + CCP §1161b — **90-day minimum notice**; the lease may have to be honored to term `[V]` |
| **LA RSO** | Covers units built **on or before 1 Oct 1978** — apartments, condos, duplexes, 2+ SFRs on one parcel, ADUs; ~**624,000 units / 118,000 properties** `[V]`. ⚠️ **Relocation amounts increase every 1 July — do NOT hardcode them.** The figures below are LAHD Chart A **effective 1 Jul 2025 – 30 Jun 2026 and are now EXPIRED**; LAHD Bulletin B already publishes the 2026–27 schedule. **Pull the current Chart A from housing.lacity.gov at runtime.** Expired reference values: eligible tenant $10,650 (<3 yrs) / $13,950 (3+ yrs or ≤80% AMI); qualified tenant (62+, disabled, or minor dependents) $22,450 / $26,550; mom-and-pop reduced $10,200 / $20,600. **Per unit, not per tenant; the higher applicable amount controls.** `[stale]` |
| **SF** | Rent Ordinance §37.9 covers most pre-13 Jun 1979 units; just cause + relocation required; **foreclosure is not a just cause.** AB 1482 statewide just-cause + one month's rent relocation applies to non-exempt units at 12+ months tenancy. |
| **FL former owner** | **Motion for writ of possession in the same foreclosure case** (§45.031) — far faster than eviction, typically a few weeks `[V]`. Squatters/unknown occupants → separate unlawful detainer or ejectment. Tenants: PTFA 90-day notice; Ch. 83 governs any tenancy you continue. Budget $500–2,500 legal + $500–5,000 cash-for-keys `[E]` |

**→ In LA and SF, an occupied rent-controlled building bought at auction may be economically
un-vacatable.** Resolve occupancy, build year and parcel unit count *before* bidding.

---

## 6. TAX STEP-UP ON TRANSFER

- **CA Prop 13:** a foreclosure sale to a third party **is a change in ownership** → reassessed
  to full cash value = your purchase price, new base year, +2%/yr `[S]`. A 1985 base year at
  $80k assessed becomes your $900k bid → **~1.0–1.25% of purchase price per year** in LA/SF
  (1% Prop 13 + voter bonds + direct assessments). Supplemental bill arrives 3–6 months post-close.
  **Never underwrite holding cost off the seller's current tax bill.**
- **FL:** the prior owner's 3% Save Our Homes cap and $25k + $25k homestead exemptions die on
  transfer; portability goes to the *seller*. As an investor you get the non-homestead 10%/yr cap
  (§193.1554/.1555) — **which also resets to full market value in the year after a change of
  ownership** `[S]`. Year-1 taxes = market value × millage. Miami-Dade effective ~**1.7–2.0%**
  `[S]`; Collier ~**0.7–0.9%** `[E]`. **A Miami condo bought at auction can see taxes double in
  year one.**

---

## 7. FLORIDA CONDO — MILESTONE & SIRS (the dominant 2026 value driver in Miami)

- **Milestone** Phase I at **30 years** from CO, **25 years if within 3 miles of the coastline**,
  then every 10 years. Phase II (destructive testing) if substantial deterioration is found;
  progress report within 180 days; repairs commenced within 365 days. `[V]`
- **SIRS** required for condos ≥3 stories; existing-building deadline was 31 Dec 2025; **full
  reserve funding mandatory from 1 Jan 2026; reserve waivers abolished** (post-SB 4D). HB 913
  (2025) raised the deferred-maintenance threshold $10k → $25k, allows funding reserves by
  special assessment, loan or LOC on a majority vote, and lets boards pause or reduce reserve
  contributions for up to two consecutive annual budgets after a milestone inspection. `[V]`
- **Observed Miami special-assessment magnitudes:** minor **$5–15k/unit**; significant deferred
  maintenance **$30–75k/unit**; major multi-system **$100k+/unit**; one 38-story Brickell tower
  levied **$45,000/unit.** `[V]`
- **Engine rule:** `Assessment_Reserve = max(known assessment, f(building_age, stories,
  coastal_distance, SIRS_status))`. **Auto-reject pre-1997 coastal Miami condos with no SIRS on
  file unless the discount to comps exceeds the p75 modelled assessment.**
- Context: Miami-Dade condos sit at **12 months supply, 125 DOM, median $400,000 (−1.5% YoY),
  47.5% cash** `[V]` — the market is repricing this risk in real time and the exit is slow.

---

## 8. INSURANCE (FL) — a go/no-go gate, not a line item

Flood: X zone **$400–1,200** · AE **$2,000–10,000** · VE **$5,000–20,000+**; South FL moderate
$800–1,500, high-risk coastal $3,000–12,000+; SW FL (Collier/Lee) moderate $900–2,000, high-risk
coastal $3,500–15,000+ `[V]`. Wind/HO separate.

**An unrepaired or unpermitted roof, an open claim, or a failed 4-point makes the property
uninsurable → unfinanceable → unsellable.** Gate it.

CA: wildfire/WUI exposure, FAIR Plan capacity constrained post-Palisades. Budget **$3,000–12,000/yr**
for a WUI-zone LA SFR `[E]`.

---

## 9. MARKET PARAMETERS — 2026

| | LA | SF | Miami-Dade | Collier / Naples |
|---|---|---|---|---|
| Median | **$1,069,418** (~flat) `[V]` | **$1.5M** (+11.1%) `[V]` | SFR **$685,000** (+3.8%) · Condo **$400,000** (−1.5%) `[V]` | All **$595,000** (+3.8%) · SFR **$750,000** (+7.1%) · Condo **$442,500** (−1.7%) `[V]` |
| $/sf | $631 (**−3.2%**) `[V]` | ~$1,030 (+8.2%) `[V]` | ~$400–450 SFR `[E]` | ~$390–430 `[E]` |
| DOM | 48 `[V]` | 25 `[V]` | SFR 88 · **Condo 125** `[V]` | 103 (condo 115) `[V]` |
| Months supply | ~3–4 `[E]` | ~2–3 `[E]` | SFR 4.8 · **Condo 12.0** `[V]` | 6.3 `[V]` |
| Cash share | ~25% `[E]` | ~25% `[E]` | **35.1% · condo 47.5%** `[V]` | high `[E]` |
| Cap rate (MF) | **5.8%** metro `[V]`; Westside 4.7–5.5 · Hollywood/NELA 5.0–6.0 · South LA/SFV 5.5–7.5 · DTLA 6.0–6.5+ `[V]` | **5.7%** `[V]` ($/unit **−10.4% YoY**, vacancy 4.0%, rent +5.8%) | ~4.75–5.5% `[E]`; luxury condo 3.5–4.5% `[V]`; STR net 4–6% after 25–30% mgmt `[V]` | ~5.0–6.0% `[E]` |

**Read-through:** SF has the tightest exit but the brutal transfer-tax cliff. LA is flat with
softening $/sf — **underwrite zero appreciation.** **Miami-Dade condo is a distressed-supply
market — exactly where 2026 auction supply comes from and exactly where exit risk is highest.**
Naples inventory fell 23.4% YoY but sits at 6.3 months and 103 DOM, with a seasonal Nov–Apr
selling window.

### Repair cost per sq ft

| Scope | LA | SF | Miami-Dade | Naples |
|---|---|---|---|---|
| Cosmetic | $25–45 | $35–55 | $20–35 | $25–40 |
| Medium | $55–90 | $70–110 | $45–75 | $50–85 |
| Full gut | $110–180 | $140–230 | $90–150 | $100–165 |

Add-ons: LA seismic retrofit $5–15k, ADU $180–300/sf, permits + plan check 3–6% of hard cost ·
**SF DBI permitting can add 6–12 months and 8–15% soft cost — treat SF permit risk as its own
line** · FL impact windows $70–120/sf of opening, roof $9–18/sf, **Chinese drywall gut $50–100/sf
(2001–2009 construction, esp. SE FL — effectively a total loss for a flip)**.

### Holding cost per month, per $500k of asset value

| | LA | SF | Miami-Dade | Collier |
|---|---|---|---|---|
| Debt service (hard money 9.99–12%, 1–3 pts, 12-mo IO, ≤90% LTC / ≤70% LTARV) `[V]` | $3,750–4,600 | $3,750–4,600 | $3,750–4,600 | $3,750–4,600 |
| Property tax (post-reassessment) | $460–520 | $480–500 | **$710–835** | $290–375 |
| Insurance (vacant / builder's risk) | $250–800 | $180–350 | **$450–1,300** + flood | **$400–1,400** + flood |
| HOA / condo | $250–700 | $400–1,200 | **$600–1,500** + assessment amortization | $500–1,200 |
| Utilities / security / lawn | $200–400 | $200–350 | $200–400 | $200–400 |
| **Total** | **$4,900–7,000** | **$5,000–7,000** | **$5,700–8,600** | **$5,100–7,900** |
| **Assumed hold** | **6–8 mo** (48 DOM + reno + 45-day §2924m) | **5–7 mo** | **SFR 7–9 · CONDO 10–14 mo** | **8–11 mo** (seasonal) |

---

## 10. CALIBRATION

Every week, backtest predicted MAO against actual clearing prices:
- **CA:** the AG's §2924m registry (`oag.ca.gov/residential-foreclosure-sales`) publishes winning
  bidder, bidder category and date-final by APN. Also Prime Recon / TMLF / NDSC "sold amount / sold to".
- **FL:** clerk sale results.

Write each observation to `state/calibration.jsonl`. Report **median deviation over the trailing
30 days** in every digest header. **If it exceeds 15%, say so plainly and recommend
recalibration — do not bury it in an appendix.**
