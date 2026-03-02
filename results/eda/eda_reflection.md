# eda_reflection.md

## Which EDA visualization surfaced the most surprising or unexpected pattern?
The most unexpected pattern came from the **target-class balance plot** for `Diabetes_binary` and the follow-up **feature-by-target comparisons** (e.g., bar plots of `HighBP/HighChol` by class and the BMI distribution split by class). The class distribution showed that the dataset is not perfectly balanced, and the comparative plots suggested that several “access-to-care” variables (especially `CholCheck`, `AnyHealthcare`, and `NoDocbcCost`) separate the classes in a way that may reflect **diagnosis visibility** as much as physiological risk. In other words, the model could partially learn “who gets screened/diagnosed” rather than purely “who is biologically at risk.”

## How will that insight influence your approach to feature engineering?
This insight changes my approach in two concrete ways:
1. **Sensitivity analysis on proxy features:** I will train and evaluate models with multiple feature sets—(A) all features, (B) excluding access-to-care variables (`CholCheck`, `AnyHealthcare`, `NoDocbcCost`), and (C) optionally excluding socioeconomic proxies (`Income`, `Education`)—to quantify how much performance depends on variables that could embed structural inequities.
2. **Modeling choices + evaluation focus:** I will prioritize interpretable baselines (logistic regression, tree models) and add **group-wise error analysis** (e.g., recall / false negative rates by `Sex`, `Age`, and SES proxies) to ensure that improved headline metrics do not hide harmful disparities. If I use scaling-sensitive models, I’ll keep preprocessing in a pipeline to avoid leakage and to make feature transformations reproducible.

## Which external resource (article, video, or tool) supported your learning the most and why?
The most helpful resource was the **UCI Machine Learning Repository dataset documentation** for the CDC Diabetes Health Indicators dataset, because it clarified the feature meanings/codings and highlighted that many variables are survey-derived and can act as proxies for healthcare access and socioeconomic status. That context directly informed what I looked for in EDA (class imbalance, skew, potential proxies) and what I plan to validate later (fairness checks and sensitivity analyses).
