# alignment_statement.md

## 1) Which dataset did you choose for your classification problem?

I use the **CDC Diabetes Health Indicators** dataset hosted on the **UCI Machine Learning Repository** (dataset id: **891**). It is derived from U.S. public health survey data (BRFSS-style indicators) and includes health behaviors, comorbidities, access-to-care indicators, and demographics.

In my project, the working label column is **`Diabetes_binary`**, and the feature matrix includes:

`HighBP, HighChol, CholCheck, BMI, Smoker, Stroke, HeartDiseaseorAttack, PhysActivity, Fruits, Veggies, HvyAlcoholConsump, AnyHealthcare, NoDocbcCost, GenHlth, MentHlth, PhysHlth, DiffWalk, Sex, Age, Education, Income`.

## 2) What classes does it cover? Are they aligned with your prediction task?

The dataset covers **two classes** via `Diabetes_binary`:

- **0 = Non-diabetes**
- **1 = Diabetes**

These classes are **directly aligned** with my prediction task: **binary classification to predict diabetes status** from available health indicators and demographic/access-related features.

## 3) Why is this dataset ethically and technically fit for your project?

### Technical fit
- **Appropriate supervision signal:** The target (`Diabetes_binary`) matches the intended classification objective.
- **Predictive feature set:** Features include well-established correlates of diabetes risk (e.g., BMI, hypertension, cholesterol, physical activity) plus broader health-status variables, making the task feasible for standard supervised learning.
- **Structured tabular format:** The data is immediately usable for classical ML baselines (logistic regression, tree ensembles) and supports interpretability techniques (feature importance, SHAP), which is important for a high-stakes health domain.
- **Scale:** Dataset size is sufficient for robust train/validation/test splits and error analysis.

### Ethical fit (with constraints)
- **Publicly available, documented source:** The dataset is distributed via UCI with documentation and provenance, supporting transparency and reproducibility.
- **Non-diagnostic framing:** The project treats the model as **decision support** (e.g., screening prioritisation), not as an automated diagnosis. Predictions should only guide *who to screen next*, with clinical confirmation required.
- **Explicit fairness review enabled:** The dataset includes variables such as `Sex`, `Age`, `Education`, `Income`, and access-to-care fields (`AnyHealthcare`, `NoDocbcCost`) that allow me to *measure* and manage potential disparate performance—rather than ignoring these issues.

## 4) What gaps remain, and how do you plan to address them?

### Remaining gaps / risks
1. **Label bias / diagnosis visibility bias**
   - Diabetes status may reflect access to healthcare (who gets tested/diagnosed), not only physiological risk. Features like `AnyHealthcare`, `CholCheck`, and `NoDocbcCost` may partially encode this mechanism.

2. **Sensitive attributes and proxies**
   - `Income`, `Education`, and access variables are strong proxies for socioeconomic status. Using them can improve accuracy but may worsen inequity if the model disproportionately misclassifies disadvantaged groups (especially false negatives that delay screening).

3. **Self-report measurement noise**
   - Many indicators are survey-based and may be affected by recall bias and social desirability bias (e.g., diet, alcohol, smoking, mental health days).

4. **Representativeness and generalisability**
   - The dataset reflects a particular population and context (U.S. survey-based). A model trained here may not generalise to other countries, clinical settings, or time periods.

5. **Class imbalance and thresholding harms**
   - If the diabetes-positive class is smaller, accuracy can be misleading; threshold choices can concentrate harm (missed positives vs. unnecessary follow-ups).

### Mitigation plan
- **Fairness and subgroup evaluation:** Report performance not only overall but by key groups (e.g., `Sex`, `Age` bins, and SES proxies like `Income`/`Education`). Track disparities in **recall / false negative rate**, since missed diabetes cases are high-cost.
- **Sensitivity analysis for access/SES variables:** Train and compare models:
  - (A) full feature set,
  - (B) excluding access-to-care variables (`AnyHealthcare`, `NoDocbcCost`, `CholCheck`),
  - (C) excluding SES proxies (`Income`, `Education`),
  to see how accuracy and fairness trade off.
- **Robust metrics:** Use **balanced accuracy, F1, PR-AUC**, and calibration checks; select thresholds based on the screening-support objective (favoring recall if appropriate), while quantifying the cost of false positives.
- **Interpretability and documentation:** Use model interpretability (e.g., permutation importance / SHAP) to identify whether predictions rely heavily on access/SES proxies and document these findings.
- **Clear scope statement:** Explicitly constrain the use case to screening prioritisation and include a limitation note that the model is not a diagnostic tool and may not transfer across populations without revalidation.
- **Data quality checks:** Assess missingness, outliers, and potential leakage; apply appropriate preprocessing and stratified splitting.

This alignment approach ensures the dataset and label match the classification task while making ethical and validity risks explicit and testable within the project deliverable.
