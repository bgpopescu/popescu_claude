---
name: robustness-battery
description: Run a systematic battery of robustness checks on a main specification. Tests alternative samples, controls, estimators, SEs, and functional forms. Produces a summary table and specification curve.
argument-hint: "[main-script-path] [language: R|Python]"
disable-model-invocation: true
allowed-tools: Read, Grep, Glob, Write, Edit, Bash
---

# Robustness Battery: Systematic Specification Testing

Take a main regression specification and systematically test its sensitivity to reasonable alternatives. Produces a robustness report with summary table and specification curve data.

## Input

`$ARGUMENTS` — path to the main analysis script and optionally the language (R, Python). If language not specified, infer from file extension.

## Step 1: Understand the Main Specification

Read the script and identify:
1. **Dependent variable** (Y)
2. **Key independent variable** (X — the treatment or variable of interest)
3. **Controls** (W)
4. **Fixed effects** (FE)
5. **Sample restrictions** (filters)
6. **Standard error clustering level**
7. **Estimator** (OLS, logit, Poisson, IV, etc.)
8. **The main result**: coefficient, SE, significance, N
9. **Data path**: Trace the data loading chain — find the actual dataset path and ensure robustness scripts can access it from `code/robustness/`

Document this as the "baseline specification" in the output report.

## Step 2: Design the Robustness Battery

If a strategy memo exists at `quality_reports/strategy/strategy_memo.md` (from `/strategize`), read its robustness plan and use it as a starting point. Otherwise, design checks from scratch.

Generate variations along these dimensions. Use judgment about which apply to the specification at hand — not every dimension is relevant to every design.

| Dimension | Examples |
|-----------|----------|
| **Sample** | Winsorize, drop outliers, split by period, leave-one-out, balanced panel |
| **Controls** | Bivariate, baseline only, full, drop-one-at-a-time |
| **Fixed effects** | None, unit, time, two-way, region × time |
| **Standard errors** | Robust, clustered (main level, higher level, two-way), wild bootstrap if <50 clusters |
| **Estimator** | Alternatives appropriate to the design (e.g., CS/SA for staggered DiD; LIML for IV; varying bandwidths for RDD) |
| **Functional form** | Log transforms, IHS, binary treatment cutoffs |
| **Placebo tests** | Permutation, pre-treatment outcomes, implausible DVs, shifted treatment timing |

Prioritize placebos and estimator alternatives — these are the checks referees care most about. Skip variations that are meaningless for the design (e.g., Poisson with a continuous Y).

## Step 3: Generate the Scripts

Create robustness scripts in `code/robustness/` (create the directory if it doesn't exist):

```
code/robustness/
├── 01_sample_variations.R
├── 02_control_variations.R
├── 03_fe_variations.R
├── 04_se_variations.R
├── 05_estimator_variations.R
├── 06_functional_form.R
├── 07_placebo_tests.R
└── run_all_robustness.R
```

Adapt file extensions to the language (`.R` for R, `.py` for Python). Each script should:
- Replicate the main script's setup: library loads, custom functions, data transformations
- Source or load the same data as the main specification
- Run each variation
- Store results (coefficient, SE, p-value, N, specification label) in a data frame
- Save results to `output/robustness/`

`run_all_robustness.R` runs everything and compiles results.

## Step 3.5: Run and Debug

Run each script. For each:

1. Execute the script
2. If it errors, read the error, fix the script, and retry
3. Only move to the next script after the current one runs cleanly
4. Run `run_all_robustness.R` last to verify everything works end-to-end

Do not proceed to Step 4 until all scripts execute without errors.

## Step 4: Compile Results

Create a summary table:

| Specification | β | SE | p | N | Sig? |
|--------------|---|----|----|---|------|
| **Baseline** | X.XX | X.XX | X.XX | XXXX | *** |
| No controls | ... | ... | ... | ... | ... |
| Drop outliers (1%) | ... | ... | ... | ... | ... |
| ... | | | | | |

And a specification curve dataset (for plotting):
- One row per specification
- Columns: coefficient, CI_lower, CI_upper, specification_id, dimension (sample/controls/FE/SE/estimator)

## Step 5: Interpret and Flag

In the report, classify specification **stability** (not result quality — a robust result from a flawed design is still flawed):

- **ROBUST**: Coefficient same sign, similar magnitude, still significant across most variations
- **SENSITIVE**: Coefficient changes notably (>50% magnitude change) or loses significance in some reasonable specifications
- **FRAGILE**: Coefficient changes sign, becomes insignificant in many specifications, or placebo tests show effects

## Output

Save to `quality_reports/robustness_report_[date].md`:

1. Baseline specification summary
2. Full results table (all variations)
3. Specification curve data (for plotting)
4. Interpretation: robust, sensitive, or fragile — with specifics
5. Recommended actions (if sensitive/fragile)

Save scripts to `code/robustness/`.
Save output data to `output/robustness/`.

## Principles

- **Run what makes sense.** Don't blindly apply all variations — a Poisson model with a continuous Y is meaningless.
- **The baseline must be clearly identified.** Every comparison is relative to it.
- **Report everything.** Don't hide unfavorable results. A referee will run the same checks.
- **Placebo tests are the most important.** If a placebo shows an effect, the main result is suspect.
- **Specification curves beat tables.** When possible, suggest the user plot the results.
- **Flag but don't panic.** Some sensitivity is expected. The question is whether reasonable alternatives overturn the result.
