---
name: strategize
description: "Design a causal identification strategy before writing code. Produces a strategy memo, pseudo-code, robustness plan, and falsification tests, then audits for identification validity."
argument-hint: "[research-question-or-project-description]"
disable-model-invocation: true
allowed-tools: Read, Write, Grep, Glob, Edit, Bash
---

# Strategize: Research Design Before Code

You are the methods coauthor who says "given this question and this data, here's how we get a causal answer" — and then stress-tests every assumption before anyone writes a line of code.

This skill has two phases: **Design** (propose the strategy) and **Audit** (critique it). Both happen in one pass.

## Input

`$ARGUMENTS` — a research question, project description, or path to a README/paper. If a README.md or strategy memo already exists, read it first for context.

Before designing, check whether the project has:
- A literature review or annotated bibliography (from `/discover-lit`)
- A data assessment (from prior work or `/discover-lit`)
- Existing analysis scripts in `code/` — read them to avoid proposing a strategy that contradicts work already done

Use whatever context exists. If none exists, ask the user for: (1) the research question, (2) the data they have or plan to use, (3) the population and context.

Use Bash only for data inspection (loading, checking structure, counting observations) — not for estimation or analysis.

---

## Phase 1: Design the Strategy

### Step 1: Assess the Identification Landscape

- What is the **ideal experiment** you'd run if you could?
- How far is the available data from that ideal?
- What is the source of **exogenous variation**?
- What causal estimand are you targeting: ATT, ATE, LATE, CATE?

### Step 2: Propose Candidate Strategies (ranked by credibility)

For each candidate, specify:

| Element | What to Define |
|---------|---------------|
| **Design** | DiD, IV, RDD, Synthetic Control, Event Study, Selection-on-Observables |
| **Estimand** | ATT, ATE, LATE, CATE — what exactly are you estimating? |
| **Treatment** | Precise, operational definition |
| **Control group** | Who, and why them |
| **Key assumptions** | Parallel trends, exclusion restriction, continuity, SUTVA, etc. |
| **Testable implications** | Pre-trends test, balance, McCrary, placebo |
| **Threats** | What could invalidate this design |
| **Data requirements** | Does the available data support this? Is variation in treatment sufficient for detection at plausible magnitudes? |

Recommend a **primary strategy** and one or two **robustness alternatives**:
- "Lead with DiD, robustness check with Synthetic Control"
- "IV as primary, reduced form as supporting evidence"

### Step 3: Specify the Estimation Approach

- Recommended estimator and package (R: `fixest`, `did`, `rdrobust`; Python: `linearmodels`, `statsmodels`)
- Functional form choices (linear, log, etc.)
- Fixed effects structure
- Clustering level and justification
- Sample restrictions

### Step 4: Plan Robustness Checks

For each check, specify what it tests and what a failure would mean:

- Alternative control groups
- Alternative fixed effects
- Alternative standard errors (clustering levels)
- Alternative estimators (e.g., Callaway-Sant'Anna vs. Sun-Abraham for staggered DiD)
- Bandwidth sensitivity (RDD)
- Placebo treatments (in time or across units)
- Specification curve across reasonable alternatives

### Step 5: Design Falsification Tests

Tests that should produce null results if the design is correct:
- Placebo outcomes (variables that should not be affected)
- Placebo treatments (dates or groups where no effect should exist)
- Pre-treatment balance tests
- Permutation/randomization inference

### Step 6: Anticipate Referee Objections

List the **top 3 things a referee will attack** and pre-plan a response or test for each. Think like a hostile but fair reviewer. Common objections include: "What's the mechanism?" — can you articulate a plausible causal channel?

---

## Checkpoint

Present the strategy memo to the user and confirm alignment before proceeding to the audit. The design is consequential — the user should review it before it gets critiqued.

## Phase 2: Audit the Strategy

After completing the design, switch perspective to a methods referee and audit your own strategy. Execute these four phases **sequentially** — do not skip ahead.

### Audit Phase 1: Claim Triage

List every causal claim the strategy implies:

| Claim | Design | Estimand | Treatment | Control | Outcome |
|-------|--------|----------|-----------|---------|---------|

If no causal claims exist, stop and report: "No causal identification — this is a descriptive study."

### Audit Phase 2: Core Design Validity

Run the appropriate design-specific checklist:

**DiD checklist:**
- Parallel trends assumption stated and testable?
- Treatment timing sharp or gradual?
- Anticipation effects possible?
- SUTVA: spillovers or general equilibrium effects?
- Functional form justified?

**IV checklist:**
- Exogeneity argued from theory (not just tests)?
- Exclusion restriction: IV affects outcome ONLY through treatment?
- First-stage F > 10 (ideally > 100)?
- Complier population well-defined?
- Monotonicity: no defiers?

**RDD checklist:**
- Running variable manipulation (McCrary test)?
- Continuity at cutoff: other policies or shocks at same threshold?
- Bandwidth sensitivity?
- Local treatment effect — generalizability limited?

**Event Study checklist:**
- Genuine staggering in treatment timing?
- Pre-event coefficients flat and near zero?
- Reference period specified?
- Binning of endpoints reasonable?

**Selection-on-Observables checklist:**
- Covariate balance (SMD < 0.1)?
- Common support/overlap?
- Sensitivity analysis (Rosenbaum bounds, Altonji ratio)?

**Mandatory sanity checks (all designs):**
- Sign economically plausible?
- Magnitude reasonable (not implausibly large)?
- Dynamics show credible causal trajectory?
- Specifications yield consistent estimates?

### Audit Phase 3: Inference Soundness

Complete this phase even if Phase 2 found critical issues — note which findings are conditional on resolving design concerns.

- Clustering level matches variation in treatment?
- Enough clusters (>50 for reliable cluster-robust SEs)?
- Multiple testing corrections needed?
- Code specification matches the stated model?

### Audit Phase 4: Completeness

Complete regardless of prior findings — note dependencies.

- Robustness plan covers key threats?
- Falsification tests address the right assumptions?
- Limitations acknowledged?

### Severity Classification

- **CRITICAL**: Identification is broken (assumption violated, sanity check fails)
- **MAJOR**: Missing key check, but design credible if resolved
- **MINOR**: Could strengthen, but design holds

---

## Output

Save all outputs to `quality_reports/strategy/`:

1. **`strategy_memo.md`** — the complete design document with sections for: question, design, estimand, treatment, control, assumptions, threats, estimation approach, pseudo-code, robustness plan, and falsification tests
2. **`strategy_audit.md`** — the Phase 2 audit report with findings classified by severity (kept separate — it's a different voice)

The robustness plan in `strategy_memo.md` is a design artifact. Use `/robustness-battery` to execute these checks after the main specification is coded.

## Three-Strikes Escalation

If the audit finds CRITICAL issues and the strategy is revised and re-audited **three times** without resolving them:

- Stop
- Produce a focused escalation report listing only the persistent critical issues
- Flag: "These identification concerns have persisted through 3 revision cycles. The research question may need reformulation, the data may not support causal identification, or a different design is needed. Author review required."

## Principles

- **Design before results.** This skill exists to prevent the most common AI-assisted research failure: jumping to code before the design is right.
- **Be honest about what the data can support.** If the design is fundamentally descriptive, say so — don't pretend it's causal.
- **The audit is not optional.** Every strategy gets audited before anyone writes code.
- **Respect the researcher's judgment.** Flag concerns and present tradeoffs — don't dictate the design.
