# Protein Shake Category Appraisal — Statistical Methods Reference

## Purpose

This is the reviewed statistical-method library for the Protein Shake Category Appraisal workshop, covering how each research question should be analyzed, what robustness checks apply, and which methods are deliberately **not** used on this dataset (Appendix A).

Use this reference together with the companion *Data Reference Guide*, which defines the dataset's variables, scales, and capability boundary.

> **An SOP describes an approved method. It does not prove that the current dataset contains the inputs required to use that method.**

---

# Global Statistical Rules

## G1. Repeated Measures Are Fundamental

All 50 panelists evaluated all 8 products.

For row-level consumer outcomes, repeated observations within `panelist_id` must be respected.

Do not treat 400 evaluation rows as 400 independent consumers.

---

## G2. Default Alpha

Unless an approved SOP says otherwise:

```text
alpha = 0.05
```

---

## G3. Multiple Comparisons

For families of product pairwise comparisons, use:

```text
Holm adjustment
```

unless the SOP explicitly specifies another reviewed procedure.

---

## G4. Uncertainty

Use **95% confidence intervals** by default.

Prefer interval estimates and effect magnitude alongside p-values.

---

## G5. Analysis Classification

Every substantive analysis must be classifiable as one of:

- **Confirmatory / hypothesis-oriented**
- **Diagnostic**
- **Exploratory**

Do not present exploratory structure as confirmatory evidence.

---

## G6. Reproducibility

For stochastic methods:

```python
RANDOM_STATE = 42
```

Document transformations, aggregation, scaling, missing-data handling, and model selection.

---

## G7. Source-of-Truth Requirement

Use:

- variable names, meanings, scales, and data capability: the companion *Data Reference Guide* + active dataset;
- prohibited methods: **Appendix A: Methods Not Used, and Why**;
- approved methods and diagnostics: this file;
- output language/reporting: the reporting conventions below;
- governing behavior: the project's operating principles.

Do not infer a variable's meaning from its name when the schema says otherwise or does not define it.

---

# G7-A. Dataset Capability Gate

The Dataset Capability Gate must be applied **before selecting or running an SOP** and again **before recommending any follow-up SOP**.

## Step 1 — Identify required inputs

For the proposed analysis, identify every required input, including:

- outcome variable;
- predictor(s);
- moderator(s);
- grouping/repeated-measures identifier;
- product identifier;
- required JAR item;
- required descriptive attribute;
- comment field;
- any design feature necessary for the inference.

## Step 2 — Verify availability

Verify each required input is:

1. present in the active dataset;
2. defined in the companion *Data Reference Guide* or an explicitly approved schema supplement;
3. measured at the unit/scale required by the SOP.

## Step 3 — Classify capability

### SUPPORTED

All required inputs are verified present.

The SOP may proceed to its design/diagnostic eligibility checks.

### PARTIALLY SUPPORTED

A separable sub-question can be answered, but another requested component lacks a required input.

Run only the supported component and name the missing input.

### UNSUPPORTED

A required substantive input is absent.

The SOP is **ineligible for the current dataset/question**.

Do not run it and do not recommend it as a current-data next step.

## No automatic proxies

Do not substitute a related construct unless an approved SOP/schema explicitly authorizes the mapping.

Examples:

- `overall_liking` ≠ purchase intent;
- `overall_liking` ≠ repurchase intent;
- `desc_berry_note` ≠ berry JAR;
- protein/off-note comments ≠ quantitative protein intensity;
- `flavor_impact` ≠ quality.

## SOP examples are conditional

Question examples listed under “Use When” mean:

> use this SOP **if the construct and required variables exist**.

They are not evidence that those variables exist in every dataset.

## Recommendation gate

If a follow-up analysis fails this gate:

- omit it from Current-Data Next Steps; or
- place it explicitly under **Future Research / Future Data Collection** and identify what would need to be collected.

---

# G8. Binary Stop-Rule Convention

Where an SOP defines a hard stop, the result from that method must not be substantively interpreted.

## Pre-fit hard stop

If a required condition can be checked before model fitting and fails:

- do not fit the substantive model;
- report the failed criterion;
- route to an approved alternative where available.

A missing required variable under G7-A is a **capability stop before analysis**, not a model diagnostic failure.

## Post-fit hard stop

Some methods require candidate models to evaluate whether a valid solution exists.

Candidate models may then be fit only for validation.

If the validation stop rule fails:

- do not retain a final substantive solution;
- do not name/interpret failed clusters, topics, components, or drivers;
- do not generate the substantive result visualization;
- report the diagnostics that caused the halt.

## Downgrade-and-proceed

Allowed only when an SOP explicitly uses those words and defines:

- the condition;
- the downgraded interpretation;
- required warning language.

If an SOP does not explicitly authorize downgrade-and-proceed, assume **hard stop**.

---

# G9. Numeric Diagnostic Thresholds and Mandatory Disclosure

## Purpose

Qualitative instructions such as “check convergence” are not sufficient for this project.

Analyses with diagnostics must use explicit operational thresholds.

These are **project governance rules for this workshop dataset**, not universal statistical laws.

## Status levels

### PASS

Within approved operating range.

### WARNING / SENSITIVITY REQUIRED

Issue must be named, quantified, disclosed, and followed by the required sensitivity procedure when specified.

### HARD STOP

The substantive result from that method must not be interpreted.

## Mandatory diagnostics block

Every inferential, clustering, nonlinear, PLS, penalty-bootstrap, or unsupervised-topic analysis must include:

| Diagnostic | Observed Value | Threshold | Status | Required Action |
|---|---:|---:|---|---|

Name every failed/warning model individually.

Do not hide failures inside ranges.

---

# G9-A. Mixed-Effects Model Thresholds

Applies to SOP-02, SOP-03, SOP-04, SOP-10, and SOP-11.

## Approved optimizer sequence

Use the statsmodels MixedLM default sequence unless reviewed otherwise:

```text
BFGS → L-BFGS → Conjugate Gradient (CG)
```

Do not keep trying arbitrary optimizers until one produces a favorable result.

## Downgrade-and-proceed rule

Downgrade-and-proceed is authorized only for A3–A7 warning/sensitivity states.

Proceed only when:

1. the primary mixed model converged;
2. no A2 hard-stop condition occurred;
3. the required repeated-measures sensitivity analysis was completed; and
4. the substantive conclusion was directionally consistent.

If sensitivity materially changes the direction, planned contrast support, or grouping used for interpretation:

- label result **model-sensitive**;
- do not use compact significance letters;
- do not present one definitive inferential conclusion.

A1 and A2 are always hard stops.

## A1. Optimizer Convergence

Required:

```text
result.converged == True
```

**HARD STOP** if false, optimization fails after approved sequence, or final convergence warning indicates optimum not reached.

## A2. Singular Random-Effects Covariance / Invalid Hessian

**HARD STOP** if any occur:

- `Random effects covariance is singular`;
- pseudo-inverse/singular handling is required for the fitted covariance;
- final Hessian is not positive definite;
- covariance inversion needed for standard errors fails.

## A3. Boundary Random-Effect Variance

Warning threshold:

```text
estimated random-intercept variance < 0.01
```

Required:

- disclose;
- calculate ICC;
- perform approved repeated-measures sensitivity analysis.

## A4. Near-Zero ICC

If:

```text
ICC < 0.01
```

→ WARNING / SENSITIVITY REQUIRED.

Use an approved repeated-measures sensitivity approach such as a panelist fixed-effect model when compatible with the question.

If the substantive conclusion changes materially, label it model-sensitive and do not use compact significance letters.

## A5. Residual Distribution

Use residual diagnostics rather than requiring perfect normality.

Warning:

```text
formal normality test p < 0.01
```

Sensitivity required if either:

```text
|residual skewness| > 1.0
```

or

```text
Pearson residual kurtosis > 5.0
```

A formal normality p-value alone is not a hard stop.

Name every outcome model crossing thresholds.

## A6. Residual Heteroscedasticity

Calculate residual variance by product:

```text
variance_ratio = max(product residual variance) / min(product residual variance)
```

Also run median-centered Levene/Brown-Forsythe.

### Warning

Either:

```text
p < 0.01
```

or

```text
variance_ratio > 2.0
```

### Sensitivity required

Either:

```text
p < 0.01 AND variance_ratio > 2.0
```

or

```text
variance_ratio > 4.0
```

## A7. Extreme Residuals

- `|residual| > 3.0` → flag observation
- more than 1% of analyzed rows with `|residual| > 3.0` → SENSITIVITY REQUIRED
- any `|residual| > 4.0` → SENSITIVITY REQUIRED

Never delete observations automatically.

---

# G9-B. JAR Penalty Thresholds

Applies to SOP-08.

The required JAR attribute itself must first pass G7-A. A descriptive attribute cannot replace a missing JAR variable.

Let:

```text
N_valid = number of valid product × attribute responses
minimum_group_n = max(10, ceiling(0.20 × N_valid))
```

For the current 50-consumer complete design:

```text
minimum_group_n = 10
```

## Eligibility

For Too Little or Too Much penalty:

```text
N_JAR >= minimum_group_n
AND
N_deviation >= minimum_group_n
```

**HARD STOP for that penalty cell** if either fails.

Report N_JAR, N_deviation, prevalence, and which group failed.

## Bootstrap

Use:

```text
9,999 bootstrap resamples
95% CI
RANDOM_STATE = 42
BCa preferred
```

