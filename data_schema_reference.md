# Protein Shake Category Appraisal — Data Reference Guide

## Purpose

This is the data-definition reference for the mock **Protein Shake Category Appraisal** dataset used in this workshop.

Dataset file: `protein_shake_category_appraisal_mock_redesigned.csv`

This guide defines:

- dataset grain and repeated-measures structure;
- variables and data types;
- valid response scales;
- observed category values and ranges;
- product codes and mock product personalities;
- qualitative-data constraints;
- the intentional structure of the redesigned mock data;
- the dataset capability boundary used to determine what analyses and conclusions are possible.

Use this guide together with the active dataset to understand what the data contain **before** interpreting or recommending any analysis.

---

# 1. Dataset Structure

**Unit of observation:** one panelist × one product evaluation.

The current dataset contains:

- **50 unique panelists**
- **8 blind-coded products:** A–H
- **8 evaluations per panelist**
- **50 evaluations per product**
- **400 total rows**
- **27 columns**
- **0 duplicate `panelist_id × product_code` rows**

Every panelist evaluated every product.

This is therefore a **complete repeated-measures consumer design**.

The 400 rows do **not** represent 400 independent consumers.

---

# 2. Redesigned Mock-Data Intent

This version of the mock dataset was deliberately redesigned so that each consumer behaves as a coherent individual across the eight product evaluations.

The data contain realistic consumer-level variation in areas such as:

- preference for milk versus dark chocolate character;
- tolerance for bitterness / dark cocoa;
- preferred sweetness;
- preference for creaminess and smoothness;
- acceptance of berry character;
- sensitivity to sensory deviations and off-notes.

These tendencies are represented through the consumers' actual response patterns.

There is **no explicit latent-segment or consumer-personality column** in the dataset.

Preference heterogeneity must be discovered from the observed data rather than read from a synthetic segment label.

The dataset was also designed to contain a mixture of:

- meaningful product differences;
- within-product preference relationships;
- optimization opportunities;
- consumer heterogeneity;
- genuine null or weak relationships;
- deliberately difficult/sparse analytical cases.

A method is not expected to produce a meaningful result for every possible question.

---

# 3. Column Groups

The 27 columns fall into seven conceptual groups:

1. panelist identifier;
2. demographics / category-use information;
3. product identifier;
4. consumer acceptance and impact measures;
5. Just-About-Right diagnostic measures;
6. descriptive sensory measures;
7. open-ended qualitative responses.

---

# 4. Panelist Identifier

## `panelist_id`

**Type:** integer
**Observed values:** 1–50

Unique consumer identifier.

The same `panelist_id` appears once for each of the eight products.

---

# 5. Demographic and Consumer Variables

Demographic variables are constant within each `panelist_id`.

When summarizing the consumer sample, use **one row per panelist**, not all 400 evaluation rows.

| Variable | Type | Definition | Observed values / range |
|---|---|---|---|
| `age` | Integer | Consumer age in years | 21–50 |
| `age_group` | Categorical | Age category | `21-29`, `30-39`, `40-49`, `50+` |
| `gender` | Categorical | Self-reported gender | `Female`, `Male`, `Non-binary` |
| `usage_frequency` | Categorical | Typical protein-shake consumption frequency | `Daily`, `Several times a week`, `Once a week`, `A few times a month`, `Rarely` |
| `chocolate_preference` | Categorical | Stated chocolate-style preference | `Milk chocolate`, `Dark chocolate`, `No strong preference`, `White / other` |
| `income_bracket` | Categorical | Approximate household income category | `Under $40k`, `$40k–$75k`, `$75k–$125k`, `$125k–$200k`, `Over $200k` |

## Current consumer counts

### Age group
- `21-29`: 22
- `30-39`: 20
- `40-49`: 7
- `50+`: 1

### Gender
- `Female`: 27
- `Male`: 22
- `Non-binary`: 1

### Usage frequency
- `Daily`: 7
- `Several times a week`: 19
- `Once a week`: 11
- `A few times a month`: 8
- `Rarely`: 5

