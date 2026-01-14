# Hockey IAR (Impact Above Replacement) — 2024–25 (5v5-first + Modular PP/PK)

This repository contains an end-to-end, auditable hockey evaluation project that produces a transparent **WAR-like** metric called **Impact Above Replacement (IAR)** using publicly available season-level tables (MoneyPuck-style data).

The framework is intentionally **5v5-first** (cleanest baseline) with **modular special teams** add-ons:
- **IAR_5v5** (5on5) — primary signal for comparability and stability  
- **IAR_PP** (5on4) — power play module  
- **IAR_PK** (4on5) — penalty kill module  
- **IAR_total = IAR_5v5 + IAR_PP + IAR_PK** — all-situations total (interpret with deployment context)

Optional (when contracts are matched):
- **IAR_total_per_M** — IAR wins per **$1M AAV** (value screening feature)

---

## Repository contents

### Notebooks
- **`IAR_modelling.ipynb`**
  - Load + validate inputs
  - Derive skater and goalie IAR by situation (5v5 / PP / PK)
  - Define replacement baselines (POC: 20th percentile thresholds among minutes-qualified players)
  - Merge contracts (AAV) and compute value features
  - Export results tables

- **`supplemental_iar_visualizations.ipynb`**
  - Portfolio-grade visualizations built from the modelling outputs
  - Top/Bottom leaderboards and decompositions (5v5 vs PP vs PK)
  - Quadrant views (5v5 vs special teams), lollipop charts with TOI cue
  - Cost/impact bubble plot (IAR_total vs AAV)
  - Team context mini-panels

### Write-up (PDF)
- **`IAR_Replacement_Write-up.pdf`**
  - Technical results summary, interpretation guidance, limitations, and recommended next-step (teammate-control upgrade)

---

## Recommended folder structure

```
.
├── IAR_modelling.ipynb
├── supplemental_iar_visualizations.ipynb
├── IAR_Replacement_Write-up.pdf
├── data/
│   ├── teams.csv
│   ├── skaters.csv
│   ├── goalies.csv
│   ├── lines.csv
│   ├── contracts.csv
│   └── MoneyPuckDataDictionaryForPlayers.csv
└── outputs/
    ├── iar_skaters_2024_poc.csv
    └── iar_goalies_2024_poc.csv
```

Notes:
- `data/` is typically **not** committed to GitHub unless you have redistribution rights. If you exclude it, add a `.gitignore` entry and include instructions for where to source the data.
- `lines.csv` is not required for the current on/off POC, but is retained for the next upgrade (see below).

---

## How to run

### 1) Environment
```bash
python -m venv .venv
source .venv/bin/activate   # mac/linux
# .venv\Scripts\activate  # windows

pip install -U pip
pip install pandas numpy plotly
```

### 2) Data
Place the required CSVs in `data/` (or update the paths in the notebooks).

### 3) Execute notebooks
1. Run **`IAR_modelling.ipynb`** to generate `outputs/` tables  
2. Run **`supplemental_iar_visualizations.ipynb`** to reproduce the figures

---

## Method overview (high level)

### Skaters
For each situation (5v5 / PP / PK), skater impact is estimated via an **on-ice vs off-ice expected-goals differential** approach:
- Convert on-ice and off-ice xG differentials to per-60 rates
- Compute an on/off impact rate and scale by minutes played
- Subtract a replacement baseline (POC: percentile-based within F vs D groups)
- Convert xG-like units to wins via a tunable constant

### Goalies
Goalie impact uses a simple **GSAx-style** construct per situation:
- `GSAx = xGoals - goals`
- Subtract replacement baseline among minutes-qualified goalies
- Convert to wins using the same scaling constant

---

## Interpretation notes
- **5v5 is the backbone** for comparing players across teams and roles.
- PP/PK modules reflect role-specific value; totals are best interpreted alongside deployment.
- This is an on/off approach: results can still reflect teammate and usage effects, which motivates the next upgrade.

---

## Next upgrade (recommended)
Use `lines.csv` (or shift-level data) to fit a **ridge regression / RAPM-lite** model that controls for linemates and deployment, while preserving the same reporting structure:
- 5v5-first + modular PP/PK + IAR_total

---

## License / attribution
If you publish derived tables or results, ensure you comply with the source data’s terms of use and provide appropriate attribution.