Hard stop on bootstrap-supported interpretation if:

- interval contains NaN;
- degenerate-data warning occurs;
- bootstrap SE is non-finite.

Raw descriptive group means may still be reported if useful, but not as robust bootstrap-supported penalty results.

## Workshop optimization-priority threshold

Flag as a workshop optimization priority only when all are true:

```text
deviation prevalence >= 20%
penalty magnitude >= 0.50 hedonic points
95% bootstrap CI lower bound > 0
```

These are project decision thresholds, not universal industry standards.

---

# G9-C. PCA Thresholds

Applies to SOP-05 and preference-map PCA where relevant.

## C1. Zero variance

A predictor with:

```text
SD == 0
```

cannot contribute to PCA.

Hard stop the PCA workflow until it is removed under the SOP and the removal is disclosed.

Do not silently drop it.

## C2. Two-dimensional map adequacy

Calculate:

```text
PC1 variance explained + PC2 variance explained
```

Interpretation thresholds:

- `>= 60%` → strong 2D workshop summary
- `50% to <60%` → usable with caution
- `40% to <50%` → exploratory / limited 2D representation
- `<40%` → do not summarize the category primarily from PC1/PC2

PCA itself is not hard-stopped below 40%; **strong 2D interpretation is**.

---

# G9-D. Nonlinear / Quadratic Relationship Thresholds

Applies to SOP-10.

The predictor must first exist under G7-A.

## Eligibility

Within modeled scope:

```text
at least 5 distinct observed predictor values
N >= 30 total observations
```

Otherwise **HARD STOP**.

## Claiming curvature

Require both:

```text
quadratic-term p < 0.05
AND
ΔAIC (linear model - quadratic model) >= 2
```

Models must be fitted comparably.

## Claiming an optimum

Turning point must:

1. fall within observed predictor range;
2. fall between 10th and 90th percentiles of observed predictor values;
3. have at least 10 observations below;
4. have at least 10 observations above.

If any fail, do not claim an optimum.

---

# G9-E. Product × Consumer Interaction Thresholds

Applies to SOP-11.

The moderator must be directly measured and defined under G7-A.

Subgroup counts are at the **unique-panelist level**.

- `N >= 10` → eligible for standard interaction interpretation
- `N = 5–9` → **DOWNGRADE-AND-PROCEED: exploratory only**
- `N < 5` → HARD STOP for inference involving that level

If any categorical moderator level has N < 5, do not fit the full interaction as coded unless an explicitly approved recoding rule exists.

Do not silently merge categories.

---

# G9-F. Consumer Segmentation Thresholds

Applies to SOP-12.

Evaluate:

```text
k = 2, 3, 4
```

and `k=5` only when no smaller solution is adequate and the user specifically needs additional resolution.

Use:

```text
n_init >= 50
random_state = 42
```

A candidate is retainable only if ALL pass:

### Silhouette

```text
mean silhouette >= 0.25
```

### Minimum cluster size

```text
each cluster N >= 5 consumers
```

### Stability

```text
median Adjusted Rand Index >= 0.60
AND
10th-percentile Adjusted Rand Index >= 0.40
```

computed on consumers common to compared resampling fits.

If no candidate passes:

> **The data do not support a stable discrete consumer segmentation.**

Hard stop substantive segmentation and route to SOP-13 if preference heterogeneity is still of interest.

---

# G9-G. Optional Product-Level PLS Thresholds

Applies to optional PLS layer in SOP-09.

Required measured sensory predictors must pass G7-A.

Because there are only 8 distinct products:

```text
maximum PLS components = 2
```

unless a future dataset has more products and the SOP is re-reviewed.

Use leave-one-product-out validation.

If:

```text
Q² <= 0
```

or performance is no better than the mean-only reference:

→ **HARD STOP on interpreting a driver hierarchy.**

Refit across leave-one-product-out folds.

If a coefficient changes sign in:

```text
> 25% of folds
```

that attribute cannot be ranked as a stable positive/negative driver.

---

# G9-H. Exploratory Correlation Thresholds

Applies to SOP-19.

Both variables must be directly measured/approved derived quantities under G7-A.

For within-product Spearman associations with N ≈ 50:

- `|rho| < 0.20` → negligible / very weak
- `0.20 <= |rho| < 0.30` → weak exploratory association
- `0.30 <= |rho| < 0.50` → moderate exploratory association
- `|rho| >= 0.50` → strong exploratory association

These are project interpretation conventions.

A pooled correlation must not be called a sensory driver if:

```text
|pooled rho - median within-product rho| > 0.20
```

or pooled direction reverses sign in:

```text
2 or more products
```

---

# G9-I. NMF Topic-Model Eligibility and Stability Thresholds

Applies to SOP-16.

## Current Workshop Dataset Status

**SOP-16 is not to be used for the current Protein Shake workshop dataset.**

For this dataset, open-ended comment theme analysis must route to **SOP-15 Structured Open-Ended Comment Coding** rather than unsupervised TF-IDF + NMF topic extraction.

The thresholds below are retained as the reviewed eligibility/stability rules for a future dataset where SOP-16 is intentionally reactivated.

SOP-16 is exploratory, but the stop rule is binary.

## Pre-fit eligibility — ALL must pass

After removing missing comments and documented preprocessing:

### Corpus size

```text
at least 100 valid comments
```

### Usable vocabulary

```text
at least 50 retained TF-IDF terms
```

### Document information

```text
at least 70% of valid comments contain >= 3 retained tokens
```

### Repetition

No single normalized exact comment may represent:

```text
> 40% of valid corpus
```

### TF-IDF filtering

Default:

```text
min_df = 2
max_df = 0.95
```

unless explicitly justified.

If any pre-fit condition fails:

**HARD STOP before NMF.** Route to SOP-15.

## Candidate topic counts

Evaluate:

```text
2 to 5 topics
```

## Post-fit stability

Fit each candidate across:

```text
20 random seeds
```

Match topics across runs by cosine similarity of topic-term weight vectors.

Pass only if:

```text
mean matched-topic cosine similarity >= 0.80
AND
minimum matched-topic cosine similarity >= 0.65
```

Each retained topic must represent at least:

```text
5% of valid comments
```

as dominant topic.

If no candidate passes:

**HARD POST-FIT STOP.** Do not name or visualize topics.

---

# G9-J. Diagnostic Disclosure Requirement

For any G9-governed analysis, disclose:

1. every hard-stop check;
2. every warning check;
3. every sensitivity analysis triggered;
4. the observed value causing it;
5. whether the substantive conclusion changed after sensitivity analysis.

Do not report only aggregate ranges when individual models fail.

---

# SOP Routing Index

The routing index is conditional on G7-A. It does **not** assert that every listed variable exists in every dataset.

| SOP | Research Question | Approved Method |
|---|---|---|
| SOP-01 | What does the sample/data look like? | Basic descriptive statistics + QC |
| SOP-02 | Which products differ in liking? | Mixed-effects product model |
| SOP-03 | Which products differ in flavor impact? | Mixed-effects product model |
| SOP-04 | Which measured descriptive attributes differentiate products? | Attribute-wise mixed-effects models |
| SOP-05 | How is the measured sensory category structured? | Standardized product-mean PCA |
| SOP-06 | Which products are sensorially similar? | Hierarchical clustering |
| SOP-07 | Where are products Too Little / JAR / Too Much on measured JAR items? | JAR distribution analysis |
| SOP-08 | Which measured JAR deviations are associated with liking loss? | JAR penalty + bootstrap |
| SOP-09 | What measured sensory characteristics appear to explain consumer preference? | IPM → EPM; PLS optional |
| SOP-10 | Is a measured sensory attribute nonlinearly related to liking? | Mixed regression with quadratic term |
| SOP-11 | Do measured consumer characteristics alter product response? | Mixed-model interaction |
| SOP-12 | Are there preference-based consumer segments? | Validated KMeans |
| SOP-13 | How heterogeneous are individual preferences? | Internal preference mapping |
| SOP-14 | How do measured sensory characteristics relate to preference? | External preference mapping |
| SOP-15 | What themes occur in open-ended comments? | Structured qualitative coding |
| SOP-16 | Can exploratory text themes be discovered automatically? | TF-IDF + NMF — reviewed method, **not used for this workshop dataset**; route current-data theme work to SOP-15 |
| SOP-17 | How strongly do products differ in prompted like/dislike language? | Product-relative VADER profile with consumer-level bootstrap |
| SOP-18 | Are there unusual/potentially problematic respondents? | Respondent QC |
| SOP-19 | Are measured variables associated exploratorily? | Correlation analysis |
| SOP-20 | How large are important product differences? | Effect-size / contrast estimation |

---

# SOP-01 — Basic Descriptive Statistics and Data Quality

## Use When

Questions include:

- “Summarize the dataset.”
- “What does the category look like?”
- “Which products score highest?”
- “What are the basic product profiles?”
- “Are there missing values?”
- “How many consumers are in the study?”

## Analysis Type

**Descriptive / diagnostic**

No inferential conclusion should be made from SOP-01 alone.

## Required Variables

Core:

- `panelist_id`
- `product_code`

Depending on question, only variables that pass G7-A may be summarized.

Examples:

- `overall_liking`
- `flavor_impact`
- measured JAR variables
- measured `desc_*` variables
- demographics
- `likes`
- `dislikes`

## Procedure

