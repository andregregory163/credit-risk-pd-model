[credit_risk_README.md](https://github.com/user-attachments/files/27853313/credit_risk_README.md)
# Credit Risk Modelling — Probability of Default (PD)

Logistic regression PD model built in R on the German Credit dataset (1,000 loan applicants). Evaluates model performance using the metrics required under **Basel III IRB** and **IFRS 9**: AUROC, Gini coefficient, and KS statistic.

## Results

| Metric | Value | Benchmark |
|--------|-------|-----------|
| AUROC | **0.778** | ≥ 0.70 |
| Gini coefficient | **0.556** | ≥ 0.30 |
| KS statistic | **0.448** | ≥ 0.30 |
| Accuracy | 77.3% | — |

All three metrics exceed industry benchmarks for retail credit PD models.

## What the model does

**1. Feature selection via Information Value (IV)**  
Before fitting the model, each variable is scored using IV — a measure of how well it separates defaulters from non-defaulters. Variables with IV < 0.02 are dropped. The strongest predictor is checking account status (IV = 0.58), followed by credit history and loan duration.

**2. Logistic regression**  
A logistic regression estimates the log-odds of default as a linear combination of borrower characteristics. The output is a probability score P(default) ∈ (0, 1) for each borrower. Logistic regression is used rather than black-box alternatives because coefficients are auditable — a requirement under Basel IRB.

**3. Credit scoring**  
PD scores are converted to a credit score on a 600–800 scale (higher = safer), consistent with FICO-style conventions.

**4. Performance validation**  
- **AUROC**: Area under the ROC curve. Measures rank-ordering ability — does the model consistently assign higher PDs to borrowers who actually default? 0.5 = random, 1.0 = perfect.
- **Gini coefficient**: `2 × AUROC − 1`. Preferred metric in European credit risk (used by EBA, APRA). A Gini of 0.556 is strong for a retail portfolio.
- **KS statistic**: Maximum separation between the CDF of defaulters and non-defaulters. KS of 0.448 indicates good discrimination.

**5. Decile analysis**  
Borrowers are ranked into 10 equal groups by predicted PD. A well-calibrated model shows monotonically declining default rates from decile 1 (riskiest) to decile 10 (safest). Default rates range from 70% in decile 1 to 6.7% in decile 10 — clean monotonic ordering.

## Outputs

| Plot | Description |
|------|-------------|
| `plots/01_roc_curve.png` | ROC curve with AUROC and Gini annotation |
| `plots/02_score_distribution.png` | PD score density by default/non-default outcome |
| `plots/03_decile_default_rate.png` | Default rate by score decile |
| `plots/04_information_value.png` | Top 15 predictors ranked by IV |

## Regulatory context

Under **Basel III** (and Australia's APRA APS 113), banks using the Internal Ratings-Based (IRB) approach must estimate PD for each borrower internally. The AUROC and Gini thresholds used here reflect EBA/APRA guidance on minimum model discriminatory power. The decile analysis replicates standard model validation reporting used in credit risk teams.

**IFRS 9** (effective 2018) requires banks to estimate 12-month and lifetime PD for impairment provisioning — this model is a simplified version of that first-stage PD estimation.

## Data

[German Credit Dataset](https://archive.ics.uci.edu/ml/datasets/statlog+(german+credit+data)) — 1,000 loan applicants, 20 features, 30% default rate. Available in R via `caret::GermanCredit`.

## Dependencies

```r
install.packages(c("caret", "pROC", "ggplot2", "dplyr", "gridExtra"))
```

## Run

```r
Rscript credit_risk_model.R
```
