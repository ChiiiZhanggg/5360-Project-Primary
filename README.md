# MATH GR5360 Final Project — Primary Market (Group 3)

Columbia University, Department of Mathematics
**Course:** MATH GR5360 Mathematical Methods in Financial Price Analysis
**Instructor:** Prof. Alexey Chekhlov
**Term:** Spring 2026
**Due:** 2026-05-02 (final presentation in class, 30–40 min)

---

## Our Scope

Group 3 is split into a **primary-market sub-team** (this repo, 5 people) and a **secondary-market sub-team** (separate workstream). This repo covers **only the primary market** portion.

**Primary market assigned to us: `PL` — Platinum Futures (NYMEX-CME, USD).**

| Field | Value |
| --- | --- |
| Exchange | NYMEX-CME |
| Currency | USD |
| Contract Size | 50 troy oz |
| Point Value | **50 USD** per 1.0 price point (PV multiplier = 1, no ×100 trap) |
| Tick Size / Value | 0.10 / $5 |
| Round-turn Slippage | **148 USD** (from `TF Data.xls`, col V) |
| Trading Hours (local/NY) | 08:20 – 13:05 |
| Data Coverage | 2007-10-02 → 2026-04-10 (~18.5 yrs, 272 344 five-minute bars) |
| Data File | `PL-5minHLV/PL-5minHLV.csv` |

---

## Deliverables (from the project PDF, in order)

1. **Market Introduction.** Write-up on PL using Bloomberg screens (DES, GPO, CT — provided as `.gif` in the repo). Basic price chart, volatility profile, session structure.

2. **Random-Walk Statistical Tests.** Implement and run both on the PL time series:
   - **Variance Ratio test**
   - **Push-Response test**

   For each test, report the time-scale range(s) at which the series is inefficient, and whether the inefficiency is **trend-following** or **mean-reverting**.

3. **Strategy Implementation — "Channel WithDDControl".**
   Implement the trend-following breakout system from `BasicTradingSystems.doc` (pseudo-code) and `main.m` / `ezread.m` (Matlab reference).

   Preferred languages per the spec: C, C++, Java.
   Our plan: **Python + Numba** (team-wide Python fluency; Numba JITs to near-C speed).

4. **Walk-Forward Optimization.**
   - In-sample = **4 years** (≈ 57 000 five-minute bars for PL).
   - Grid: `ChnLen ∈ [500, 10000]` step 10 (951 points) × `StpPct ∈ [0.005, 0.10]` step 0.001 (96 points) = **91 296 grid points**.
   - Objective: **Net Profit / Worst Drawdown**.
   - After each IS window, apply the best (ChnLen, StpPct) to the **next quarter (3 months)** out-of-sample.
   - Roll forward quarter by quarter through all remaining data → stitched **OOS equity curve + trade-by-trade table**.
   - Maintain a **per-quarter optimal-parameters table**.
   - Bonus (spec-rewarded): implement an alternative search beyond full grid (coarse-to-fine, gradient-free, Bayesian, etc.).

5. **Performance Statistics on OOS curve + trade log:**
   Average return, standard deviation, Sharpe/Information Ratio, total trades, % winners, Net Profit / Worst DD, average winner, average loser, profit factor — plus any extras we think useful (recovery time, Ulcer, Calmar, etc.).

6. **Full-Sample In-Sample Comparison.**
   Re-run with the whole data set as in-sample; compare to walk-forward OOS to compute **decay coefficients** per metric.

7. **Sensitivity Sweep.**
   Repeat the walk-forward pipeline for T (in-sample years) = 1, 2, …, 10 and τ (OOS horizon) = 1, 2, 3, 4 quarters (or finer). Produce heatmaps + parameter-stability plots.

8. **Presentation.** 30–40 min PowerPoint covering everything above. All team members should have a speaking slot.

---

## Task Assignment (5 people)

Timeline assumes 11 calendar days remaining (2026-04-21 → 2026-05-02).

| Role | Owner | Responsibilities | Deliverables |
| --- | --- | --- | --- |
| **P1 – Market & Stats** | TBD | PL market intro, Variance Ratio test, Push-Response test, interpretation | Intro slides, two test implementations, time-scale conclusion |
| **P2 – Strategy Core Engine** | TBD | Implement Channel WithDDControl in Python + Numba; **validate against instructor HO reference** (`HO MatLab Test.xlsx`, `Equity 11500 0.019.pdf`, `Equity 12700 0.010.pdf`) | `strategy.py` with a clean `run_strategy(...)` API + unit tests |
| **P3 – Walk-Forward Framework** | TBD | Rolling 4-yr IS → next-quarter OOS, 91 296-point grid search with multi-core parallelism, per-quarter parameter log | `walk_forward.py`, OOS equity CSV, optimal-params table |
| **P4 – Performance Analytics** | TBD | All required and bonus statistics, decay coefficients (OOS vs full-IS), equity/DD plots | Stats tables, plots, analysis slides |
| **P5 – Sensitivity + Integration + Presentation** | TBD | T×τ sensitivity sweep, parameter heatmaps, final PPT assembly, dry-run coordination | Sensitivity results, final deck, rehearsal schedule |

