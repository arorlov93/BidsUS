---
name: underwrite-lot
description: Underwrite a single auction lot or off-market property — establish lien position, build the itemized MAO, apply jurisdiction-correct costs and statutory risks, and return a bid ceiling with a walk-away price. Use when the user asks what a specific property is worth, what to bid, or to check a deal.
---

# Underwrite a Lot

Read `agents/shared/UNDERWRITING.md` and `config/cost-model.yaml` first. This skill applies
them to one property.

## Inputs needed (ask only for what you cannot derive)

Market, parcel identifier, channel, sale date, opening bid, and whether the interior has been
inspected. Everything else you should be able to pull.

## Sequence

### 1. Establish what is actually foreclosing — before anything else

CA: pull the deed of trust referenced by the NOD/NTS. FL: read the **complaint and the final
judgment** on the clerk docket. This decides more deals than every other factor combined.

- First position → proceed.
- Junior position → the bid ceiling is the **lesser** of MAO and `(senior payoff + arrears + costs)`.
- Cannot establish → **gate G1, stop.** Do not discount your way past an unknown.

FL only: verify every junior lienholder was **named AND served**. An omitted junior survives
the sale. Check the docket returns of service, not just the lis pendens.

### 2. ARV

≥3 closed comps, ≤6 months, ≤1 mile, same use code, ±25% GLA. Record `comp_count` and
`comp_dispersion`. <3 comps or >25% dispersion → gate G10, downgrade to WATCH. You do not have
an ARV; you have a guess.

Anchor `SellDiscount` to actual list-to-sale for the market (LA 99.7%, MDC condo 93%, Naples 94.5%).

### 3. Repairs

Cost/sf table by market and scope from `config/cost-model.yaml`. Contingency **+15% with
interior access, +30–40% without** — and without is the normal case at auction.

FL add-ons that swing the number: impact windows, roof, and **Chinese drywall** (2001–2009
SE FL construction — a full gut of drywall, wiring and HVAC at $50–100/sf; effectively a total
loss for a flip).

### 4. Buy-side costs — use the right jurisdiction

Never one national constant. CA trustee sale ≈ **0.8–1.2%**; FL clerk sale ≈ **2.3–2.8%**.
The FL delta is the clerk registry fee (3% of first $500 + 1.5% of balance ⇒ ~1.5% of bid) plus
doc stamps. **Miami-Dade condo pays 1.05% doc stamps; Miami-Dade SFR pays 0.60%** — the surtax
does not apply to a single-family dwelling.

### 5. What survives the sale

Work `agents/shared/UNDERWRITING.md` §4 line by line. The three that most often get missed:

- **FL HOA/condo:** a third-party bidder is **jointly and severally liable for ALL** pre-transfer
  unpaid assessments. The 12-month / 1% safe harbor is for the **first mortgagee only**.
  $36k delinquency → the bank would pay $4k; you pay $36k.
- **FL unrecorded municipal charges** — not in the official records at all. Order the municipal
  lien search, per municipality.
- **IRS §7425** — was 25 days' notice given? Defective → the FTL survives. Good → 120-day IRS
  redemption at your price + 6%/yr, so no safe resale for four months.

### 6. Holding costs

Per `config/cost-model.yaml`. Two step-ups people forget:

- **CA Prop 13** — a third-party foreclosure purchase *is* a change in ownership. Reassessed to
  your bid. Never underwrite off the seller's current bill.
- **FL** — homestead and Save Our Homes die on transfer; the non-homestead 10% cap also resets
  to full market value the year after. Miami-Dade taxes can double in year one.

Hold months: LA 6–8 (48 DOM + reno + the 45-day §2924m window), SF 5–7, **MDC condo 10–14**
(125 DOM, 12 months supply), Collier 8–11 with a seasonal Nov–Apr window.

### 7. Statutory risk haircuts — show them, don't bury them

CA §2924m 45-day takeaway on every 1–4 unit trustee purchase · FL 10-day objection window ·
FL tax-deed pre-sale redemption · CA tax-deed 1-year set-aside · unknown interior.

### 8. Occupancy

Resolve it before bidding. **LA RSO** (built on or before 1 Oct 1978) relocation runs roughly **$10k–$27k per unit**,
higher amount controls. **Pull the current LAHD Chart A at runtime — it resets every 1 July.** **SF §37.9 — foreclosure is not a just
cause.** An occupied rent-controlled LA/SF building can be economically un-vacatable.

### 9. FL condo ≥3 stories — Milestone / SIRS

Milestone at 30 years from CO, **25 if within 3 miles of coast**. SIRS required, full reserve
funding mandatory from 1 Jan 2026, waivers abolished. Observed assessments $5k–$100k+/unit.
**Auto-reject pre-1997 coastal Miami condos with no SIRS on file unless the discount to comps
exceeds the p75 modelled assessment.**

### 10. Insurability (FL)

Failed 4-point, unpermitted or unrepaired roof, or an open claim → uninsurable → unfinanceable
→ unsellable. This is a go/no-go gate.

## Output

A Russian-language memo:

- **Вывод** — bid ceiling, walk-away price, one sentence on why
- **Разбор цены** — every line with its statutory or config basis and provenance tag
- **Что выживает после торгов** — with the statute for each call
- **Риски** — ordered by cost of being wrong, each with its haircut
- **Чего мы не знаем** — the gaps, and which of them would change the number

Never present an estimate as verified. Never state a legal conclusion as fact.
