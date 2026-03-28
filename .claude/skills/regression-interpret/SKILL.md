---
name: regression-interpret
description: Interpret regression output from R, Stata, or Python. Explains coefficients, significance, effect sizes, and potential concerns in plain language suitable for a methods section or referee response.
argument-hint: "[output-file-or-paste] [method: OLS|DiD|IV|RDD|FE|logit|probit]"
allowed-tools: Read, Grep, Glob, Write, Edit, Bash
---

# Regression Interpret: From Output to Prose

Take regression output and produce a structured interpretation suitable for a paper's results section, a methods discussion, or a response to referees.

## Input

`$ARGUMENTS` — either a file path containing regression output or the user will paste output directly. Optionally specify the method (OLS, DiD, IV, RDD, FE, logit, probit). If not specified, infer from the output.

## Step 1: Parse the Output

Identify from the regression table or console output:
- Dependent variable
- Key independent variable(s) of interest (vs. controls)
- Coefficient estimates and standard errors
- Significance levels
- Sample size (N)
- R² or pseudo-R² (if reported)
- Fixed effects included
- Clustering level for standard errors
- Any first-stage statistics (if IV)

## Step 2: Substantive Interpretation

For each key coefficient:

1. **Direction and magnitude**: "A one-unit increase in X is associated with a [β] unit [increase/decrease] in Y"
2. **Translate to meaningful units**: If X is log, β is an elasticity. If Y is binary, interpret as percentage points. If both logged, it's an elasticity. State this explicitly.
3. **Statistical significance**: Report p-value or confidence interval. Note if marginally significant (0.05 < p < 0.10).
4. **Economic significance**: Is the effect large or small relative to the mean of Y? Relative to a standard deviation? Relative to effects found in comparable studies?
5. **Causal language**: Match language to the identification strategy. OLS with controls → "associated with." DiD/IV/RDD → "effect of" (if design is credible). Flag if the user uses causal language without a causal design.

## Step 3: Diagnostic Check

Flag any concerns visible in the output:

- **Few clusters**: If clustered SEs with < 50 clusters, note this and suggest wild bootstrap
- **Weak instruments**: If IV and first-stage F < 10, flag prominently
- **Overcontrolling**: If controls include potential mediators or post-treatment variables, flag
- **Collinearity signals**: If SEs are implausibly large or coefficients flip signs across specifications
- **Sample size drops**: If N differs notably across columns, ask why
- **R² suspiciously high**: In cross-country regressions, R² > 0.90 with few controls is unusual
- **Fixed effects absorbing variation**: If unit FE + time FE + treatment varies only cross-sectionally → identification concern

## Step 4: Draft Results Paragraph

Write a paragraph suitable for the paper's results section:

```
[Table/Figure X] reports [estimation method] estimates of the effect of [X] on [Y].
[Specification details — FE, controls, clustering].
The [preferred specification in column N] yields a coefficient of [β] ([SE]),
[significant at the P% level / not statistically significant].
This implies that [substantive interpretation in real-world units].
The magnitude is [comparison — e.g., "roughly half a standard deviation of Y"
or "comparable to the effect found by Author (Year)"].
[Any caveats or robustness notes].
```

## Step 5: Comparison Across Specifications (if multiple columns)

If the output contains multiple specifications (columns):

1. Note which is the **preferred** specification and why
2. Describe how the coefficient changes as controls/FE are added
3. Flag any sign changes or large magnitude shifts — these are red flags
4. Note if significance appears/disappears across specifications

## Output

Save to `quality_reports/regression_interpretation_[date].md` with:
- The parsed coefficient table
- Substantive interpretation
- Diagnostic flags
- Draft results paragraph
- Specification comparison (if applicable)

## Principles

- **Match causal language to design.** Never say "effect" for an OLS association.
- **Always translate to real units.** Economists think in standard deviations; readers think in dollars, years, percentage points.
- **Flag what you don't know.** If you can't assess instrument strength from the output shown, say so.
- **Be honest about precision.** A large point estimate with a wide CI is not a strong result.
- **Context matters.** A 2pp effect on voter turnout is meaningful; a 2pp effect on GDP growth is enormous.
