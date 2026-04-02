---
name: regression-interpret
description: "Interpret regression output from R or Python. Explains coefficients, significance, effect sizes, and concerns in plain language for a methods section or referee response."
argument-hint: "[output-file-or-paste] [method: OLS|DiD|IV|RDD|FE|logit|probit]"
disable-model-invocation: true
allowed-tools: Read, Grep, Glob, Write, Edit
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

## Step 2: Design Credibility Check

Before interpreting magnitudes, assess whether the specification is credible:

- What identification strategy is being used (OLS, DiD, IV, RDD, FE)?
- Are there obvious design concerns visible in the output (weak instruments, few clusters, post-treatment controls)?
- If critical concerns are found, flag them prominently before proceeding — magnitudes are meaningless if the design is flawed. If the design itself needs rethinking, recommend `/strategize` before continuing interpretation.

## Step 3: Substantive Interpretation

For each key coefficient:

1. **Direction and magnitude**: "A one-unit increase in X is associated with a [β] unit [increase/decrease] in Y"
2. **Translate to meaningful units**: If X is log, β is an elasticity. If Y is binary, interpret as percentage points. If both logged, it's an elasticity. State this explicitly.
3. **Statistical significance**: Report p-value or confidence interval. Note if marginally significant (0.05 < p < 0.10). For null results, interpret the confidence interval width — is it informative (tight CI rules out meaningful effects) or uninformative (wide CI, likely underpowered)?
4. **Economic significance**: Is the effect large or small relative to the mean of Y? Relative to a standard deviation? Relative to effects found in comparable studies? If the mean/SD of Y is not visible in the output, ask the user to provide it.
5. **Causal language**: Match language to the identification strategy. OLS with controls → "associated with." DiD/IV/RDD → "effect of" (if design is credible). Flag if the user uses causal language without a causal design.
6. **Interaction terms**: If the specification includes X × Z, interpret the marginal effect of X at different values of Z — not the raw interaction coefficient alone. For DiD (which is an interaction of treatment × post), interpret the interaction as the treatment effect. For subgroup analyses, note that comparing coefficients across separate regressions is not the same as testing an interaction — if the goal is to test whether effects differ, an interaction specification is needed.
7. **Nonlinear models**: For logit/probit, distinguish between reporting odds ratios and marginal effects — state which is being shown and what it means in probability terms. For other nonlinear models (Poisson, negative binomial), interpret coefficients as semi-elasticities or incidence rate ratios as appropriate.
8. **IV output**: If two-stage output is shown, interpret the first-stage coefficient and instrument relevance (F-statistic), then the second-stage causal estimate. The second stage is the headline result; the first stage is the credibility check.

## Step 4: Diagnostic Check

Flag any concerns visible in the output:

- **Few clusters**: If clustered SEs with < 50 clusters, note this and suggest wild bootstrap
- **Weak instruments**: If IV and first-stage F < 10, flag prominently
- **Overcontrolling**: If controls include potential mediators or post-treatment variables, flag
- **Collinearity signals**: If SEs are implausibly large or coefficients flip signs across specifications
- **Sample size drops**: If N differs notably across columns, ask why
- **R² suspiciously high**: In cross-country regressions, R² > 0.90 with few controls is unusual
- **Fixed effects absorbing variation**: If unit FE + time FE + treatment varies only cross-sectionally → identification concern. Note that coefficients on time-invariant variables are absorbed by unit FE and cannot be estimated.
- **Multiple hypothesis testing**: If many coefficients are being tested (e.g., heterogeneity across subgroups), flag the risk of false discoveries and suggest adjustment (Bonferroni, Benjamini-Hochberg, or Romano-Wolf)

## Step 5: Draft Results Paragraph

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

## Step 6: Comparison Across Specifications (if multiple columns)

If the output contains multiple specifications (columns):

1. Note which is the **preferred** specification and why
2. Describe how the coefficient changes as controls/FE are added
3. Flag any sign changes or magnitude shifts >50% from the baseline specification — these are red flags
4. Note if significance appears/disappears across specifications

## Output

Respond in the conversation. If the user asks, save to `quality_reports/regression_interpretation_[date].md` with:
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