1. Verify row/column count, consumers, products.
2. Verify evaluations per panelist.
3. Check duplicate `panelist_id × product_code`.
4. Check missingness.
5. Check scale-valid ranges.
6. Summarize demographics using one row per panelist.
7. Summarize product outcomes using evaluation rows.
8. Report mean, SD, median, N, and CI where useful.
9. For liking, optionally report %7–9 and %1–3.
10. For descriptive variables, report product means/SDs.

## Guardrails

- Do not count demographics across 400 rows.
- Do not infer significance from rank order.
- Do not call highest mean “statistically best” without SOP-02.
- Do not infer JAR performance from JAR means alone.
- Do not treat missing open-ended responses as “none.”
- Do not summarize a construct that is absent merely because it would normally appear in a category appraisal.

## Approved Visuals

- mean liking plot;
- flavor-impact plot;
- product × descriptive heatmap;
- demographic bar charts;
- response-distribution charts.

---

# SOP-02 — Product Differences in Overall Liking

## Use When

- “Which products are most liked?”
- “Are liking scores significantly different?”
- “Is Product G preferred over Product B?”
- “Which products form statistically different liking groups?”

## Required Inputs

- `overall_liking`
- `product_code`
- `panelist_id`

All must pass G7-A.

## Analysis Type

**Confirmatory / hypothesis-oriented**

## Model

```text
overall_liking ~ product_code + (1 | panelist_id)
```

## Procedure

1. Confirm repeated product evaluations.
2. Fit mixed-effects model.
3. Apply G9-A diagnostics.
4. Test overall product effect.
5. Estimate product means/contrasts.
6. Perform requested or complete pairwise comparisons when justified.
7. Apply Holm correction.
8. Report difference, 95% CI, adjusted p-value, practical magnitude.
9. If letters requested, derive from adjusted pairwise results.

## Significance-Letter Convention

When compact letters are requested and diagnostics permit:

- order products from highest estimated mean to lowest;
- assign the highest estimated mean group the letter **`a`**;
- products not significantly different may share letters;
- derive letters from the actual adjusted pairwise-comparison matrix;
- never fabricate letters visually.

## Guardrails

- Never treat 400 rows as independent.
- Do not use raw pairwise t-tests.
- Do not infer equality from p > 0.05.
- Do not call small significant differences practically important without context.
- Overall liking does not become purchase intent simply because the user asks a buying question.

## Approved Visual

Mean overall liking ± 95% CI; optional adjusted significance letters / forest plot.

---

# SOP-03 — Product Differences in Flavor Impact

## Required Inputs

- `flavor_impact`
- `product_code`
- `panelist_id`

## Use When

- “Which product has the strongest flavor?”
- “Do products differ in flavor impact?”
- “Is Product F more intense than G?”

## Analysis Type

**Confirmatory / hypothesis-oriented**

## Model

```text
flavor_impact ~ product_code + (1 | panelist_id)
```

Use SOP-02's mixed-model, G9-A, multiplicity, CI, and magnitude rules.

## Guardrail

Flavor impact is not liking, chocolate intensity, bitterness, overall quality, or purchase intent.

## Approved Visual

Mean flavor impact ± 95% CI.

---

# SOP-04 — Product Differences in Descriptive Sensory Attributes

## Use When

- “Is E higher in berry?”
- “Which product is more bitter?”
- “Which sensory attributes differentiate A and F?”
- “Do products differ in smoothness?”

## Required Inputs

For each requested attribute:

- the specific measured `desc_*` variable;
- `product_code`;
- `panelist_id`.

The exact attribute must pass G7-A.

Do not substitute a neighboring attribute.

## Analysis Type

**Confirmatory / hypothesis-oriented when prespecified; exploratory when screening many attributes.**

## Model

For each attribute:

```text
desc_attribute ~ product_code + (1 | panelist_id)
```

## Procedure

1. Identify measured attributes relevant to the question.
2. Confirm each requested attribute exists.
3. Fit attribute-wise mixed models.
4. Apply G9-A to each model.
5. Test product effect.
6. Estimate requested contrasts / product means.
7. Control multiplicity within the inferential family.
8. Report magnitude in 0–10 intensity units.

## Guardrails

- Do not run all attributes automatically for a narrow question.
- Do not call a descriptive intensity “JAR.”
- `desc_berry_note` cannot support a berry JAR penalty.
- Absence of `desc_protein_note` means quantitative protein-note differences cannot be tested.

## Approved Visuals

- selected attribute means ± CI;
- product × attribute heatmap for broader profile questions.

---

# SOP-05 — Product Sensory PCA

## Use When

- “How does the category map sensorially?”
- “Which attributes define the main sensory dimensions?”
- “How are products positioned in measured sensory space?”

## Required Inputs

- `product_code`
- at least two appropriate measured `desc_*` variables.

Only attributes that pass G7-A may enter the PCA.

## Analysis Type

**Exploratory multivariate**

## Unit of Analysis

Rows = product-level descriptive means.

Columns = measured descriptive sensory attributes.

## Procedure

1. Select the relevant measured `desc_*` variables.
2. Aggregate to product means.
3. Inspect zero variance.
4. Standardize attributes.
5. Fit PCA.
6. Report explained variance.
7. Interpret product positions and loadings jointly.
8. Apply G9-C to 2D interpretation.

## Guardrails

- Product metadata cannot substitute for measured sensory values.
- PCA distance does not prove significance.
- Do not imply causal drivers of liking from sensory PCA alone.
- Do not add unmeasured attributes as vectors.

## Approved Visual

PCA biplot with product scores, attribute loadings, and PC variance.

---

# SOP-06 — Product Sensory Similarity / Hierarchical Clustering

## Use When

- “Which products are most similar sensorially?”
- “Are there sensory families among products?”

## Required Inputs

- `product_code`
- measured `desc_*` sensory variables used to define similarity.

## Analysis Type

**Exploratory**

## Unit

Product-level standardized descriptive means.

## Preferred Method

Hierarchical clustering on the same defensible sensory representation used for category mapping.

## Procedure

1. Define measured sensory variables intentionally.
2. Aggregate to product means.
3. Standardize.
4. Compute transparent distance/linkage.
5. Plot dendrogram.
6. Interpret relative similarity, not population-level clusters.

## Guardrails

- Results depend on variable selection/scaling/linkage.
- Do not use metadata-only attributes.
- Do not claim statistically significant product groups from dendrogram appearance.

## Approved Visual

Dendrogram.

---

# SOP-07 — JAR Distribution Analysis

## Use When

- “Where are products off-JAR?”
- “Is Product F too bitter/dark?”
- “Are consumers saying G is too creamy?”

## Required Inputs

- the specific measured JAR variable;
- `product_code`.

A JAR analysis is unavailable for an attribute without a JAR field.

## Analysis Type

**Diagnostic / descriptive**

## Grouping

- 1–2 → Too Little
- 3 → JAR
- 4–5 → Too Much

## Procedure

1. Confirm the requested JAR item exists.
2. Preserve original responses.
3. Collapse only for reporting/analysis.
4. Calculate N and % in each category by product.
5. Use explicit denominators.
6. Identify directional imbalance.

## Guardrails

- JAR means are not enough to diagnose direction.
- Descriptive intensity is not a substitute for JAR.
- A high off-JAR incidence is not automatically a liking penalty; use SOP-08.

## Approved Visual

100% stacked bar: Too Little / JAR / Too Much.

---

# SOP-08 — JAR Penalty Analysis + Bootstrap

## Use When

- “What should we fix?”
- “Which JAR deviations are associated with lower liking?”
- “Is Too Much dark cocoa hurting Product F?”

## Required Inputs

For each product × attribute:

- a measured JAR variable;
- `overall_liking`;
- `product_code`;
- sufficient JAR/deviation groups.

All must pass G7-A before G9-B is evaluated.

## Analysis Type

**Diagnostic / associative**

## Core Penalty

For each direction:

```text
penalty = mean liking(JAR) - mean liking(deviation group)
```

## Procedure

1. Confirm the exact JAR attribute exists.
2. Group 1–2 / 3 / 4–5.
3. Apply G9-B group eligibility before calculating substantive penalty.
4. Calculate deviation prevalence.
5. Calculate mean liking penalty.
6. Bootstrap 95% CI with 9,999 resamples, BCa preferred, seed 42.
7. Classify workshop optimization priority only under G9-B threshold.
8. Report N_JAR, N_deviation, prevalence, penalty, CI, eligibility.

## Guardrails

- High penalty affecting 4% is not automatically important.
- High incidence with negligible drop is not automatically a liking driver.
- Diagnostic/associative, not causal proof.
- Do not perform penalty analysis on `desc_*` variables.
- Do not recommend a penalty analysis for an unmeasured JAR construct.

## Approved Visual

Penalty bubble plot:

- X = % consumers affected
- Y = mean liking penalty
- label = attribute/direction

---

# SOP-09 — Preference Drivers: IPM → EPM Preferred; PLS Optional

## Use When

- “What seems to be driving liking?”
- “Which sensory characteristics appear to explain preference?”
- “Are different consumers liking different things?”
- “Are mean scores hiding preference polarization?”

## Required Inputs

For IPM:

- `panelist_id`
- `product_code`
- `overall_liking`

For EPM:

- individual preference data;
- measured product-level `desc_*` sensory data.

PLS uses only measured sensory predictors.

## Analysis Type

**Exploratory multivariate / preference analysis**

## Preferred hierarchy

