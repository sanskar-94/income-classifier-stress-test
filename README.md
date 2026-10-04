# Stress-testing an income classifier

Sanskar Awasthi

Two simple models (OLS and logistic regression) that predict whether a person earns more than $50K a year, built on the **Adult Census Income** dataset (UCI). They are then stress-tested for threshold effects, Simpson's paradox, omitted-variable bias, confident failures and fairness trade-offs. The train/test split is 70/30 with `random_state = 138`.

## What is in here

| Path | What it is |
|---|---|
| `income_classifier_stress_test.ipynb` | The full analysis. Every number in the report comes from this notebook. |
| `report.pdf` | The written report. |
| `data/` | `adult.data`, `adult.test` and `adult.names` from the UCI repository. |
| `outputs/tables/` | Every table as a CSV file. |
| `outputs/figures/` | Every chart as a PNG file. |
| `outputs/key_numbers.json` | The headline numbers quoted in the report. |

## How to run it

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace income_classifier_stress_test.ipynb
```

Or open the notebook in Jupyter and use Kernel > Restart & Run All. If the `data/` folder is empty, the notebook downloads the files from UCI first. It takes well under a minute. The results are deterministic, so a re-run gives exactly the same tables, figures and numbers.

## What the notebook does

1. **Preprocessing**: joins the two UCI files (48,842 rows) and drops 52 duplicates. Missing values get mode imputation plus a missing-value flag, because rows with missing values earn less (13.2% vs 24.8% above $50K). Rare categories are merged, the capital gain and loss columns are log-transformed, and the numeric columns are standardised using training-set statistics only.
2. **Part 1**: OLS (linear probability model) vs logistic regression. Compares a median cut-off on OLS with a 0.5 cut-off on the logit.
3. **Part 2.1**: Simpson's paradox. The sex coefficient is positive overall but negative among married people.
4. **Part 2.2**: omitted-variable bias from dropping marital status.
5. **Part 2.3**: confident wrong predictions (p ≥ 0.75 or p ≤ 0.25) and a random sample of 50 of them.
6. **Part 2.4**: demographic parity, equalized odds and predictive parity for women vs men at thresholds 0.05 to 0.95.
7. **Extra checks** used in the written answers: a matched pair of real test-set people, a residual check against the dropped `relationship` column, and accuracy by sex.