### Chocolate preference
- `Milk chocolate`: 23
- `Dark chocolate`: 13
- `No strong preference`: 13
- `White / other`: 1

### Income bracket
- `Under $40k`: 4
- `$40k–$75k`: 23
- `$75k–$125k`: 17
- `$125k–$200k`: 3
- `Over $200k`: 3

Some demographic categories are intentionally sparse and should not automatically be used for inferential subgroup analysis.

---

# 6. Product Identifier

## `product_code`

**Type:** categorical string
**Values:** `A`, `B`, `C`, `D`, `E`, `F`, `G`, `H`

These are blind product codes.

## Mock Product Personalities

The following profiles describe the intended construction of the mock products.

They are **design metadata, not substitutes for statistical evidence**.

| Product | Mock product personality |
|---|---|
| **A** | Authentic darker-cocoa product: higher cocoa authenticity and bitterness, less sweet than the milk-chocolate products; attractive to some dark-chocolate-oriented consumers |
| **B** | Broadly appealing balanced milk-chocolate product with good smoothness and creaminess |
| **C** | Very sweet artificial/candy-forward chocolate product with strong artificial candy character and generally poor acceptance |
| **D** | Strong chocolate product with lower smoothness/creaminess and texture-related drawbacks |
| **E** | Distinct berry-forward chocolate product; preference varies meaningfully according to consumer acceptance of berry character |
| **F** | Extreme dark-cocoa product with very high cocoa authenticity and bitterness; excessive for most consumers, although tolerance varies |
| **G** | Creamy, smooth, balanced milk-chocolate alternative with broad acceptance |
| **H** | Product constructed to share D's measured descriptive sensory profile while having substantially poorer acceptance because of a protein/off-note issue represented only in qualitative comments |

## Important D vs H construction

For every panelist, Products **D and H have identical values on all nine measured `desc_*` variables** in the current mock dataset.

Their mean overall liking differs:

- Product D: **5.40**
- Product H: **3.42**
- Difference: **1.98 hedonic points**

The explanatory protein/off-note signal for H exists in open-ended qualitative feedback and **does not have a quantitative descriptive variable**.

This is an intentional feature of the mock dataset. Do not invent a `desc_protein_note` variable to explain the difference.

---

# 7. Consumer Acceptance Measure

## `overall_liking`

**Type:** integer / ordinal
**Valid scale:** 1–9
**Observed range:** 1–9

Nine-point hedonic scale:

| Score | Meaning |
|---:|---|
| 1 | Dislike extremely |
| 2 | Dislike very much |
| 3 | Dislike moderately |
| 4 | Dislike slightly |
| 5 | Neither like nor dislike |
| 6 | Like slightly |
| 7 | Like moderately |
| 8 | Like very much |
| 9 | Like extremely |

`overall_liking` is the primary consumer-acceptance outcome.

**It is not purchase intent, repurchase intent, willingness to pay, market preference, or likelihood to recommend.** Those constructs are not present in the current dataset.

---

# 8. Flavor Impact

## `flavor_impact`

**Type:** integer / ordinal intensity
**Valid scale:** 1–9
**Observed range in current data:** 4–7

Scale anchors: 1 = Extremely weak / no impact, 5 = Moderate, 9 = Extremely strong / high impact. Intermediate integers represent increasing perceived impact.

The observed data do not need to occupy every theoretically valid scale point.

`flavor_impact` is not interchangeable with liking, chocolate intensity, purchase intent, or quality.

---

# 9. Just-About-Right Variables

All JAR variables use a **five-point Just-About-Right scale**.

| Score | Interpretation |
|---:|---|
| 1 | Much too little |
| 2 | Too little |
| 3 | Just about right |
| 4 | Too much |
| 5 | Much too much |

## JAR columns