1. SOP-13 IPM
2. SOP-14 EPM
3. optional PLS sensitivity/modeling layer

Only 8 distinct products support product-level sensory modeling, so PLS coefficients/VIP-like rankings are not stable truth.

## Procedure

1. Assess preference heterogeneity with SOP-13.
2. Build measured descriptive sensory product space.
3. Use SOP-14 to relate preference structure to sensory space.
4. If specifically useful, fit highly parsimonious PLS and apply G9-G.

## Guardrails

Do not:

- jump from mean liking to one driver story when heterogeneity exists;
- describe EPM associations as causal;
- use unmeasured metadata as sensory predictors;
- use PLS to model an outcome like purchase intent if that outcome is absent;
- report unstable coefficients/VIPs as validated rankings.

## Approved Visuals

Internal preference map; external preference map; optional exploratory PLS loading/coefficient plot.

---

# SOP-10 — Nonlinear Sensory Relationship With Liking

## Use When

- “Is there an optimal bitterness level?”
- “Does liking fall when dark cocoa becomes excessive?”
- “Is the relationship curved?”

## Required Inputs

- `overall_liking`
- requested measured predictor
- `panelist_id`

## Analysis Type

**Hypothesis-oriented if prespecified; otherwise exploratory.**

## Model

```text
liking ~ attribute + attribute² + (1 | panelist_id)
```

## Procedure

1. Specify measured attribute before inspecting significance where possible.
2. Apply G9-D eligibility.
3. Fit linear model.
4. Fit quadratic model.
5. Apply G9-A diagnostics.
6. Compare model behavior/AIC.
7. Claim curvature/optimum only if G9-D passes.
8. Plot across observed range only.

## Guardrails

- No higher-order polynomial fishing.
- No extrapolation.
- No optimum claim outside well-supported observed data.
- Consider product-level confounding.
- Cannot be run on an absent construct.

## Approved Visual

Observed data + fitted curve with uncertainty band.

---

# SOP-11 — Product × Consumer Characteristic Interaction

## Use When

- “Do milk-chocolate preferrers like G more?”
- “Do dark-chocolate preferrers tolerate F better?”
- “Do frequent shake users respond differently?”
- “Does age/gender/income change product response?”

## Required Inputs

- outcome, usually `overall_liking`;
- `product_code`;
- requested measured moderator;
- `panelist_id`.

## Analysis Type

**Confirmatory when prespecified and adequately supported; otherwise exploratory.**

## Model

```text
overall_liking ~ product_code * moderator + (1 | panelist_id)
```

## Procedure

1. Confirm moderator exists.
2. Count subgroup N at unique-panelist level.
3. Apply G9-E before fitting/interpreting.
4. Fit interaction model.
5. Apply G9-A.
6. Test interaction.
7. Estimate relevant simple contrasts only if justified.
8. Correct multiplicity.
9. Report subgroup N and interpretation level.

## Guardrails

- Do not test every demographic automatically.
- Do not silently merge sparse categories.
- Do not infer population demographic truths from the mock sample.
- Do not invent a moderator such as purchase intent or product-specific purchase frequency.

## Approved Visual

Interaction plot with estimated liking + CI.

---

# SOP-12 — Consumer Preference Segmentation

## Use When

- “Are there different groups of consumers?”
- “Do some consumers prefer dark while others prefer milk chocolate?”
- “What preference segments exist?”

## Required Inputs

- `panelist_id`
- `product_code`
- `overall_liking`

## Analysis Type

**Exploratory**

## Input Matrix

Rows = consumers  
Columns = products  
Values = `overall_liking`

## Preferred Method

Validated KMeans.

## Procedure

1. Build complete consumer × product liking matrix.
2. Check missingness.
3. Decide/document standardization.
4. Fit candidate k under G9-F.
5. Calculate silhouette.
6. Check cluster sizes.
7. Assess resampling stability.
8. Retain only a solution passing all criteria.

## Hard post-fit validation halt

If no candidate passes:

> “The current liking data do not support a stable consumer segmentation.”

Do not name failed clusters or create substantive segmentation visuals.

Route to SOP-13 if heterogeneity remains relevant.

## Approved Visuals — only when retained solution passes

- consumer × product heatmap ordered by cluster;
- mean liking profile by cluster.

---

# SOP-13 — Internal Preference Mapping (IPM)

## Use When

- “Are consumers liking different things?”
- “Are mean liking scores hiding polarization?”
- “How heterogeneous are preferences?”
- “Which products attract different consumers?”

## Required Inputs

- `panelist_id`
- `product_code`
- `overall_liking`

## Analysis Type

**Exploratory**

## Primary Purpose

IPM is preferred first when understanding consumer preference heterogeneity.

It does not require sensory descriptive variables to construct the preference space.

## Input Matrix

Rows = consumers  
Columns = products  
Values = `overall_liking`

## Preferred Method

PCA-based internal preference mapping.

## Procedure

1. Build complete matrix.
2. Confirm one score per consumer × product.
3. Check missingness.
4. Center/transform only as defined.
5. Fit PCA to appropriate orientation.
6. Report explained variance.
7. Display product positions and consumer orientations where useful.
8. Assess common versus divergent preference directions.
9. Determine whether mean liking masks heterogeneity.
10. If useful and sensory inputs exist, route to SOP-14.

## Guardrails

IPM does not:

- prove discrete segments;
- establish sensory drivers by itself;
- establish significance;
- justify demographic labels;
- establish purchase intent.

Do not automatically cluster merely because the map shows spread.

## Approved Visual

Internal preference map.

---

# SOP-14 — External Preference Mapping (EPM)

## Use When

- “Which sensory characteristics appear to explain preference?”
- “What measured sensory attributes align with preferred products?”
- “How does sensory space relate to consumer liking?”

## Required Inputs

- individual consumer preference/liking data;
- `product_code`;
- product-level means for measured `desc_*` attributes.

## Analysis Type

**Exploratory associative**

## Preferred Role

EPM follows or complements IPM when the question is how measured sensory space aligns with heterogeneous consumer preference.

## Procedure

1. Confirm liking matrix and measured sensory variables exist.
2. Build product sensory space using measured descriptive means.
3. Standardize/document preprocessing.
4. Relate consumer preference orientations to sensory space using the approved mapping approach.
5. Report explained variance / map adequacy.
6. Interpret sensory directions associated with preference patterns.
7. Preserve heterogeneous directions rather than forcing one ideal.

## Guardrails

- Use measured sensory variables only.
- Do not overlay product metadata as though measured.
- Do not imply causality.
- Do not convert EPM preference into purchase likelihood.
- If a key explanatory attribute is only qualitative, report it separately rather than inventing a quantitative vector.

## Approved Visual

External preference map showing product positions and measured sensory directions.

---

# SOP-15 — Structured Open-Ended Comment Coding

## Use When

- “What are consumers saying?”
- “What did they like/dislike?”
- “What complaints recur?”
- “What did we fail to measure?”

## Required Inputs

- `likes` and/or `dislikes` as appropriate.

## Analysis Type

**Structured qualitative / diagnostic-exploratory**

## Procedure

### Step 1 — Inspect actual comments

Use the in-scope corpus, preserving missing values.

### Step 2 — Create a sensory-relevant codebook

Potential categories may include chocolate, cocoa, sweetness, bitterness, creaminess, smoothness, chalkiness/grittiness, artificial/candy, berry, aftertaste, protein/off-note.

These are examples only. Actual codes must be supported by comments.

### Step 3 — Freeze codebook

Apply the same rules to all comments. Do not change definitions product-by-product.

### Step 4 — Apply coding

Calculate count and % of valid comments mentioning each theme; distribution by product; likes versus dislikes when relevant.

### Step 5 — Optional triangulation

Compare coded themes with measured overall liking, measured JAR, or measured descriptive scores where appropriate.

## Mandatory coverage accounting

For **each field separately** (`likes`, `dislikes`), report:

1. expected responses;
2. valid nonmissing comments;
3. missing comments;
4. coded comments;
5. uncoded valid comments;
6. coding coverage = coded / valid;
7. uncoded rate = uncoded / valid.

Required format:

```text
Likes coverage:
- Expected: N
- Valid comments: N
- Missing: N
- Coded: N/N valid (X%)
- Uncoded: N/N valid (Y%)

Dislikes coverage:
- Expected: N
- Valid comments: N
- Missing: N
- Coded: N/N valid (X%)
- Uncoded: N/N valid (Y%)
```

If either:

```text
uncoded rate > 10%
```

include a coverage warning and review uncoded comments for missing recurring themes, ambiguity, or inconsistent coding.

If codebook changes, re-freeze and reapply to full in-scope corpus. Do not patch selectively after seeing product results.

## Guardrails

- Do not force ambiguous comments into codes.
- Do not convert theme mentions into sensory intensity scores.
- Do not treat a theme as a measured quantitative attribute.
- Do not infer “no dislike” from missing response.
- Do not let product metadata determine coding result.
- A recurring unmeasured theme can justify a **future measurement recommendation**, but not a current quantitative analysis.

## Approved Visuals

- horizontal theme-frequency bars;
- product × theme heatmap;
- liking by theme-present versus absent when appropriate and clearly associative.

---

# SOP-16 — Exploratory Topic Extraction With TF-IDF + NMF

## Current Workshop Dataset Status

**Do not run SOP-16 on the current Protein Shake workshop dataset.**

For this dataset, route open-ended theme analysis to **SOP-15 Structured Open-Ended Comment Coding**.

