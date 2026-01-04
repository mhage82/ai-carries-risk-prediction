# Root Caries Risk Prediction (NHANES)

## Overview

This project develops an **interpretable machine learning model** to identify individuals at **elevated risk of root caries** using publicly available health survey data from NHANES.

The goal is **early risk identification (screening)**, not diagnosis. The model prioritizes **recall (sensitivity)** to reduce missed high-risk cases, while maintaining reasonable precision.

---

## Problem Statement

Root caries is a dental condition that disproportionately affects older adults and individuals with certain behavioral and socioeconomic risk factors. Early identification of high-risk individuals can enable preventive interventions before irreversible damage occurs.

This project asks:

> *Can demographic, behavioral, socioeconomic, and dental history features be used to identify individuals at elevated risk of root caries?*

---

## Data Source

- **Dataset:** National Health and Nutrition Examination Survey (NHANES)
- **Provider:** U.S. Centers for Disease Control and Prevention (CDC)
- **Survey Cycle:** 2017 – March 2020 (pre-pandemic)

NHANES is a cross-sectional survey combining interviews, physical examinations, and laboratory data from a nationally representative sample.

---
### Raw Data Files Used

The following NHANES XPT files were used directly in this project:

- **Dental Examination Data**  
  https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_OHXDEN.XPT

- **Demographics Data** (age, sex, socioeconomic status)  
  https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_DEMO.XPT

- **Smoking Questionnaire Data**  
  https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_SMQ.XPT

All files correspond to the **2017–March 2020 NHANES cycle** and are publicly available from the CDC.

---
## Target Variable

### `has_root_caries`

A binary outcome indicating the presence of **root caries**, derived from the NHANES dental examination variable:

- `OHXRCAR == 1` → Root caries present
- `OHXRCAR != 1` → Root caries not present

Root caries was selected as the target because it represents a **specific, clinically meaningful outcome** and avoids target leakage from tooth-level decay indicators.

---

## Feature Engineering

The final model uses the following features:

### Demographics
- **Age (`RIDAGEYR`)** – strongest predictor of root caries risk
- **Sex (`is_female`)** – binary encoding

### Dental History
- **Number of missing teeth**
- **Number of filled teeth**

These capture long-term oral health history without directly encoding current decay.

### Behavioral
- **Smoking status** – strong independent risk factor

### Socioeconomic
- **Income-to-poverty ratio (`INDFMPIR`)** – proxy for access to care and long-term preventive behavior

#### Features intentionally excluded
- Tooth-level decay indicators
- Composite caries labels derived from exam data

These were excluded to **prevent target leakage** and preserve model validity.

---

## Model Choice

- **Algorithm:** Logistic Regression
- **Rationale:**
  - Interpretable coefficients
  - Well-suited for low-prevalence outcomes
  - Commonly used in healthcare risk modeling
  - Enables odds-ratio interpretation

Model interpretability was prioritized over complexity.

---

## Handling Class Imbalance

Root caries is relatively uncommon in the dataset. Instead of resampling, the model uses:

- **Probability threshold tuning** to control the recall–precision tradeoff
- **ROC-AUC** to evaluate overall discrimination independent of threshold

---

## Model Performance

### Final Operating Threshold
- **Threshold:** 0.10  
- Selected to prioritize recall for screening use

### Performance Metrics

| Metric | Value |
|------|------|
| ROC-AUC | **0.73** |
| Recall (root caries) | **~70%** |
| Precision (root caries) | **~19%** |
| Accuracy | ~65% |

At this threshold, the model identifies approximately **7 out of 10 individuals with root caries**, accepting additional false positives as appropriate for a screening application.

---

## Interpretation (Odds Ratios)

Model coefficients were converted to **odds ratios** to enable interpretation.

Examples:
- **Smoking:** Substantially increases odds of root caries
- **Age:** Odds increase incrementally with each additional year
- **Socioeconomic status:** Higher income-to-poverty ratio is protective

Odds ratios reflect **associations**, not causation.

---

## Intended Use

This model is intended for:
- Risk stratification
- Screening and prioritization
- Exploratory and educational purposes

### Not intended for:
- Clinical diagnosis
- Individual treatment decisions
- Causal inference

---

## Limitations

- Cross-sectional data (no temporal prediction)
- Self-reported behavioral variables
- No dietary or salivary measurements
- Observational associations only

These limitations are inherent to NHANES and are acknowledged explicitly.

---

## Repository Structure

```
notebooks/
  01_eda.ipynb
  08_final_feature_engineering.ipynb
  09_final_model_root_caries.ipynb

models/
  root_caries_logreg.joblib
  root_caries_metadata.joblib

data/
  raw/
  processed/
```

---

## How to Run

1. Create a virtual environment
2. Install dependencies
3. Run notebooks in order:
   - `08_final_feature_engineering.ipynb`
   - `09_final_model_root_caries.ipynb`

The final notebook trains the model, evaluates performance, and saves reusable artifacts.

---

## Key Takeaways

- Careful feature selection and leakage prevention matter more than model complexity
- Demographic, behavioral, and socioeconomic features provide complementary signal
- Threshold selection should reflect **policy goals**, not accuracy alone
- Logistic regression remains a powerful and interpretable tool for healthcare ML

---

## Author

Built as a portfolio project to demonstrate applied machine learning, data hygiene, and interpretability in a healthcare context.