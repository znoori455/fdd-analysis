# Audit: SBA Dashboard & Franchise Comparison Scorecard

**Date:** 2026-11-05
**Scope:** The dashboard artifact ([claude.ai/artifact/JU4ZeBrx6decSBCj6eVTTq](https://claude.ai/artifact/JU4ZeBrx6decSBCj6eVTTq), copy in `sba-dashboard/`) and `Franchise_Comparison_Scorecard.xlsx`, audited against the raw SBA FOIA files in `SBA-Data/`, the FDD PDFs in this repo, and current public rate data.

**Verdict: the dashboard data is correct and the rankings are accurate.** Every dashboard statistic I rebuilt from the raw SBA files reproduced exactly; every score and rank in the Excel recomputed with zero discrepancies; all FDD figures spot-checked (8 brands in depth) matched the PDFs. The only real fragility is one the workbook already flags: Visiting Angels' #2 rank rests on a low-confidence profit estimate and is statistically tied with Valvoline at #3.

---

## 1. Dashboard artifact vs. raw SBA FOIA data — VERIFIED

Method reproduced: default rate = charge-offs ÷ resolved loans (PIF + CHGOFF) for 1991–2019 vintages, ÷ funded loans (excl. cancelled/commitments) for 2020–present; risk index = actual defaults ÷ defaults expected at each decade's all-loan baseline.

- **All five baselines exact**, including the two the Excel uses:
  **31.22%** 2005–08 recession vintages (99,527 / 318,755) and **7.99%** 2010s (34,554 / 432,275). Also 12.11% (1990s), 23.85% (2000s), 2.49% (2020s to date).
- **Per-brand table (`brands.csv`): 37 brands × 14 fields reproduced exactly** — loans, defaults and rates per decade, 2005–08 vintages, risk indexes, 2020s median loans, purchase-loan counts and medians. Where my first pass differed, the dashboard was right: it correctly captured SBA name variants "SPORTS CLIPS" (479 loans) and the misspelled "LITTLE CEASAR'S PIZZA" (317 loans).
- **Industry proxies and factors exact from raw NAICS data**: plumbing/HVAC risk index 1.01 (1-800-Plumber proxy), car wash 0.91 (Tommy's Express proxy), oil change 0.96, general auto repair 0.88, and every "buy-existing risk factor" used on the Dashboard Rankings tab (auto 1.31, child care 0.19, restoration 1.35, senior care 0.42, collision 0.42, oil change 0.53…).
- **Methodology footnotes verified**: 24.1% coded-only 2000s baseline (I get 24.08%), 7.3% purchase-loan 2010s rate (7.35%), 2.0% purchase 2020–23 (2.00%).

Trivial quibbles (no downstream effect):

1. Valvoline's 2020s count (9 loans, median $1.1M) includes 2 "Express Care Sales Agreement" + 1 "Supply Agreement" loans that are not VIOC franchise units (strictly 6 loans, median $975K). Either way it is under the 20-loan threshold, so the FDD cost was kept — no ranking impact.
2. Little Caesars shows 0 loans in the 1990s; the raw data has 22 resolved (5 defaults) under the misspelled name. Affects only the `_w90` columns, which the scorecard does not use (w90 risk index would be ~0.54 vs 0.48 shown).
3. The 1991–99 baseline row differs from my count by 248 loans out of ~291K; the rate is identically 12.11%.

## 2. Excel scorecard — zero math errors

Recomputed independently (Python) from the Data tab's raw inputs:

- All derived columns correct: investment midpoints, payback, unit CAGR, exit rates, fee midpoints.
- **All 8 category scores × 39 brands, all weighted totals, all ranks: 0 discrepancies.** Formulas implement the Scoring Method tab exactly.
- **Dashboard Rankings tab: 0 discrepancies** — shrinkage toward the 7.99% baseline, blended risk index, new-vs-existing path selection, confidence labels, and the Top-20 order all recompute exactly; every dashboard-sourced input matches `brands.csv`.
- Transcription from dashboard to the Data tab is exact, with three deliberate n/a judgment calls on tiny samples: Take 5 risk index (0.0 on 19 loans → scored as "too few loans"/7), Biggby 2005–08 rate (50% on 4 loans → n/a), Supercuts (0% on 2 loans → n/a). Defensible, but note the Scoring Method text says "judgment only if **no** 2005–08 loans" — in practice ≤4 loans was treated as none (helps Biggby, hurts Supercuts).

## 3. FDD spot checks — 8 brands, all matched

| Brand | Checked | Result |
|---|---|---|
| Christian Brothers | Item 7 $515,250–$650,400; Item 19 median net sales $2,702,696 (302 stores), median Total Owner Benefit $282,274, bottom/top-20% avg $126,280 / $554,464; Item 20 265→326 (2023–25), 302 start of 2025, 0 closures | all exact |
| Visiting Angels | Item 7 $125,460–$171,150; Item 20 532→541, exits 9/539 (6 term + 1 non-renew + 2 ceased); Item 6 fees 3.0–3.5% royalty + 2.0–2.5% co-op ad = 5.5–6.0% | all exact |
| Valvoline | Item 7 $192,375–$639,550 leased / $1.77M–$3.48M owned; Item 19 median $1,704,870 (891 centers); FY2025 exits 2/962 (6 franchisor reacquisitions properly excluded) | all exact |
| Two Men and a Truck | Item 7 metro $146,950–$512,450, mod $92,100–$251,500; Item 19 median gross sales $2,072,481; fees 6% royalty + 1% ad + 1–1.5% tech | all consistent |
| Take 5 | Item 7 $912,248–$2,053,642 | exact |
| Subway | Item 20: 20,576→18,773 (2023–25), 19,502 start of 2025; 2025 exits 4 term + 46 non-renew + 1,026 ceased = 1,076 | exact |
| Jiffy Lube | Item 7 $211,000–$510,000 | exact |
| Home Instead | Item 7 $92,640–$350,550 | exact |

Christian Brothers' perfect SBA record (0 defaults in 262 loans since 2000) is real in the raw FOIA data; its #1 rank is solid, with a 0.54-point gap to #2.

## 4. Financing & ROI tab and external assumptions

- **Prime 7.00%** — confirmed current: Fed H.15 shows the bank prime loan rate at 7.00% through Nov 3, 2026 (last changed Sep 17, 2026). Rate sites still showing 6.75% are stale.
- **504 25-yr debenture 6.97%** — matches the Oct 8, 2026 debenture sale.
- 7(a) max variable spreads (prime + 3% / 4.5% / 6% by loan size) match SBA rules; $5M cap correct.
- The Christian Brothers financing row recomputed end-to-end (down payment, financed guarantee fee, 10-yr PMT, DSCR 4.15, stress test, exit value with the 50% franchisor resale split, IRR cash-flow vector): all correct.

## Remaining caveats (not errors)

1. **Ranks #2–#3 are a statistical tie**: Visiting Angels 7.964 vs Valvoline 7.927. VA's $108K SDE is an online-margin estimate on an estimated $900K young-territory revenue; the Dashboard-Adjusted tab already flips the two. Treat them as tied pending franchisee interviews.
2. The ~10 "Low confidence" SDE figures (Servpro, Jiffy Lube, Jersey Mike's, …) cannot be audited against any disclosure — those FDDs have no profit data. The confidence labels represent this honestly.
3. FDD inputs for the 31 brands not deep-checked were verified for internal consistency only; Item 12/19 judgment scores (territory, transparency) are inherently subjective — nothing I reviewed looked misjudged.

*This audit is a data check, not financial advice.*
