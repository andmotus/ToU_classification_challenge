## Feature selection for modeling (based on EDA findings)

### Keep (core signal features)
EDA showed clear separation in diabetes prevalence for several *clinical / risk indicator* variables, so these should be retained as the backbone of the model:

- **HighBP**
- **HighChol**
- **HeartDiseaseorAttack**
- **Stroke** *(rare=1, but strong risk signal)*
- **DiffWalk**
- **GenHlth** *(worse categories → higher diabetes prevalence)*

### Keep (continuous-ish features with visible class shift)
Boxplots indicated meaningful shifts between classes and/or different spreads:

- **BMI** *(higher for diabetes cases)*
- **Age** *(higher for diabetes cases)*
- **PhysHlth**, **MentHlth** *(zero-inflated with long right tails; still informative “health burden” proxies)*

> Note: For linear models, scale these numeric/ordinal features; for tree models scaling is optional.

### Keep (context / demographic / socioeconomic)
These can capture confounding and improve calibration/generalization:

- **Sex**
- **Education**
- **Income**

### Keep for now, but mark as “candidates to drop” (potentially weaker / confounded)
EDA suggested some lifestyle/access proxies may show weaker or more confounded patterns, so we should include them initially, then let model-based selection decide:

- **Smoker**
- **PhysActivity**
- **Fruits**, **Veggies**
- **HvyAlcoholConsump**
- **CholCheck**
- **AnyHealthcare**
- **NoDocbcCost**

### Correlation / redundancy note
Correlations were mostly modest but there are small clusters among health-status variables (e.g., **GenHlth / PhysHlth / MentHlth / DiffWalk**).  
Rather than dropping them upfront, keep them and rely on:
- **regularization** (LogReg with `class_weight="balanced"` + L1/L2), and/or
- **post-hoc feature selection** (permutation importance / SHAP / drop-column tests)
to prune if needed.

**Proposed starting feature set for Notebook 03:**  
Use **all 21 predictors** initially, with a clear “core vs candidate” grouping as above, then validate removals by PR-AUC/F1 (stratified CV).