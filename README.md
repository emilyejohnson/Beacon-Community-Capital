# Beacon Community Capital – First-Pass Loan Sorter

## Description

This course project explores Beacon Community Capital’s historical loan applications and builds a logistic regression model to support first-pass application sorting. The model estimates the probability of a historical approval decision using information from each application. It predicts **past loan-officer decisions**, rather than repayment, default, or creditworthiness. The notebook demonstrates three probability groups, while the proposed workflow keeps final lending decisions with a loan officer. Live deployment is outside the project’s scope.

| Predicted approval probability | Sorting group | Proposed handling |
| --- | --- | --- |
| > 0.80 | Likely approval | Expedited review, with final loan-officer sign-off |
| 0.20 to 0.80, inclusive | Borderline | Loan-officer review |
| < 0.20 | Likely rejection | Loan-officer review before any final decision |

## Data

- **Source file:** `loan_applications.csv`, located in the repository root. The dataset is supplied for the assignment; the notebook does not download external data.
- **Raw dataset:** 232 rows and 9 columns. Each row represents a historical loan application.
- **Cleaned dataset:** 212 applications with no missing values.
- **Columns:** `applicant_id`, `fico_score`, `annual_income`, `loan_amount`, `loan_term_months`, `employment_status`, `years_employed`, `savings_balance`, and `approved`.
- **Target:** `approved`, encoded as `approved_flag`, where Approved = 1 and Rejected = 0.
- **Model inputs:** FICO score, annual income, loan amount, loan term, years employed, savings balance, and an indicator for self-employment.
- **Excluded inputs:** `applicant_id`, `approved`, and `approved_flag`.

The notebook fixes four data problems in the required order:

1. Convert annual income, loan amount, and savings balance from money-formatted text to numbers.
2. Remove 12 exact duplicate rows, reducing the dataset from 232 to 220 applications.
3. Remove 8 rows containing specified impossible values, leaving 212 applications.
4. Fill 10 missing FICO scores with the median FICO score from the remaining applications.

The impossible-value checks remove rows with FICO scores above 850, negative annual income, loan amounts of zero or less, loan terms above 84 months, more than 60 years of employment, or negative savings balances.

## Project Structure

| File or deliverable | Purpose |
| --- | --- |
| `loan_applications.csv` | Supplied historical loan applications |
| `Learner_Guide_Notebook.ipynb` | Data cleaning, exploratory analysis, model training, evaluation, charts, and written reflections |
| `README.md` | Project overview, setup instructions, results, and limitations |
| Executed notebook HTML export | Submission deliverable containing code, explanations, and executed outputs |
| Codex prompt trail | Submission deliverable documenting prompts and key development decisions, including Stage 0 |

## How to Run

1. Open the `Beacon-Community-Capital` repository folder in VS Code.
2. Select a Python 3.13 environment with the required libraries. The course environment uses Python 3.13.15.
3. If you need a new environment, run these commands in the project’s terminal:

   ```bash
   python3.13 -m venv .venv
   source .venv/bin/activate
   python -m pip install pandas numpy matplotlib seaborn scikit-learn ipykernel nbconvert
   ```

4. Open `Learner_Guide_Notebook.ipynb` and select the environment as the notebook’s Python kernel.
5. Keep `loan_applications.csv` in the repository root. Run all notebook cells from top to bottom, beginning with the imports and data-loading cells.
6. Review the cleaning counts, charts, model results, and written explanations.
7. Save the executed notebook. To create the HTML submission copy, run:

   ```bash
   python -m jupyter nbconvert --to html Learner_Guide_Notebook.ipynb
   ```

   This creates `Learner_Guide_Notebook.html` in the project folder.

## Stages

1. **Explore and clean:** Inspect the raw data, fix the four identified problems in order, and verify the final dataset.
2. **Business insights:** Explore approval patterns using charts and discuss what the results suggest, including fairness concerns.
3. **Model training:** Encode the target and employment status, create a stratified 80/20 train/test split using `random_state=42`, train logistic regression, and examine raw and standardized coefficients.
4. **Evaluation:** Compare test accuracy with a FICO cutoff rule and an always-approve baseline, then sort test applications into probability groups.

## Output and Findings

### Data and exploratory analysis

The cleaned dataset contains **212 applications**: 127 historically approved and 85 rejected, for an overall approval rate of approximately **59.9%**.

The notebook produces:

- Approval and rejection counts.
- A FICO-score histogram with the 660 cutoff.
- Approval rates by employment type.
- A FICO-score boxplot grouped by historical decision.
- A numerical correlation heatmap.
- Raw and standardized logistic regression coefficient charts.
- An accuracy comparison chart.
- A chart showing the three probability groups.

Historically approved and rejected applications have substantial overlap in their FICO scores. This suggests that a single credit-score cutoff cannot fully capture the officers’ decision patterns.

### Test results

The stratified split uses **169 applications for training** and **43 for testing**.

| Approach | Test accuracy | Correct matches to historical decisions |
| --- | ---: | ---: |
| Logistic regression | 86.0% | 37 of 43 |
| Approve when FICO ≥ 660 | 65.1% | 28 of 43 |
| Always approve | 60.5% | 26 of 43 |

The logistic regression model matched nine more historical decisions than the FICO rule on the test set. It considers several application features, giving it more information than a single cutoff.

A separate model trained on standardized training features is used to compare coefficient magnitudes. The reported test accuracy and probability groups come from the original model trained on unscaled features.

### Probability groups

| Group | Probability threshold | Test applications |
| --- | --- | ---: |
| Likely approval | > 0.80 | 21 |
| Borderline | 0.20 to 0.80, inclusive | 14 |
| Likely rejection | < 0.20 | 8 |
| **Total** | | **43** |

The learner guide labels the outer groups “auto-approve” and “auto-reject.” These counts describe the notebook’s sorting demonstration. Under the proposed workflow, a loan officer remains responsible for every final decision, including applications in the low-probability group.

All charts and tables appear inline in the notebook and are included in the HTML export when the executed notebook is exported.

## Additional Project Notes

- **Historical decisions are the learning target.** Higher test accuracy means closer agreement with past officers’ decisions. It does not establish better lending outcomes.
- **Probabilities have a limited meaning.** The model’s approval probability is an estimate of the historical approval label, not the probability of repayment. Its calibration has not been established.
- **Fairness requires further review.** Historical decisions may contain bias or inconsistency. Income, savings, and employment information may also reflect unequal opportunities or serve as proxies for protected characteristics. Excluding explicit protected traits does not establish fairness.
- **Human oversight remains part of the proposed workflow.** Low-probability applications require review before rejection, and high-probability applications require final officer sign-off.
- **The evaluation is small.** Results come from one test set of 43 applications and may change with a different split or new data.
- **Cleaning follows the assignment’s required order.** FICO median imputation occurs before the train/test split, so it uses information from the full cleaned dataset. A production evaluation should fit imputation and other learned preprocessing steps on training data only.
- **Live lending use is outside scope.** Further work would require repayment-outcome data, broader validation, probability calibration, fairness assessment, and an appropriate policy and legal review.