| Variable | Definition |
|---|---|
| `jar_sweetness` | JAR assessment of sweetness |
| `jar_chocolate_flavor` | JAR assessment of chocolate flavor intensity |
| `jar_cocoa_dark_note` | JAR assessment of cocoa / dark-chocolate character |
| `jar_creaminess` | JAR assessment of creaminess / mouthfeel thickness |
| `jar_smoothness` | JAR assessment of smoothness / absence of chalkiness or graininess |
| `jar_saltiness` | JAR assessment of saltiness / mineral character |

## Standard grouped interpretation

When required by an approved SOP: **Too Little** = 1–2, **JAR** = 3, **Too Much** = 4–5. The original 1–5 responses must remain preserved.

## Observed ranges

- `jar_sweetness`: 1–5
- `jar_chocolate_flavor`: 1–5
- `jar_cocoa_dark_note`: 1–5
- `jar_creaminess`: 1–5
- `jar_smoothness`: 1–5
- `jar_saltiness`: 2–5

JAR responses in this redesigned dataset are intended to reflect each consumer's product perception relative to their own preferred level, creating meaningful within-product variation for optimization analyses.

There is **no berry JAR variable** and **no protein/off-note JAR variable**.

---

# 10. Descriptive Sensory Variables

All descriptive sensory variables use a **0–10 intensity scale** (0 = not at all/absent, 5 = moderate, 10 = extremely strong/high).

## Descriptive columns

| Variable | Definition | Observed range |
|---|---|---:|
| `desc_sweetness` | Perceived sweetness intensity | 2.2–9.0 |
| `desc_chocolate_intensity` | Perceived chocolate flavor intensity | 4.5–9.8 |
| `desc_cocoa_authenticity` | Perceived authentic cocoa / dark-cocoa character | 2.3–10.0 |
| `desc_creaminess` | Perceived creaminess / thick mouthfeel | 3.4–9.2 |
| `desc_smoothness` | Perceived smoothness; higher = smoother / less chalky or grainy | 2.3–9.8 |
| `desc_artificial_candy_note` | Artificial, candy-like, bubblegum or fruity-note intensity | 0.0–8.7 |
| `desc_bitterness` | Bitterness / dark-chocolate bitterness intensity | 0.6–10.0 |
| `desc_saltiness` | Saltiness / mineral-note intensity | 1.0–4.1 |
| `desc_berry_note` | Berry-character intensity | 0.0–9.4 |

## Berry constraint

`desc_berry_note` is a descriptive sensory variable only. There is no berry JAR variable. A descriptive berry intensity cannot be silently substituted for a berry JAR response.

## Protein-off-note constraint

There is no `desc_protein_note`, protein JAR variable, or quantitative protein-off-note measure. Protein/off-note information is represented qualitatively in comments only. A comment theme about protein/off-note cannot be treated as a quantitative protein intensity measurement.

---

# 11. Open-Ended Qualitative Variables

## `likes`

**Type:** free-text string. Describes what the consumer liked about the product.

Current dataset: 400 nonmissing responses, 0 missing responses.

## `dislikes`

**Type:** free-text string. Describes what the consumer disliked about the product.

Current dataset: 395 nonmissing responses, 5 missing responses.

Missing dislike comments must remain missing. They must not automatically be interpreted as no dislikes, product satisfaction, positive sentiment, or absence of an off-note.

Qualitative themes may identify issues that were not measured structurally. They do **not** create new quantitative variables.

---

# 12. Current Product-Level Validation Snapshot

The following means are provided as a **dataset-integrity snapshot**, not as a substitute for formal statistical analysis.

| Product | Mean overall liking | Mean flavor impact | Key measured sensory signature |
|---|---:|---:|---|
| A | 6.40 | 5.58 | dark cocoa / authentic cocoa / moderate bitterness |
| B | 7.60 | 5.06 | balanced, creamy, smooth milk-chocolate profile |
| C | 3.60 | 5.10 | very sweet, high artificial/candy character |
| D | 5.40 | 5.68 | strong chocolate, lower creaminess/smoothness |
| E | 5.94 | 5.30 | high berry intensity |
| F | 2.94 | 6.20 | extreme cocoa authenticity and bitterness |
| G | 7.40 | 4.98 | high creaminess and smoothness, lower bitterness |
| H | 3.42 | 5.68 | same measured descriptive profile as D; additional unmeasured qualitative protein/off-note issue |

