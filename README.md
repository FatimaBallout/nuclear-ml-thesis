# Combination of Non-Destructive Assay and Machine Learning Techniques to Detect Replaced Pins in Spent Fuel Assemblies

**Author:** Fatima Ballout  
**Degree:** Master of Science in Physics of Data  
**University:** Università degli Studi di Padova  
**Year:** 2023–2024  
**Supervisor:** Dr. Marcello Lunardon  
**Mentor:** Dr. IR Riccardo Rossa | **Co-Mentor:** Dr. Alessandro Borella

---

## Overview

This repository contains all the code, figures, and results from the Master's thesis. The work combines Monte Carlo simulation data (MCNP) with machine learning to detect fuel pin diversion in spent nuclear fuel assemblies using non-destructive assay (NDA) techniques.

**Goal:** Given neutron flux measurements from a PDET detector, classify whether a fuel assembly has been tampered with (replaced pins) and predict how many pins were replaced.

---

## Dataset

- **15,984 simulation files** (MCNP `.o` output files → converted to `.parquet`)
- **2,744 intact** assemblies (MODab_Flag = 0)
- **12,240 modified** assemblies with replaced stainless steel dummy pins (MODab_Flag = 1)
- **Features:** Neutron flux per cell × cross-section for 3 isotopes → 65 cells × 3 isotopes = 195 features per file
- **Targets:**
  - Classification: `MODab_Flag` (0 = intact, 1 = modified)
  - Regression: `Total Replaced Pins` (0–130)

**Isotope scenarios analyzed:** Combined (U238 + U235 + Pu239), U238-only, U235-only, Pu239-only

---

## Models

| Model | Type |
|---|---|
| Extra Trees | Classifier + Regressor |
| Random Forest | Classifier + Regressor |
| K-Nearest Neighbors | Classifier + Regressor |
| Light Gradient Boosting Machine (LGBM) | Classifier + Regressor |

---

## Classification Results (Accuracy)

| Model | Combined | U238 | U235 | Pu239 |
|---|---|---|---|---|
| Extra Trees | 99.26% | **99.80%** | 98.83% | 98.93% |
| Random Forest | 99.09% | 99.40% | 98.89% | 98.83% |
| LGBM | 98.89% | 97.62% | 97.28% | 97.28% |
| KNN | 96.88% | 95.27% | 96.11% | 96.34% |

All models achieve AUC > 0.99 across all scenarios.

---

## Regression Results (R²)

| Model | Combined | U238 | U235 | Pu239 |
|---|---|---|---|---|
| LGBM | **0.9785** | 0.9429 | 0.9499 | 0.9538 |
| Extra Trees | 0.9782 | **0.9612** | **0.9626** | **0.9673** |
| Random Forest | 0.9754 | 0.9519 | 0.9560 | 0.9618 |
| KNN | 0.9313 | 0.8898 | 0.9249 | 0.9252 |

---

## Repository Structure

```
nuclear-ml-thesis/
├── notebooks/
│   ├── combined_analysis.ipynb       # Combined U238 + U235 + Pu239 — all models + outputs
│   ├── uranium238_analysis.ipynb     # U238-only — all models + outputs
│   ├── uranium235_analysis.ipynb     # U235-only — all models + outputs
│   ├── plutonium239_analysis.ipynb   # Pu239-only — all models + outputs
│   ├── thesis_analysis.ipynb         # Full pipeline in one notebook
│   └── ML_SF_exploration.ipynb       # Original single-file data exploration
├── figures/
│   ├── exploration/
│   │   ├── assembly_layout.png
│   │   ├── data_overview.png
│   │   ├── energy_vs_flux_cell1011.png
│   │   └── final_summary.png
│   ├── classification/
│   │   ├── Combined/   # Extra_Trees, Random_Forest, KNN, LGBM (confusion matrix + ROC)
│   │   ├── U238/
│   │   ├── U235/
│   │   └── Pu239/
│   └── regression/
│       ├── Combined/   # Extra_Trees, Random_Forest, KNN, LGBM (prediction error + residuals)
│       ├── U238/
│       ├── U235/
│       └── Pu239/
├── results/
│   └── thesis_results.xlsx        # All metrics: accuracy, F1, AUC, MAE, RMSE, R²
├── requirements.txt
└── README.md
```

---

## How to Run

```bash
# Install dependencies
pip install -r requirements.txt

# Launch the notebook
jupyter notebook notebooks/thesis_analysis.ipynb
```

The notebook loads pre-processed `.parquet` files. Update `DATA_DIR` in the notebook to point to your local parquet files.

---

## Key Findings

- **Extra Trees Classifier** achieves the highest accuracy (up to 99.80% for U238) and near-perfect AUC (0.9997), making it the best classifier for detecting replaced pins.
- **LGBM Regressor** achieves the best overall R² (0.9785) for predicting the number of replaced pins in the combined scenario.
- **U238-only** features yield the strongest classification performance, while **combined** features give the best regression performance.
- All models significantly outperform random baseline, confirming that neutron flux measurements combined with cross-section data carry strong discriminative information for nuclear safeguards.