SOP-16 remains in the reviewed library only as a future-data method. It may be reactivated for a different dataset only when:

1. the research question specifically calls for unsupervised topic discovery;
2. the corpus passes G7-A;
3. the corpus passes the G9-I eligibility requirements; and
4. the method is intentionally selected rather than used as an automatic follow-up to SOP-15.

## Use When — Future Datasets Only

- “Can comments reveal unexpected topics?”
- “What themes emerge without a predefined codebook?”

## Required Inputs

A valid comment corpus meeting G9-I.

## Analysis Type

**Exploratory only**

## Procedure — When Reactivated

1. Use nonmissing comments.
2. Apply transparent cleaning.
3. Preserve sensory-relevant vocabulary.
4. TF-IDF vectorize.
5. Apply G9-I pre-fit eligibility.
6. Fit candidate 2–5 topic solutions across 20 seeds.
7. Apply cosine-stability and prevalence thresholds.
8. Human-review each retained topic.
9. Label only after review.
10. Compare with SOP-15 where useful.

## Hard halt

If G9-I fails:

> “The comment corpus does not support a meaningful unsupervised topic model.”

No downgrade-and-proceed.

Route to SOP-15.

## Guardrails

- Do not run SOP-16 for the current Protein Shake workshop dataset.
- NMF topics are not validated sensory constructs, measured variables, or causal drivers.
- Do not use an NMF topic as though it created a new quantitative dataset field.
- Do not invoke SOP-16 automatically because open-ended comments are available.

## Approved Visual — Future Datasets Only

Product × exploratory-topic heatmap only when the solution passes and SOP-16 has been intentionally reactivated.

---

# SOP-17 — Product-Level Comment Sentiment Profile

## Use When

Questions include:

- “Which products generate more enthusiastic positive comments?”
- “Which products generate harsher negative comments?”
- “How does the emotional strength of like/dislike language differ by product?”
- “Which products have unusually strong positive or negative comment language compared with the category?”
- “Which products appear polarizing in the language consumers use?”

Do **not** use this SOP merely to report the overall percentage of positive versus negative comments when the study design explicitly asks consumers to provide both likes and dislikes.

In prompted like/dislike studies, the useful signal is the **relative strength of the language by product within each prompt**, not the overall positive/negative split created by the questionnaire structure.

## Required Inputs

All are required:

- `panelist_id`
- `product_code`
- `likes`
- `dislikes`

`panelist_id` is required because uncertainty must preserve the repeated-measures design by resampling whole consumers rather than individual comments.

Missing individual comments are permitted and remain missing.

Do not substitute:

- `overall_liking` for sentiment;
- liking for purchase intent;
- JAR ratings for comment sentiment;
- descriptive sensory intensity for comment sentiment.

## Analysis Type

**Secondary exploratory**

This SOP describes the **language consumers use**, not product acceptance itself.

## Preferred Tool

```python
from nltk.sentiment.vader import SentimentIntensityAnalyzer

```

Use VADER compound scores for the primary product-relative metrics.

## Primary Quantities

Analyze `likes` and `dislikes` separately.

### 1. Like enthusiasm deviation

For each product:

```text
product mean VADER compound for likes
-
category mean VADER compound for likes

```

Interpretation:

```text
positive value
=
more enthusiastic positive-language tendency than the category average

negative value
=
less enthusiastic positive-language tendency than the category average

```

### 2. Dislike severity deviation

For each product:

```text
category mean VADER compound for dislikes
-
product mean VADER compound for dislikes

```

The direction is intentionally reversed so that:

```text
positive value
=
harsher / more negative dislike language than the category average

negative value
=
milder dislike language than the category average

```

### 3. Consumer-level bootstrap uncertainty

Use:

```text
2,000 bootstrap resamples
95% CI
RANDOM_STATE = 42

```

Resample **whole consumers**, including all available product evaluations belonging to the sampled consumer.

Do not bootstrap individual comments independently.

Bootstrap intervals quantify sampling uncertainty in the product-relative sentiment metric. They do **not** validate VADER as a sensory construct.

## Procedure

1. Apply the Dataset Capability Gate.
2. Confirm `panelist_id`, `product_code`, `likes`, and `dislikes` are present.
3. Verify one intended evaluation per consumer × product.
4. Report missingness and valid-comment coverage separately for `likes` and `dislikes`.
5. Remove only missing or blank comments from sentiment scoring.
6. Preserve likes and dislikes as separate prompted corpora.
7. Apply VADER to each valid comment.
8. Calculate product mean compound scores separately for likes and dislikes.
9. Calculate the category mean separately for each prompt.
10. Calculate:
    - like enthusiasm deviation;
    - dislike severity deviation.
11. Use whole-consumer bootstrap resampling to obtain 95% intervals for each product-relative deviation.
12. Compare products descriptively using their estimated deviations and uncertainty.
13. Create a two-dimensional product comment-language profile using:
    - x-axis = like enthusiasm deviation;
    - y-axis = dislike severity deviation.
14. State the domain limitations of generic sentiment analysis.
15. Do not convert the results into product liking, purchase intent, quality, or sensory-intensity claims.

## Product-Language Profile Interpretation

The two-dimensional profile may be described using these qualitative regions:

### More enthusiastic likes + milder dislikes

Relatively favorable **comment-language profile**.

Do not call this superior product acceptance unless supported separately by the appropriate liking analysis.

### More enthusiastic likes + harsher dislikes

Potentially **polarizing comment language**.

Consumers use comparatively strong language on both prompted positive and negative sides.

### Less enthusiastic likes + milder dislikes

Relatively **muted comment-language profile**.

The product does not evoke unusually strong positive or negative language.

### Less enthusiastic likes + harsher dislikes

Relatively unfavorable **comment-language profile**.

This describes language intensity only and is not itself a liking conclusion.

## Coverage and Eligibility

Evaluate valid-comment coverage for every:

```text
product × prompt

```

where prompt is:

- `likes`
- `dislikes`

### Hard stop

If:

```text
valid comments < 5

```

for any required product × prompt cell:

**HARD STOP** for the full product-profile analysis.

Do not report category-relative product sentiment as though the sparse product cell were stable.

Report the affected product, prompt, and valid N.

### Warning

If:

```text
5 <= valid comments < 20

```

for any product × prompt cell:

→ **WARNING**

The analysis may proceed, but:

- disclose the sparse cell;
- retain the bootstrap uncertainty;
- avoid strong ranking language;
- state that the estimate may be unstable.

These are **project operational thresholds for this workshop**, not universal sentiment-analysis standards.

## Study-Design Checks

Required checks include:

- unique `panelist_id × product_code` rows;
- number of consumers;
- number of products;
- products evaluated per consumer;
- missing likes;
- missing dislikes;
- product × prompt valid-comment counts.

### Duplicate evaluations

If duplicate consumer × product rows exist:

**HARD STOP**

Resolve the duplicate structure before analysis.

Do not silently allow duplicate evaluations to receive extra weight in consumer-level resampling.

### Incomplete product sets

If some consumers are missing one or more products:

→ **WARNING**, not automatic hard stop.

Continue using whole-consumer resampling, but disclose that product-specific denominators differ.

## Missing-Comment Rule

Missing or blank comments remain missing.

Do not interpret a missing dislike as:

- neutral sentiment;
- a mild dislike;
- “no dislike”;
- satisfaction;
- positive sentiment.

Do not interpret a missing like as negative or neutral.

All product-level denominators must use observed valid comments for that prompt.

## Multiplicity

SOP-17 does not define a family of null-hypothesis significance tests.

Therefore:

```text
Holm adjustment = not applicable

```

Do not create:

- significance letters;
- pairwise p-value matrices;
- “significantly more positive” claims.

Bootstrap intervals are used for estimation and uncertainty, not compact-letter significance grouping.

## Mandatory Limitation

VADER is a **generic-language sentiment model**, not a validated sensory-language model.

Sensory words such as:

- bitter;
- dark;
- strong;
- intense;
- thick;
- sweet;
- mild;

may be desirable, undesirable, or context-dependent in sensory research and may therefore be misread by a generic sentiment lexicon.

A strong VADER score indicates the polarity/intensity of the language according to the generic model. It does not prove how the sensory characteristic itself was perceived.

## Guardrails

- Sentiment must never be the primary explanation of product acceptance when structured sensory coding is available.
- Sentiment is not overall liking.
- Sentiment is not purchase intent.
- Sentiment is not repurchase likelihood.
- Sentiment is not product quality.
- Sentiment is not sensory intensity.
- Sentiment is not causality.
- Do not pool prompted `likes` and `dislikes` into one synthetic acceptance or sentiment score.
- Do not rank products as “best” or “worst” from SOP-17 alone.
- Product-relative scores describe **comment-language tendencies**, not validated product-performance effects.
- Bootstrap intervals do not validate VADER as a sensory construct.

## Relationship to Other SOPs

Use **SOP-15 Structured Open-Ended Comment Coding** when the question is:

> “What specifically are consumers talking about?”

SOP-15 identifies interpretable themes and sensory concepts.

Use SOP-17 when the question is:

> “How strongly are consumers expressing positive and negative language about each product?”

These analyses are complementary.

SOP-17 should not replace SOP-15 when transparent sensory-theme interpretation is needed.

Use **SOP-02** when the question is whether products differ in measured overall liking.

Do not infer a liking difference from SOP-17.

## Approved Visuals

### Primary visual