These values may be used to detect whether the wrong dataset has been loaded. They must not replace the appropriate inferential or diagnostic method when answering a research question.

---

# 13. Missingness and Structural Validation

Expected current validation:

| Check | Expected result |
|---|---:|
| Rows | 400 |
| Columns | 27 |
| Unique panelists | 50 |
| Unique products | 8 |
| Evaluations per panelist | 8 |
| Evaluations per product | 50 |
| Duplicate `panelist_id × product_code` rows | 0 |
| Missing structured numeric/categorical values | 0 |
| Missing `likes` | 0 |
| Missing `dislikes` | 5 |
| Demographic inconsistencies within panelist | 0 |

If an uploaded dataset differs from these values, flag the discrepancy rather than silently assuming it is the same mock dataset.

---

# 14. Current Column Order

1. `panelist_id` 2. `age` 3. `age_group` 4. `gender` 5. `usage_frequency` 6. `chocolate_preference` 7. `income_bracket` 8. `product_code` 9. `overall_liking` 10. `flavor_impact` 11. `jar_sweetness` 12. `jar_chocolate_flavor` 13. `jar_cocoa_dark_note` 14. `jar_creaminess` 15. `jar_smoothness` 16. `jar_saltiness` 17. `likes` 18. `dislikes` 19. `desc_sweetness` 20. `desc_chocolate_intensity` 21. `desc_cocoa_authenticity` 22. `desc_creaminess` 23. `desc_smoothness` 24. `desc_artificial_candy_note` 25. `desc_bitterness` 26. `desc_saltiness` 27. `desc_berry_note`

---

# 15. Dataset Capability Boundary

A construct is **available for analysis** only when its required input field(s) are present in the dataset and defined in this guide. If a construct is absent, treat it as **not measured** — do not infer that it exists because it is common in consumer research, would be useful for the business question, or a comment hints at it.

## Capability classifications

- **SUPPORTED** — all required inputs verified present.
- **PARTIALLY SUPPORTED** — a separable portion of the question is supported; only that portion may be analyzed, and the missing input must be named explicitly.
- **UNSUPPORTED** — a required input is absent; do not run or recommend the analysis as executable on the current dataset.

## No automatic proxy substitution

A nearby construct is not automatically an acceptable proxy. Examples: `overall_liking` is not purchase intent or repurchase intent; `usage_frequency` is not category involvement or product-specific purchase frequency; `desc_berry_note` is not berry JAR; protein/off-note language in `dislikes` is not `desc_protein_note`; `flavor_impact` is not overall sensory quality.

## Future-study distinction

An absent construct may be mentioned only as a clearly separated **future research / future data collection** opportunity (e.g., "a future study could collect purchase intent..."), never phrased as an executable next step on the current data.

---

# 16. Interpretation Rules for This Dataset

- a row is an evaluation, not an independent consumer;
- `panelist_id` identifies repeated observations;
- demographics should be summarized at the unique-panelist level;
- `overall_liking` is the primary acceptance outcome;
- JAR ratings are consumer diagnostics relative to perceived ideal;
- `desc_*` variables are measured sensory intensities;
- `likes` and `dislikes` are qualitative evidence;
- product personalities are mock design metadata, not measured evidence;
- preference heterogeneity must be discovered from responses, not assumed from a hidden segment label;
- qualitative themes must not be converted into invented quantitative variables;
- D and H intentionally demonstrate that the structured sensory questionnaire does not measure every possible reason for product acceptance;
- every analysis and every suggested follow-up must first be checked against the variables actually available.

---

# Governing Data Principle

> **Use the observed data as evidence, this guide as definition, and the product personalities only as mock-study context.**

This guide defines what the variables mean and what constructs are actually available. The statistical methods reference (companion document) determines **how** an eligible research question should be analyzed.

> **Method availability does not imply data availability.**
