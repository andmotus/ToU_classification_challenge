# Model Selection Context (M3L3 → M3L4)

This document summarizes the **feature engineering + EDA takeaways** that should guide algorithm choice and evaluation for the CDC Diabetes Health Indicators classification task (`Diabetes_binary`, 0 = no diabetes, 1 = diabetes).

## Top 5 most important features (based on EDA)

From bivariate prevalence plots (P(diabetes=1) by category) and class-separated boxplots, the strongest and most consistent signals were:

1. **HighBP** — diabetes prevalence increases clearly when hypertension is present.
2. **HighChol** — higher prevalence when high cholesterol is present.
3. **BMI** — diabetes cases show higher BMI distribution vs non-diabetes.
4. **Age** — diabetes cases skew older; separation visible across age bins.
5. **GenHlth** — worse self-reported general health corresponds to higher diabetes prevalence.

**Also strong / high-signal (honorable mentions):** `DiffWalk`, `HeartDiseaseorAttack`, `Stroke`, plus health-burden proxies `PhysHlth` and `MentHlth`.

## Class imbalance (special handling?)

Yes. EDA shows a **strong imbalance**: about **86% class 0** vs **14% class 1**.

**Implications**
- Don’t optimize for accuracy alone.
- Use **stratified splits**.
- Prefer imbalance-aware metrics: **PR-AUC**, **recall/precision**, **F1** (ROC-AUC as secondary).
- Consider imbalance handling in modeling:
  - `class_weight="balanced"` (e.g., Logistic Regression, tree models that support it)
  - threshold tuning (optimize for recall/precision trade-off)
  - (optional) resampling methods if needed (SMOTE/undersampling), but start with class weights + proper metrics first.

## Feature set size (affects algorithm choice)

- **Original dataset:** ~35 features (CDC Diabetes Health Indicators).
- **EDA-driven selected feature set used downstream:** **21 features**
  - Core (13): `HighBP, HighChol, HeartDiseaseorAttack, Stroke, DiffWalk, GenHlth, BMI, Age, PhysHlth, MentHlth, Sex, Education, Income`
  - Candidate (8): `Smoker, PhysActivity, Fruits, Veggies, HvyAlcoholConsump, CholCheck, AnyHealthcare, NoDocbcCost`

**Implications**
- Feature space is **small-to-moderate**, so:
  - Linear baselines (Logistic Regression) are feasible and interpretable.
  - Tree-based ensembles (Random Forest, Gradient Boosting / XGBoost / LightGBM / HistGB) are also feasible and may capture non-linearities/interactions.

## Interpretability or prediction speed constraints?

**Interpretability:** *Moderately important.*  
This is a health-risk screening context, so being able to explain drivers (e.g., BP, BMI, age) is valuable for stakeholder trust and clinical/public-health usability.

- Favor at least one interpretable baseline:
  - **Regularized Logistic Regression** (with standardized continuous-ish features)
  - Optionally complement with model-agnostic explanations (permutation importance / SHAP) for non-linear models.

**Prediction speed:** *Not a hard constraint.*  
With ~21 tabular features, **all common sklearn models** will predict very fast; even boosted trees are typically fine for batch or near-real-time scoring.

## What this means for algorithm choices

Recommended comparison set (ordered from simplest → more flexible):

1. **Logistic Regression (regularized, class_weight balanced)**  
   - Strong baseline, interpretable, fast.
2. **Tree-based non-linear model** (e.g., Random Forest or Gradient Boosting / HistGradientBoosting)  
   - Captures non-linearities and feature interactions suggested by EDA.
3. **Threshold tuning + PR-focused selection**  
   - Choose decision thresholds that match the goal (e.g., higher recall to catch more diabetes cases).

Evaluation should emphasize **PR-AUC and recall/precision trade-offs**, not raw accuracy.