Category-relative product sentiment forest plots showing:

- like enthusiasm deviation + 95% consumer-bootstrap CI;
- dislike severity deviation + 95% consumer-bootstrap CI;
- zero as the category-average reference.

The direction of each axis must be labeled explicitly.

### Secondary visual

Product comment-language quadrant:

```text
x = like enthusiasm deviation
y = dislike severity deviation

```

Quadrant interpretation may distinguish:

- more enthusiastic likes / milder dislikes;
- more enthusiastic likes / harsher dislikes;
- less enthusiastic likes / milder dislikes;
- less enthusiastic likes / harsher dislikes.

Use the phrase **comment-language profile** rather than acceptance profile.

## Reporting Language

Preferred wording:

> “Product G generated more enthusiastic like-language than the category average, while its dislike-language was comparatively mild.”

> “Product F showed less enthusiastic positive language and harsher dislike-language relative to the category.”

> “Product C showed strong language in both its positive and negative comments, suggesting a comparatively polarizing comment-language profile.”

Avoid:

> “Consumers liked G significantly more.”

> “F has lower purchase intent.”

> “C is the most polarizing product.”

unless those claims are established by an appropriate approved analysis of the relevant measured construct.
---

# SOP-18 — Respondent-Level Data Quality Review

## Use When

- “Are there outliers?”
- “Did anyone respond strangely?”
- “Should any consumers be excluded?”

## Required Inputs

Depends on the requested QC check; only measured variables may be used.

## Analysis Type

**Data-quality diagnostic**

## Possible Checks

- evaluations per consumer;
- missingness;
- within-consumer response range/SD;
- repeated identical patterns;
- extreme scale use;
- product liking profiles;
- standardized values;
- PCA score inspection.

## Procedure

1. Check structural completeness.
2. Calculate consumer-level summaries.
3. Flag unusual patterns.
4. Inspect flagged records.
5. Do not remove automatically.
6. If exclusion is defensible, document rule/reason/observations.
7. If exclusion changes conclusions, report sensitivity.

## Guardrail

Outlier does not mean invalid respondent. Consumer disagreement is expected.

---

# SOP-19 — Exploratory Correlation Analysis

## Use When

- “What variables move together?”
- “Is bitterness correlated with liking?”
- “Are cocoa authenticity and chocolate intensity redundant?”

## Required Inputs

Both requested variables must be measured and pass G7-A.

## Analysis Type

**Exploratory**

## Procedure

1. Define variables based on question.
2. Choose Pearson or Spearman intentionally.
3. Report coefficient.
4. Control multiplicity where many inferential correlations are tested.
5. Interpret direction/strength, not causality.
6. Check product structure and G9-H when pooled relationships may confound product identity.

## Guardrail

A pooled correlation across product rows does not automatically establish a sensory driver.

A correlation cannot be run on an unmeasured construct by replacing it with a related variable.

## Approved Visual

Correlation heatmap or targeted scatterplot.

---

# SOP-20 — Effect Size and Estimated Product Difference

## Use When

- “How big is the difference?”
- “Is the difference meaningful, not just significant?”
- “How much higher is G than F?”

## Required Inputs

Same measured outcome and contrast structure as the corresponding approved inferential SOP.

## Analysis Type

**Confirmatory companion / estimation**

## Preferred Quantity

For repeated ratings:

- estimated paired/mixed-model mean difference;
- 95% CI.

If standardized effect requested, use a repeated/paired formulation and document the denominator.

## Procedure

1. Use same model/contrast as approved inferential SOP.
2. Estimate difference.
3. Report CI.
4. If standardized effect calculated, document exact denominator.
5. Do not use independent-sample Cohen's d without justification.

## Guardrail

Interpret practical importance in sensory scale units where possible.

Do not estimate an effect size for a construct absent from the dataset.

## Approved Visual

Forest plot of estimated differences + 95% CI.

---

# Integrated Query-to-SOP Examples

## Example 1 — “Which product is the winner?”

Clarify if needed.

If user means highest acceptance and `overall_liking` exists:

1. SOP-01 descriptive ranking;
2. SOP-02 statistical differences;
3. SOP-20 magnitude for important contrasts.

Do not infer purchase intent.

## Example 2 — “Why is Product F performing poorly?”

Possible measured components:

- SOP-02 acceptance;
- SOP-04 measured sensory differences;
- SOP-07 measured JAR distributions;
- SOP-08 eligible measured JAR penalties;
- SOP-15 dislike themes;
- SOP-09 only if broader preference/sensory association is requested.

Do not claim causality.

## Example 3 — “How does the category map?”

If sensory positioning:

- SOP-05 sensory PCA;
- optionally SOP-06 product clustering.

Do not add acceptance or purchase analysis unless separately requested and measured.

## Example 4 — “What should we fix in Product F?”

Primary:

- SOP-07 measured JAR distributions;
- SOP-08 eligible measured JAR penalties.

Support:

- SOP-04 measured descriptive differences;
- SOP-15 comments.

Do not recommend penalty analysis for an attribute without a JAR measure.

## Example 5 — “Are there different kinds of consumers?”

Clarify whether preference segments or observed demographics.

If preference segments: SOP-12.

If heterogeneity without forced clusters: SOP-13.

Do not force segmentation if stability is poor.

## Example 6 — “What did we fail to measure?”

Use SOP-15 and compare recurring themes with schema.

If a theme lacks a structured variable:

> “The qualitative data identified a recurring unmeasured sensory issue.”

Do not invent the missing quantitative variable.

## Example 7 — “Which product has highest purchase intent?”

G7-A:

- purchase intent absent → **UNSUPPORTED**.

Do not select SOP-02 and relabel liking.

Allowed response:

> “Purchase intent is not measured. The current data can separately compare overall liking, but that answers a different question.”

Future research may recommend collecting purchase intent.

## Example 8 — “What is Product E's berry JAR penalty?”

G7-A:

- `desc_berry_note` present;
- berry JAR absent → JAR penalty **UNSUPPORTED**.

Do not use `desc_berry_note` as a JAR proxy.

---

# Statistical Decision Checklist

Before running any approved SOP, answer:

1. What exactly is the research question?
2. What construct is required?
3. What exact input variables/design elements does the candidate SOP require?
4. Are all required inputs verified present in the active dataset and schema?
5. Is capability Supported, Partially Supported, or Unsupported?
6. If partially supported, what separable portion can be answered?
7. What is the outcome variable?
8. What is the unit of analysis?
9. Does the method respect repeated measures?
10. Is the analysis confirmatory, diagnostic, or exploratory?
11. Is multiplicity relevant?
12. What uncertainty measure is required?
13. What validation/diagnostic must pass?
14. What is the approved visualization?
15. What language is permitted for interpretation?
16. Does **Appendix A: Methods Not Used, and Why** prohibit any part of the workflow?
17. Does every proposed follow-up analysis also pass G7-A?
18. If an unsupported construct is useful strategically, has it been moved to Future Research rather than presented as executable now?

If the method cannot satisfy these checks, do not run it.

---

# Approved Python Stack

Preferred packages:

```python
import pandas as pd
import numpy as np
import scipy.stats as stats
from scipy.cluster.hierarchy import linkage, dendrogram
import statsmodels.api as sm
import statsmodels.formula.api as smf
from statsmodels.stats.multitest import multipletests
from sklearn.preprocessing import StandardScaler
from sklearn.decomposition import PCA, NMF
from sklearn.cross_decomposition import PLSRegression
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_score, adjusted_rand_score
from sklearn.model_selection import KFold, GroupKFold
from sklearn.feature_extraction.text import TfidfVectorizer
import matplotlib.pyplot as plt
```

Optional:

```python
from nltk.sentiment.vader import SentimentIntensityAnalyzer
```

Use additional packages only when the approved method genuinely requires them and the reason is documented.

---

# Preference-Analysis Priority Rule

For questions about drivers of liking or measured sensory characteristics associated with preference, default to:

1. SOP-13 IPM;
2. SOP-14 EPM;
3. SOP-09 optional PLS only when specifically useful.

Because only 8 products are available, product-level PLS coefficients must not be treated as highly stable.

This priority rule does not authorize any unmeasured outcome or predictor.

---

# Diagnostic Threshold Rationale Notes

The G9 cutoffs combine:

1. software-native diagnostics;
2. standard statistical diagnostic concepts;
3. conservative project-specific operating rules for this workshop dataset.

Examples of explicit project governance values include:

- ICC < 0.01;
- minimum JAR group N = 10;
- silhouette >= 0.25;
- median ARI >= 0.60;
- penalty >= 0.50 hedonic points;
- NMF corpus/vocabulary/prevalence floors (retained for future datasets; SOP-16 is inactive for the current workshop dataset).

They make behavior reproducible and conservative and must not be described as universal industry standards.

---

# Current Workshop Text-Analysis Routing Note

For the current Protein Shake workshop dataset:

- use **SOP-15** for structured qualitative theme analysis;
- **do not run SOP-16** TF-IDF + NMF topic extraction;
- use **SOP-17** only for product-relative comment-language strength, analyzing prompted likes and dislikes separately.

SOP-17 must not report the questionnaire-driven overall positive/negative split as the primary sentiment result.

---

# Final Governing Principle

The SOP library exists to ensure the analysis agent chooses statistics based on the **research question, dataset capability, and experimental design**, not based on whichever method produces the most interesting result.

Prefer:

> **the simplest reviewed method that robustly answers a question the current data can actually support**

