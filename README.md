# Credit Analytics of Bajaj Finance and Mahindra Finance

**Which of two large Indian NBFCs is the riskier credit, by how much, and why?**

A ten-year (FY2016-17 to FY2025-26), consolidated-basis comparison of **Bajaj Finance** and **Mahindra Finance**. The project combines classic ratio analysis with a transparent, stress-tested **Credit Risk Index** (0-100, higher = riskier), and frames the findings with the **7 Cs of credit**.

> Group project for the Financial Analytics course, MSc Economics (Data Analytics), Symbiosis School of Economics, Pune.
> Brief: *"Credit analytics of NBFCs that deal in the high-risk segment: Bajaj Finance and Mahindra Finance."*

---

## Headline results

| Metric (FY26) | Bajaj Finance | Mahindra Finance |
|---|---|---|
| AUM (₹ Cr) | 509,975 | 143,405 |
| AUM CAGR, FY17-FY26 | 26.8% | 12.9% |
| Gross NPA | 1.01% | 3.34% |
| Net NPA | 0.41% | 1.40% |
| Provision coverage (PCR) | 60.0% | 58.9% |
| Credit cost | 2.22% | 1.83% |
| RoA / RoE | 4.30% / 18.10% | 1.89% / 11.85% |
| Cost-to-income | 32.8% | 47.6% |
| CRAR (RBI floor 15%) | 21.55% | 18.80% |
| **Credit Risk Index** | **18.8** | **33.1** |

1. **Ranking is robust.** Mahindra scores riskier than Bajaj in 9 of 9 years, in all 10 tested scenarios (weights, thresholds, PCA, non-compensatory aggregation), and in at least 98% of 10,000 Monte Carlo draws in every year.
2. **The gap has narrowed.** The Mahindra-minus-Bajaj gap fell from 40.4 index points (FY21) to 14.3 (FY26). Mahindra's index is down 43.4 points from its FY20 peak; Bajaj's is up 14.3 points from its FY18 low.
3. **Why Mahindra still scores higher:** inherited asset quality (+10.3 points) and thinner loss-absorption buffers (+5.0), not current losses (loss experience is -1.0, because Bajaj's credit cost is now higher).
4. **Direction is more certain than magnitude.** The 5th-95th percentile bands overlap in FY19, FY21, FY25 and FY26.

All numbers are computed in the notebook from the input data; nothing is typed in by hand.

## Approach

| Part | What it does |
|---|---|
| 0-2 | Setup, data loading, audit of reported vs estimated values, analysis variables |
| A, B | Company-specific profiles (scale, asset quality, profitability, capital, portfolio mix) |
| C | Head-to-head comparison, FY21 (COVID) vs FY26 snapshot, DuPont decomposition of the RoE gap |
| D | Correlation analysis with safeguards: ex-FY21 re-run, year-on-year changes, Benjamini-Hochberg false-discovery check, lead-lag tests |
| E | Segment analysis: product mix, contribution to growth, concentration (HHI), reported-only segment GNPA |
| F | **Credit Risk Index** construction: 3 pillars, 7 inputs, fixed thresholds, equal weights |
| G | Index comparison, gap decomposition, scenarios, PCA weighting, Monte Carlo, validation |
| H | Findings, limitations, export to Excel |

**Credit Risk Index design**

- **Asset quality:** gross NPA, net NPA, Stage 2
- **Loss experience:** credit cost, loan losses / net total income
- **Loss absorption:** PCR, CRAR headroom over the RBI 15% floor
- Each input is scored 0 (best) to 100 (worst) between fixed thresholds, capped; pillars are averaged with equal weights. Unsecured share is deliberately left out (a driver of risk, not an outcome). Scoring uses the Ind AS years, FY18-FY26.

## Repository structure

```
.
├── NBFC_Credit_Analytics.ipynb      # full analysis (run top to bottom)
├── data/
│   ├── NBFC_merged_final.xlsx       # sheets: Panel, Segments, Fill_Log, Change_Log
│   └── Consolidated_Segment_Data_Clean.xlsx   # optional cross-check
├── figures/                         # charts saved by the notebook
├── outputs/
│   └── NBFC_analysis_outputs.xlsx   # index, pillars, scenarios, Monte Carlo, correlations
├── presentation/
│   └── Bajaj_vs_Mahindra_7Cs_Combined.pptx
├── requirements.txt
└── README.md
```

## How to run

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
pip install -r requirements.txt
jupyter notebook NBFC_Credit_Analytics.ipynb
```

Run all cells. The notebook looks for the input workbook in the notebook folder or `data/`. Charts are written to `./figures` and results to `./outputs`.

`requirements.txt`:

```
pandas
numpy
matplotlib
seaborn
scipy
openpyxl
jupyter
```

Python 3.9+. Monte Carlo uses a fixed seed (42), so results reproduce.

## Data

The input workbook has one row per company-year (`Panel`: 20 rows x 64 columns) and one row per segment-year (`Segments`: 154 rows), plus `Fill_Log` and `Change_Log` documenting every estimated or changed value.

- **Basis:** consolidated for both companies. Mahindra was switched from standalone to consolidated for comparability.
- **Sources:** [FILL IN: e.g. annual reports, investor presentations, with years and links]
- **Integrity checks (all pass):** net NPA <= gross NPA; CRAR >= Tier 1; opex/NTI recomputes within 0.1 pp; GNPA % recomputes within 0.25 pp of reported; segment shares sum to 100%.
- **What is estimated** (kept visible, flagged in `Estimated_Fields`): Bajaj net worth, total assets, borrowings, deposits, write-offs; one-time provisions; FY17 stage data for both. Headline ratios used in the index (GNPA, NNPA, PCR, credit cost, CRAR) are reported values.

## Limitations

- **Two companies, nine Ind AS years.** A descriptive case study, not evidence about the whole NBFC sector.
- **Thresholds are analyst judgement.** The ranking is stable to reasonable changes (Part G), but absolute index levels depend on them. Read ranking and trend, not exact levels.
- **Estimated inputs.** Bajaj's debt-to-equity is the least reliable ratio; the index does not use it.
- **Segment data.** Mahindra's segment GNPA and 47 of Bajaj's 100 segment-GNPA rows are allocated by the data compiler, so segment GNPA is analysed only where flagged "Reported". Bajaj's segment taxonomy changed in FY19, FY23 and FY25, and risk-tier and unsecured definitions are the compiler's classification.
- **Capital ratios** are used as given; source notes describe Mahindra's as standalone regulatory ratios.
- **Correlations and lead-lag tests are descriptive** (n = 8-9 per company), not causal.
- **Cash flow C is only partly covered:** ALM gaps and liquidity data are not in the dataset. Character, Conditions and Covenants are qualitative in the presentation.

## Possible extensions

Add 6-8 peer NBFCs for panel regressions and calibrated thresholds; add a funding and ALM pillar; add macro variables (repo rate, GDP growth, rural income).

## Team

Aadya, Akriti Kachroo, Jessica Dhabai, Muskan Pardhi, Nandini Sharma, Viraj Rank (analysis and notebook).

## License and disclaimer

[FILL IN: e.g. MIT for code; data subject to original publishers' terms.]

This is an academic exercise. It is not investment advice or a credit rating.