**Cross-cutting:** everyone code-reviews at least one PR; everyone documents their own module in its docstring.

---

## Timeline

| Date | Milestone |
| --- | --- |
| **Apr 21 – 23** | P1 starts market intro + VR test. **P2 writes core strategy and matches HO reference numbers by end of Apr 23.** P3/P4 set up scaffolding, P5 drafts slide outline |
| **Apr 24 – 26** | P2 freezes core API. P3 runs walk-forward on PL end-to-end. P1 finishes Push-Response. P4 codes stats module (test on synthetic first) |
| **Apr 27 – 29** | P3 delivers full OOS results. P4 computes all metrics + decay. **P5 kicks off sensitivity sweep early** (long-running) |
| **Apr 30 – May 1** | Slide integration in P5's hands. At least one full dry run with all five presenters. Buffer for fixes |
| **May 2** | Final presentation in class |

---

## Repository Layout

```
.
├── README.md                         ← this file
├── .gitignore
├── BasicTradingSystems.doc           ← pseudo-code for Channel WithDDControl
├── PNL Formula.doc
├── Final Project MATH GR5360.pdf     ← project spec
├── Final Project Groups 2026.xlsx
├── main.m, ezread.m                  ← instructor Matlab reference
├── TF Data.xls                       ← futures-facts "Mendeleyev" table
├── TrCostsChina 03-07-2019.xls       ← (secondary-market reference, unused here)
├── Chinese Futures Markets.xlsx      ← (same)
├── HO-5minHLV.csv                    ← HO reference data (for validating P2)
├── HO MatLab Test.xlsx               ← expected numeric outputs to match
├── HO Optimization.xlsx
├── CO MatLab Test.xlsx
├── Equity 11500 0.019.pdf            ← reference HO equity curves
├── Equity 12700 0.010.pdf
├── PL DES.gif, PL GPO.gif, PL CT.gif ← Bloomberg screens for PL
└── PL-5minHLV/
    └── PL-5minHLV.csv                ← our primary market data
```

Planned additions as we build:

```
├── src/
│   ├── strategy.py          (P2)
│   ├── walk_forward.py      (P3)
│   ├── stats.py             (P4)
│   ├── sensitivity.py       (P5)
│   └── random_walk_tests.py (P1)
├── notebooks/               ← per-person exploratory notebooks
├── results/                 ← .gitignored; generated OOS CSVs, plots
└── slides/                  ← final PPT parts
```

---

## Getting Started

### 1. Clone

```bash
git clone https://github.com/ChiiiZhanggg/5360-Project-Primary.git
cd 5360-Project-Primary
```

### 2. Python environment

Python 3.11+ recommended.

```bash
python3 -m venv .venv
source .venv/bin/activate             # Windows: .venv\Scripts\activate
pip install numpy pandas numba matplotlib scipy openpyxl xlrd tqdm joblib
```

`.venv/` is already in `.gitignore`.

### 3. Quick sanity check (once strategy is in)

```bash
python -m src.strategy --data PL-5minHLV/PL-5minHLV.csv --chn-len 5000 --stp-pct 0.02
```

(Exact CLI will be finalized by P2.)

---

## Workflow

- **Branching:** one branch per role — `feat/p1-stats`, `feat/p2-strategy`, `feat/p3-walkforward`, `feat/p4-analytics`, `feat/p5-sensitivity`. Open a **PR into `main`** when ready; at least one teammate must approve before merge.
- **Commits:** short imperative subject (`add VR test`, `fix long-entry bar indexing`); body with context if non-trivial.
- **Standup:** one message per person in the group chat each morning — *yesterday / today / blockers*.
- **Do not commit:** generated result CSVs, `__pycache__/`, venv folders, IDE metadata (all handled by `.gitignore`).
- **Do not break `main`:** run at least a smoke test locally before pushing.

---

## Reference Materials in This Repo

- `Final Project MATH GR5360.pdf` — authoritative spec
- `BasicTradingSystems.doc` — pseudo-code for Channel WithDDControl (and an unused MR system)
- `main.m`, `ezread.m` — instructor Matlab reference implementation
- `HO MatLab Test.xlsx`, `HO Optimization.xlsx` — expected numeric outputs for P2 to match
- `Equity 11500 0.019.pdf`, `Equity 12700 0.010.pdf` — expected equity curves for two reference parameter sets on HO
- `TF Data.xls` — futures facts table; relevant rows include PL (row 58) and HO (reference)
- `PL DES.gif`, `PL GPO.gif`, `PL CT.gif` — Bloomberg description, chart, and contract table screens

---

## Graded On (per the spec)

> "you will not be judged on how much money your strategy makes or on how big is the value of your risk-adjusted return. Instead, you will be judged on how close your results are to the expected ones."

Translation: **correctness, faithful reproduction, thorough analysis** > profit. Don't tune away from the spec chasing Sharpe; do the process faithfully and interpret the results honestly.