over:

> **the most sophisticated method available.**

Valid outcomes include:

- products differ;
- no statistically detectable difference;
- a diagnostic issue is present;
- an exploratory pattern is plausible;
- a segment solution is unstable;
- comments reveal an unmeasured issue;
- a method hard-stops;
- or the requested construct is absent and the current dataset cannot answer the question.

All are acceptable analytical outcomes.

> **Method availability does not imply data availability.**


---

# Appendix A: Methods Not Used, and Why

This appendix documents analysis types and behaviors that are deliberately excluded from this workshop's methodology, and why — a record of the reviewed statistical guardrails, not an incident log.

When a request maps to one of these disallowed patterns, the closest approved alternative from the main SOP library above should be used instead, with the reason for the substitution stated.

## 1. Independent-Samples Tests That Ignore Repeated Measures

### DISALLOWED

Do not analyze the 400 product evaluations as though they came from 400 independent consumers.

Examples:

- independent-samples one-way ANOVA across products;
- independent-samples t-tests comparing products;
- independent Mann–Whitney tests between products;
- ordinary regression models that ignore repeated observations within `panelist_id`;
- any inferential model that assumes product evaluations from the same panelist are independent without explicit justification.

### Why

Every panelist evaluated every product.

The dataset contains:

- 50 unique panelists;
- 8 evaluations per panelist;
- 400 evaluation rows.

Observations within the same panelist are correlated. Ignoring this can distort standard errors, p-values, confidence intervals, and conclusions.

### Approved direction

Use an approved repeated-measures or mixed-effects SOP.

---

## 2. Uncorrected Mass Pairwise Testing

### DISALLOWED

Do not run many significance tests and interpret raw p-values without controlling multiplicity.

Examples:

- all pairwise product comparisons with no adjustment;
- separate tests across many descriptive attributes followed by selective reporting;
- many demographic interactions with only significant results retained;
- repeated testing until a desired p-value appears.

### Why

Multiple testing inflates false-positive risk.

### Approved direction

Use the multiplicity procedure specified in the relevant SOP. Where defined, Holm adjustment is the preferred default for families of pairwise comparisons.

---

## 3. P-Value Fishing / Significance Hunting

### DISALLOWED

Do not:

- repeatedly change models until significance is achieved;
- remove observations because they weaken significance;
- switch tests after seeing results solely to obtain a lower p-value;
- test arbitrary subgroup definitions and report only favorable findings;
- treat p = 0.051 and p = 0.049 as fundamentally different scientific outcomes;
- report significance without magnitude and uncertainty when the SOP requires them.

### Why

The goal is to answer the research question, not manufacture significance.

---

## 4. Causal Claims From Association

### DISALLOWED

Do not state that a sensory attribute **caused** a consumer response solely because variables are associated.

Disallowed examples:

- “Bitterness caused Product F to be disliked.”
- “Creaminess caused consumers to prefer Product G.”
- “The berry note caused Product E’s score.”
- “Protein off-notes caused Product H’s low liking” when based only on comments and liking.

### Approved language

Prefer:

- associated with;
- related to;
- corresponds with;
- accompanied by;
- consumers reporting X tended to rate Y lower/higher.

---

## 5. Inventing Unmeasured Variables

### DISALLOWED

Never create a measured variable that does not exist.

Specific prohibitions include:

- creating `desc_protein_note`;
- assigning protein-note intensity scores from comments;
- creating a protein JAR variable;
- creating numerical berry preference when only berry descriptive intensity exists;
- fabricating consumer responses or sensory attributes.

### Why

A qualitative theme is not equivalent to a measured quantitative attribute.

Protein/off-note information may be analyzed qualitatively under an approved text-analysis SOP, but must not be converted into an invented 0–10 intensity variable.

---

## 6. Using Product Metadata as Measured Evidence

### DISALLOWED

Do not use intended product-profile descriptions to prove statistical results.

Examples:

- declaring Product E significantly higher in berry because its metadata says “berry-led”;
- declaring Product F excessively bitter without evaluating measured bitterness;
- declaring Product G the winner because its profile says high acceptance;
- declaring Product H protein-forward solely because that was the intended profile.

### Why

Product-profile descriptions are study metadata. They may support interpretation, but they are not substitutes for measured evidence.

---

## 7. Unsupported External Benchmarks or Normative Claims

### DISALLOWED

Do not compare products to:

- industry norms;
- category benchmarks;
- competitor databases;
- national averages;
- historical company standards;
- external sensory databases

unless those sources are explicitly supplied and approved.

### Example

A mean liking score can be compared with other products in this dataset. It cannot be called “above category average” without an actual category benchmark.

---

## 8. Population Generalization Beyond the Study Sample

### DISALLOWED

Do not imply that the 50 participants represent:

- all protein-shake consumers;
- the U.S. population;
- all chocolate consumers;
- the commercial market;
- all members of a demographic subgroup.

### Approved language

Use:

- “among participants in this study”;
- “in this sample”;
- “these consumers”;
- “within the current dataset.”

---

## 9. Arbitrary Respondent Exclusion

### DISALLOWED

Do not automatically remove a respondent because they:

- look unusual;
- dislike most products;
- use the scale differently;
- reduce significance;
- appear as a PCA outlier;
- belong to a small demographic group;
- disagree with the majority.

### Approved direction

Quality-control procedures may flag respondents. Any exclusion requires:

- an explicit reason;
- a documented rule;
- a defensible data-quality basis;
- sensitivity analysis if exclusion changes the conclusion.

---

## 10. Treating Missing Qualitative Responses as “No Dislike”

### DISALLOWED

Do not convert a missing `dislikes` response into:

- “none”;
- “no dislikes”;
- positive sentiment;
- satisfaction;
- evidence that no sensory issue was perceived.

Missing qualitative data are simply missing unless another source establishes otherwise.

---

## 11. Mean JAR Score as the Sole JAR Interpretation

### DISALLOWED

Do not diagnose JAR performance solely from the arithmetic mean of a JAR variable.

### Why

Opposing groups can average to approximately 3 even when few consumers selected JAR.

### Approved direction

Use the response distribution:

- Too Little: 1–2;
- JAR: 3;
- Too Much: 4–5.

Penalty analysis must follow its approved SOP.

---

## 12. JAR Penalty Conclusions From Tiny Subgroups

### DISALLOWED

Do not describe a large liking drop as a robust optimization opportunity without reporting:

- subgroup N;
- subgroup prevalence;
- penalty magnitude;
- uncertainty where required.

A large mean drop from only a few consumers may be unstable.

---

## 13. PCA Used as a Significance Test

### DISALLOWED

Do not use PCA to claim:

- statistical significance;
- proof of product difference;
- causal sensory drivers;
- confirmed consumer segments.

Do not describe a loading as statistically significant merely because it is large.

### Approved direction

Use PCA to describe exploratory multivariate structure. Use approved inferential tests for confirmatory product differences.

---

## 14. Incorrect PCA Unit of Analysis

### DISALLOWED

For a **product sensory map**, do not automatically run PCA on all 400 consumer × product rows and interpret that as the eight-product category map.

### Approved direction

For the approved product sensory PCA:

- aggregate descriptive measures to product means;
- rows = products;
- columns = descriptive attributes;
- apply standardization according to the PCA SOP.

A different unit of analysis requires a different research question and approved method.

---

## 15. Forced Consumer Segmentation

### DISALLOWED

Do not:

- arbitrarily select a number of clusters because the story looks compelling;
- force every dataset into segments;
- label unstable clusters as meaningful consumer types;
- assign psychological or demographic identities to clusters without evidence;
- use a single KMeans run as proof of stable segmentation.

### Approved direction

Segmentation must follow the approved SOP and include validation such as:

- cluster-size review;
- silhouette evaluation;
- reproducibility;
- stability assessment where feasible;
- substantive interpretability.

If no stable solution exists, report that the data do not support robust segmentation.

---

## 16. Demographic Overinterpretation

### DISALLOWED

Do not:

- repeatedly slice the 50 consumers into many small groups;
- infer demographic explanations without an appropriate interaction test;
- present small-sample subgroup differences as population truths.

Demographic analyses may need to be labeled exploratory because total consumer N = 50.

---

## 17. Predictive Machine Learning Presented as Validated Prediction

### DISALLOWED

Do not train an arbitrary prediction model and claim it predicts future consumer acceptance unless a separate predictive-modeling SOP and proper validation design are approved.

Examples:

- random forest liking prediction;
- gradient boosting;
- neural networks;
- AutoML;
- row-level random train/test splitting that places the same consumer in both sets.

### Why

With repeated measures and only 50 consumers, naive row-level splitting can cause leakage.

### Approved direction

Use approved inferential, multivariate, preference, and diagnostic methods. PLS used for interpretable driver analysis must follow its own SOP.

---

## 18. Black-Box Topic Modeling as Ground Truth

### DISALLOWED

Do not:

- accept automatically generated topic labels without review;
- report machine-discovered topics as validated constructs;
- use complex neural topic models simply because they are available;
- treat a topic as equivalent to a measured sensory attribute.

### Approved direction

Structured, auditable coding is preferred. Exploratory TF-IDF/NMF topic extraction may be used only when justified and clearly labeled exploratory.

---

## 19. Generic Sentiment as the Primary Sensory Conclusion

### DISALLOWED

Do not use generic sentiment as the primary evidence for why consumers liked or disliked a product.

