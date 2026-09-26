# Beacon Community Capital - First-Pass Loan Sorter

## Description

This school project builds a first-pass sorter for Beacon Community Capital's historical loan applications. A logistic regression classifier estimates how likely an application is to resemble those Beacon's officers previously approved. It predicts **historical officer decisions**, not repayment, default, or creditworthiness. Deployment to a live lending system is outside scope.

| Predicted approval probability | Sorting group | Handling |
| --- | --- | --- |
| ≥ 0.80 | Likely approval | Eligible for an expedited workflow; an officer signs off on the final decision |
| 0.20 to < 0.80 | Borderline | Mandatory loan-officer review |
| < 0.20 | Likely rejection | Mandatory loan-officer review; never reject automatically |

## Data

- **File:** `loan_applications.csv` in the repository root. No data is downloaded from an external source.
- **Rows:** Each row is a historical loan application. The raw file has 232 rows and 9 columns, including duplicates.
- **Columns:** `applicant_id`, `fico_score`, `annual_income`, `loan_amount`, `loan_term_months`, `employment_status`, `years_employed`, `savings_balance`, and `approved`.
- **Target:** `approved` records the historical decision (Approved or Rejected). `applicant_id`, `approved`, and its numeric copy are excluded from model inputs.
- **Known raw-data issues:** Exact duplicates, missing FICO scores, money values stored as text, and impossible values. The notebook will document and handle them.

## Project structure

| File | Purpose |
| --- | --- |
| `loan_applications.csv` | Supplied historical applications |
| `README.md` | Overview, setup, and scope |
| Project notebook (`.ipynb`, to be added) | Analysis, figures, model, and explanations |
| Exported notebook (`.html`, to be added) | Submission copy with code and executed outputs |
| Codex prompt trail (to be added) | Prompts and key development decisions |

## How to run

1. Open the repository folder in VS Code and select a Python/Jupyter kernel. The course setup uses Python 3.13.
2. If the libraries are missing, create an environment and install them:

   ```bash
   python3.13 -m venv .venv
   source .venv/bin/activate
   python -m pip install pandas numpy matplotlib seaborn scikit-learn ipykernel nbconvert
   ```

3. Keep `loan_applications.csv` in the repository root. Open the notebook once added and run its cells from top to bottom.
4. Inspect the cleaning counts, charts, and test results, then export the executed notebook to HTML.

## Four stages

1. **Explore and clean:** Inspect the raw data and missing values. Convert money columns to numbers, remove exact duplicates and specified impossible-value rows, then fill blank FICO scores with the median. Document each change.
2. **Business insights:** Chart approval counts, FICO distribution, approval rate by employment type, FICO by decision, and correlations. Explain the observed patterns and fairness concerns.
3. **Model training:** Encode the historical decision as 0/1, select inputs without leakage, make a stratified 80/20 train/test split with a fixed seed, and fit logistic regression. Show raw and scaled coefficient charts.
4. **Evaluation:** Report held-out classification results; compare accuracy with the FICO ≥ 660 rule and an always-approve baseline; calculate approval probabilities and count test applications in the three sorting groups.

## Output and findings

The completed notebook will show its charts and tables inline. **Model results are pending.** Update this section with the actual test metrics, comparisons, sorting counts, and findings after the notebook runs. No model performance is claimed yet.

## Additional project notes

- Historical officer decisions may contain inconsistency or bias. Income, savings, and employment may also disadvantage some groups or act as proxies for protected characteristics. Excluding explicit protected traits alone does not establish fairness.
- An approval probability measures resemblance to historically approved applications; it is not a probability of repayment.
- An officer signs off on every final decision. Applications in the low-probability group require human review.
- The learner guide calls the low-probability band “auto-reject,” while the main assignment explicitly requires human review. This project follows the main assignment.
- The dataset is small and contains past decisions rather than repayment outcomes. Test results cannot establish real-world credit risk or legal compliance. Live deployment and a fuller fairness review are outside scope.
