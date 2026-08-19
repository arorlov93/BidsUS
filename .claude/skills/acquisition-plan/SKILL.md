---
name: acquisition-plan
description: Build a per-property acquisition plan for an off-market or distressed target — select the legal pathway, price it, sequence the steps, and produce the compliance checklist. Use when the user asks how to buy a specific property, which route to take, or for a plan on a scouted candidate.
---

# Acquisition Plan

For one property. Read `agents/shared/COMPLIANCE.md` and `agents/shared/UNDERWRITING.md` first.

## 1. Establish the facts before choosing a route

Record owner and vesting · chain of title · open liens and their positions · tax status and
years delinquent · probate status · occupancy · physical condition with imagery dates ·
municipality (FL — this determines which lien search you order).

If the owner is an entity, resolve it through CA SOS bizfile or FL Sunbiz to a managing member
and registered agent. That is frequently a better and cleaner contact path than any skip trace.

## 2. Check the exclusions and flags — before writing a plan, not after

- **CA §2079.26 ZIP?** → stop. Unsolicited offers prohibited by any means; $25,000 per
  violation, misdemeanor exposure, 4-month seller cancellation right.
- **DNC (national / FL / internal)?** → mail only.
- **`ELDERLY_OWNER`?** → independent counsel or documented cooling-off period, documented
  fair-value analysis, no time pressure of any kind, principal sign-off. Cal. W&I §15610.30
  reaches **undue influence alone** — no fraud required.
- **`PROBATE_GRIEF`** (death within 12 months)? → delay contact, mail only, never cold-call.
- **`OCCUPIED` / `HOARDING`?** → it is not abandoned. Reclassify or drop.

## 3. Select the pathway — and say why not the others

| | Fits when | The thing that will surprise you |
|---|---|---|
| **A. Direct from owner** | Living, competent, locatable owner; clean title | Slowest, but the cleanest title and the best price when the owner's alternative is continued carry |
| **B. Estate / heir** | Decedent owner, unprobated or mid-probate | **Establish the personal representative's authority first.** Full IAEA → no court confirmation, no overbid, just a DE-165 notice with 15 days for heirs to object. Limited → ≥90% of the appraised value (§10309), §10300 publication, and a §10311 overbid of **bid + $1,000 + 5% of the excess over $10,000** ($500,000 → $525,500). Everything about your strategy changes on this one fact. |
| **C. Pre-foreclosure / short payoff** | Owner in default | **CA HESCA (§§1695–1695.17)** applies to 1–4 unit owner-occupied residences in foreclosure, from NOD to the scheduled sale: written contract in the seller's language, statutory disclosures, **5-business-day cancellation** extending to the day before the sale. Violations can be **criminal**; the contract is voidable for two years. Hand the sale-date clock to Auction Hunter. |
| **D. Tax lien / tax deed** | Multi-year delinquency, owner unreachable | FL: redeemable until the deed actually issues (§197.472). **§197.552 — governmental, municipal, county, special-district and CDD liens of record SURVIVE**, including Miami-Dade code fines at $250/day. Quiet title $1,500–5,000; deed challengeable 4 years. CA: 5 years to Power to Sell; redemption cuts off 17:00 the last business day before the sale; parties of interest may sue to set aside for **1 year**. ⚠️ **LA Chapter 8 agreement sales are statutorily closed to private investors.** |
| **E. Code enforcement / receivership** | Municipality already has an open nuisance case | Slowest and most political, but real in LA where the VBA program runs a statutory nuisance-abatement track under LAMC Div. 89. Requires counsel. |
| ~~F. Adverse possession~~ | **Never.** Excluded categorically. | CCP §325 (CA, 5 years + all taxes); Fla. Stat. §95.18 as tightened + HB 621 sheriff summary removal. Criminal exposure. |

## 4. Price it

Full itemized MAO per `agents/shared/UNDERWRITING.md`, with the **jurisdiction- and
pathway-correct** cost stack — a probate purchase, a tax deed and a clerk auction have
materially different buy-side costs on the same parcel. Apply the **+30–40% unknown-interior
contingency** unless the interior has actually been seen. State the walk-away price explicitly.

## 5. Sequence it

Steps with owners and dates, working backwards from whatever the binding constraint is —
a sale date, a redemption deadline, a probate hearing, an expiring code compliance window.
Name the professionals needed (title, counsel, municipal lien search, HOA estoppel) and their
lead times.

## 6. Compliance checklist for the chosen channel

Contact method and why it is lawful · DNC scrub evidence · calling window (FL: **08:00–20:00
local**, tighter than federal) · required disclosures · cancellation rights · documents in the
seller's language if `LANGUAGE_ACCESS`.

## Output

`out/plans/{parcel_id}.md`, Russian:

```
# План приобретения — {адрес}

## Резюме
{Что это, почему интересно, какой путь, сколько готовы заплатить, чего боимся.}

## Объект и состояние
{Снимки с датами съёмки. Признаки и их вес.}

## Собственник
{Кто, как установили, почему объект в таком состоянии.}

## Титул и обременения
{Что есть, что выживет при выбранном пути, со ссылкой на норму по каждому пункту.}

## Экономика
{Построчно. ARV · ремонт · вход · держание · выход · MAO · цена отказа.}

## Выбранный путь
{Почему этот. Почему не остальные — по строке на каждый.}

## Последовательность
{Шаги, ответственные, даты, обратный отсчёт от связывающего дедлайна.}

## Комплаенс
{Канал контакта, скрабы, раскрытия, флаги чувствительности.}

## Чего мы не знаем
{И что из этого меняет цену.}
```