Words such as:

- bitter;
- dark;
- strong;
- intense;
- thick

may be sensory descriptors rather than inherently negative language.

### Approved direction

Structured sensory-topic coding and measured acceptance/JAR/descriptive data take precedence. Sentiment, if allowed by SOP, is secondary.

---

## 20. Unsupported Text-to-Number Conversion

### DISALLOWED

Do not convert open-ended comments into numerical sensory intensity ratings unless an approved, validated coding procedure explicitly defines the transformation.

Examples:

- “very bitter” = 8.5 bitterness;
- “protein taste” = 9 protein intensity;
- “smooth” = 7.2 smoothness.

This creates false precision.

---

## 21. Treating Correlation as a Complete Driver Analysis

### DISALLOWED

Do not declare a sensory attribute a “driver of liking” solely because its pooled correlation with overall liking is high.

### Why

The dataset contains:

- repeated consumers;
- systematic product differences;
- correlated sensory attributes.

A pooled correlation may confound product identity with sensory intensity.

### Approved direction

Correlations may be exploratory. Formal driver analysis must follow the approved SOP.

---

## 22. Unvalidated High-Dimensional Modeling

### DISALLOWED

Do not fit models whose complexity is unreasonable for the information available.

Examples:

- very large interaction models;
- too many predictors for the sample size;
- high-order polynomial models selected by trial and error;
- models that fail to converge but are still interpreted;
- PLS models with an arbitrary number of components.

### Approved direction

Use parsimonious models defined by approved SOPs. If the sample cannot support the question robustly, state that limitation.

---

## 23. Ignoring Model Warnings or Failed Diagnostics

### DISALLOWED

Do not report inferential results from a model that:

- fails to converge;
- produces a critical singular fit;
- generates serious numerical warnings;
- fails a required validity check;
- produces obviously unstable estimates

without addressing the issue.

A completed code run is not the same as a valid analysis.

---

## 24. Fabricated Significance Letters

### DISALLOWED

Do not add significance letters such as `A`, `AB`, or `B` to charts unless they were actually derived from an approved pairwise-comparison procedure.

Do not infer significance letters visually from overlapping means or confidence intervals.

---

## 25. Treating Non-Significance as Proof of Equality

### DISALLOWED

Do not interpret p > 0.05 as proof that products are equal.

### Approved language

Use:

> “No statistically detectable difference was observed under this analysis.”

Do not use:

> “The products are the same.”

Formal equivalence requires a separate approved equivalence-testing design and predefined margins.

---

## 26. Equivalence or Non-Inferiority Without Predefined Margins

### DISALLOWED

Do not perform or claim:

- equivalence;
- non-inferiority;
- superiority margins

unless:

- the question was defined in advance;
- a defensible margin was supplied;
- an approved SOP exists.

The agent must never invent a margin after seeing the data.

---

## 27. Unsupported Weighting or Reweighting

### DISALLOWED

Do not apply:

- demographic weighting;
- market weighting;
- usage-frequency weighting;
- post-stratification;
- custom consumer weights

unless the weights and rationale are explicitly supplied.

---

## 28. Silent Data Transformation

### DISALLOWED

Do not silently:

- recode categories;
- collapse demographic groups;
- drop categories;
- winsorize scores;
- transform liking scales;
- reverse-code variables;
- standardize variables;
- impute missing values;
- aggregate observations

when the transformation materially affects interpretation.

### Approved direction

Transformations required by an approved SOP may be performed, but they must be documented.

---

## 29. Analysis Outside the Available Variables

### DISALLOWED

If a user asks a question requiring information that is not measured, do not infer the answer from adjacent variables.

Examples:

- purchase intent;
- willingness to pay;
- brand loyalty;
- health perceptions;
- protein content;
- nutrition quality;
- ingredient preferences;
- sales;
- formulation composition;
- market share.

### Approved response

State that the dataset does not measure the requested construct. If useful, identify a narrower question that the available variables can answer.

---

## 30. External Facts Inserted Into Dataset Conclusions

### DISALLOWED

Do not enrich dataset conclusions with unsupported outside assumptions about:

- protein-shake trends;
- market chocolate preferences;
- demographic behavior;
- ingredient perceptions;
- brand performance;
- consumer health beliefs

unless the user explicitly requests external research and it is clearly separated from dataset analysis.

---

## 31. Unsupported “Best Product” Claims

### DISALLOWED

Do not declare a product universally “best” based on one metric.

Examples:

- highest mean liking does not prove universal superiority;
- highest %JAR does not prove best overall product;
- most central PCA position does not mean best;
- preference by the largest cluster does not mean best for everyone.

### Approved language

Prefer:

> “Product G had the highest mean overall liking in the current sample.”

rather than:

> “Product G is the best protein shake.”

---

## 32. Automatic Analysis Cascade

### DISALLOWED BY DEFAULT

Do not automatically run every available analysis simply because the dataset supports it.

The copilot is query-driven.

For a normal analytical question, do not automatically run:

- PCA;
- penalty analysis;
- clustering;
- preference mapping;
- demographic interactions;
- topic modeling;
- PLS;
- every possible product comparison.

Select the smallest defensible analysis that answers the user’s question, then use the corresponding approved SOP.

### Explicit Exception — Authorized Full Sweep

A multi-analysis cascade is permitted only when the user **explicitly requests a comprehensive analysis of the dataset or category**.

Examples of sufficient authorization include:

- “Run a full category appraisal.”
- “Give me the full analytical sweep.”
- “Analyze this dataset comprehensively.”
- “Run the workshop demo across the approved methods.”
- “Produce a complete category report.”

The copilot must not infer full-sweep authorization from a broad but answerable question such as:

- “Which product is best?”
- “What is going on with Product F?”
- “What drives liking?”

Those questions should still be routed to the smallest defensible workflow unless the user explicitly asks for a comprehensive analysis.

### Full-Sweep Guardrails

An authorized full sweep does **not** override any statistical rule.

During a full sweep:

- each analysis must still map to an approved SOP;
- each SOP’s eligibility criteria still apply;
- hard-stop rules remain hard stops;
- a stopped analysis must be reported as stopped, not forced to produce a result;
- exploratory analyses must remain labeled exploratory;
- the copilot must not add methods outside the approved SOP library merely to make the report more comprehensive;
- duplicate analyses that answer the same question should be avoided;
- the final output must follow the authorized full-sweep format in the reporting conventions below.

A full sweep authorizes **breadth of approved analysis**, not relaxation of rigor.

---

## 33. Bypassing an Approved SOP

### DISALLOWED

When an approved SOP exists, the copilot must not improvise a substantially different implementation without explaining why.

Examples:

- raw PCA instead of approved standardized product-mean PCA;
- ordinary regression instead of approved repeated-measures modeling;
- unvalidated KMeans instead of the approved segmentation workflow;
- raw JAR means instead of approved JAR distributions.

The SOP library represents reviewed methods and is part of the system’s quality assurance.

---

## 34. Guessing When the Analytical Question Is Ambiguous

### DISALLOWED

Do not choose between materially different statistical methods by guessing what the user meant.

Examples:

- “Which product is strongest?” could mean flavor impact, chocolate intensity, bitterness, or liking.
- “What drives Product G?” could mean sensory differentiation, acceptance drivers, or text themes.
- “Are A and F different?” does not identify the outcome.

### Approved direction

Ask the minimum number of clarifying questions required to select a defensible method.

---

## 35. Analysis Without Referencing the Source of Truth

### DISALLOWED

Do not analyze variables or scales without grounding their definitions in the designated source-of-truth files.

At minimum, the copilot should recognize:

- the data schema;
- the applicable statistical SOP;
- the output rules;
- the non-negotiables.

Correct code applied to an incorrectly interpreted variable is still an incorrect analysis.

---

# Decision Rule for Disallowed Requests

## A. Can the research question be answered with an approved method?

If yes:

- explain why the requested method is inappropriate;
- identify the approved alternative;
- proceed only when the intended question is sufficiently clear.

## B. Does the question require data that do not exist?

If yes:

- state that the dataset does not measure the construct;
- do not invent a proxy unless an approved SOP explicitly defines one;
- offer a narrower supported question if useful.

## C. Is the analysis technically possible but methodologically unstable?

If yes:

- explain the limitation;
- label any approved alternative as exploratory when appropriate;
- do not present the result as confirmatory.

## D. Is there no defensible way to answer the question?

Say so directly.

A correct refusal to overinterpret the dataset is a successful analytical outcome.

---

# Summary — Hard Stops

The copilot must never:

1. ignore the repeated-measures design;
2. fabricate variables, observations, or results;
3. use product metadata as measured evidence;
4. hunt for significance;
5. ignore multiplicity;
6. claim causality from association;
7. generalize beyond the supported sample;
8. force unstable consumer segments;
9. treat PCA or topic modeling as confirmatory proof;
10. convert qualitative comments into invented sensory intensity ratings;
11. use generic sentiment as primary sensory evidence;
12. remove respondents merely for being unusual;
13. treat missing comments as positive responses;
14. treat non-significance as proof of equality;
15. invent external benchmarks, margins, or weights;
16. silently alter the data;
17. run analyses unrelated to the user’s question;
18. bypass an approved SOP without justification;
19. answer a materially ambiguous analytical question by guessing;
20. report a result when the method has failed required validity checks.

## Governing Principle

> **If the data, design, or approved method does not support the conclusion, the copilot must not make the conclusion.**
