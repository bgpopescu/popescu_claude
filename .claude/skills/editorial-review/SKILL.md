---
name: editorial-review
description: Comprehensive manuscript review — proofread, internal consistency, methods audit, scope conditions, and simulated peer review calibrated to a target journal. Includes author-defense filter to drop hallucinated criticisms.
argument-hint: "[paper-path] [--proofread|--consistency|--methods|--scope|--peer JOURNAL|--all]"
allowed-tools: Read, Grep, Glob, Write, Bash, Edit
---

# Editorial Review: Multi-Dimensional Manuscript Critique

Unified review command that evaluates a manuscript from multiple angles. Routes to the appropriate review mode based on flags.

## Input

`$ARGUMENTS` — path to the paper (LaTeX or Quarto) and optionally a flag:
- `--proofread` — Writing quality only
- `--consistency` — Internal consistency audit (numbers, figures, citations)
- `--methods` — Causal design and identification audit
- `--scope` — Scope conditions and generalizability
- `--peer JOURNAL` — Simulated blind review calibrated to a journal
- `--all` — Everything (default if no flag given)

## Mode 1: Proofread (`--proofread`)

Review the manuscript for writing quality across 6 categories:

1. **Structure**: Does the paper follow a logical arc? Introduction → Literature → Data → Method → Results → Discussion → Conclusion? Are transitions smooth?
2. **Claims-Evidence Alignment**: Does every empirical claim have a corresponding table, figure, or citation? Are claims appropriately hedged?
3. **Identification Fidelity**: Does the prose description of the method match what the code actually does? (Cross-reference if code is available.)
4. **Writing Clarity**: Sentence length, jargon density, passive voice overuse, paragraph coherence
5. **Grammar and Style**: Typos, agreement errors, inconsistent formatting, reference formatting
6. **Compilation**: Does the document compile without errors? Missing references? Broken cross-references?

Score each category 0–100. Aggregate with equal weights.

## Mode 2: Internal Consistency Audit (`--consistency`)

Line-item reconciliation of every number, figure, and table against the text.

**Part 1 — Arithmetic consistency:** For every sentence that reports an effect size relative to a mean or baseline, compute the implied treated and control values and check whether they are arithmetically plausible (no negative shares, no proportions outside [0,1], no values exceeding logical bounds). Flag any sentence where the implied values are impossible.

**Part 2 — Abstract-to-results traceability:** Take every quantitative claim in the abstract and introduction and trace it to the specific table or figure it comes from. Check that the number matches exactly. Flag rounding discrepancies and any claim that cannot be traced.

**Part 3 — Figure-text reconciliation:** For every figure and table, state what quantity is plotted or reported. Find the corresponding prose description and check whether it correctly describes that quantity. Pay special attention to:
- Whether interaction coefficients are interpreted as interaction effects or misread as main effects
- Whether panels within the same figure plot different estimands that the text treats as comparable
- Whether the same variable or coefficient is described differently across sections

**Part 4 — Conceptual term drift:** Identify the paper's 5–6 most important conceptual terms. Track every definition and usage across sections. Flag any place where a term shifts meaning, is used more loosely than its formal definition, or is applied to a context where the definition doesn't cleanly apply.

**Part 5 — Citation accuracy:** For every citation supporting a specific empirical or theoretical claim, evaluate whether the cited work plausibly supports that specific claim. Flag cases where:
- The claim attributed to a source is stronger than what that source establishes
- A citation is used to support a claim that is contested or superseded in the field
- Multiple citations are stacked in a way that obscures that none individually supports the claim

Also flag missing literature that a reviewer in the field would immediately notice — particularly work that challenges the mechanism, provides alternative explanations, or establishes known limitations of the empirical strategy.

For each finding, assign a confidence score (0–100%) that it is a genuine problem rather than a hallucination. Quote the specific passage.

## Mode 3: Methods Audit (`--methods`)

Evaluate the empirical strategy across 5 weighted dimensions:

| Dimension | Weight | What to Check |
|-----------|--------|---------------|
| **Identification** | 35% | Source of exogenous variation. Assumptions stated? Credible? |
| **Estimation** | 25% | Does the estimating equation match the design? Correct implementation? |
| **Inference** | 20% | SEs clustered at right level? >50 clusters? Robust to alternatives? |
| **Robustness** | 15% | Alternative specs? Placebo tests? Sensitivity analysis? |
| **Replication** | 5% | Can someone reproduce from the package? |

**Identification assumption stress test:** For each identifying assumption relevant to the design (exclusion restriction, parallel trends, monotonicity, no-interference, SUTVA, etc.):
1. Is it stated explicitly in the paper?
2. Is it testable? If so, does the paper test it?
3. What empirical pattern would falsify it?
4. Does the paper offer a defense? Is that defense logically sufficient, or does it merely restate the assumption?

**Mandatory sanity checks before scoring:**
- Effect direction: Does it make theoretical sense?
- Magnitude: Is the implied effect plausible?
- Pre-treatment coefficients near zero (if applicable)
- Result stability across specifications

**Domain expertise**: DiD (Callaway-Sant'Anna, Sun-Abraham, de Chaisemartin-D'Haultfoeuille), IV, RDD, Synthetic Control, Event Studies.

Score each dimension 0–100, compute weighted aggregate. Assign confidence scores to each finding.

## Mode 4: Scope Conditions (`--scope`)

**Part 1 — Stated scope conditions:** List every explicit scope condition. For each, evaluate whether the empirical tests respect it, or whether claims implicitly exceed it.

**Part 2 — Unstated scope conditions:** Identify cases, contexts, or parameter ranges where the central mechanism would not operate or would operate in the opposite direction — and that the paper does not acknowledge. For each: describe the case, explain why the mechanism breaks down, assess how much of the generalization claim it undermines.

**Part 3 — External validity:** Identify what selection process governs which units enter the sample, whether that selection is correlated with the outcome or mechanism, and what the implied population of inference is. Flag any general claim the sample cannot support.

**Part 4 — Theory-empirics scope mismatch:** Identify every place where the theory section makes a claim broader than what the empirical setting can test, and every place where empirical results are generalized beyond what the theory predicts.

Assign confidence scores. Classify each finding as: overgeneralization | missing scope condition | sample selection problem | theory-empirics mismatch.

## Mode 5: Simulated Peer Review (`--peer JOURNAL`)

Simulate a blind review as if submitted to the specified journal.

**How to calibrate:** Claude does not have reliable built-in knowledge of specific journals' standards. For a useful simulated review, the user should provide:

1. **The journal name** — gives Claude a rough sense of field and scope
2. **2–3 recent articles from the journal** — read these with `/split-pdf` first, then tell Claude: "Use these as reference points for the kind of contribution, methods, and writing style this journal publishes."
3. **The journal's submission guidelines** — paste or link to the author guidelines page (scope, formatting, word limits, review criteria if published)

The more concrete material Claude has, the better the review. Without reference articles or guidelines, the review defaults to a generic top-field standard, which is less useful.

**Review structure:**
1. One-paragraph summary of the paper
2. Main contribution assessment (is this new and important for this journal's audience?)
3. Major concerns (numbered, with specific suggestions)
4. Minor concerns (numbered)
5. Questions for the authors
6. Preliminary assessment: Accept / Minor Revisions / Major Revisions / Reject

**Two independent referee reports**, then an editorial synthesis:
- Referee A focuses on substance and contribution
- Referee B focuses on methods and identification
- Editor weighs both and gives an assessment

::: {.callout-important}
**These are structured drafts, not verdicts.** The "Accept / Minor / Major / Reject" categories are vocabulary for organizing your own thinking, not authoritative judgments. Claude's simulated review is a starting point — it catches issues you might miss, but it also misses issues a real referee would catch, and it sometimes hallucinates problems that don't exist. The author-defense filter (Mode 6) helps, but human judgment is the final gate.
:::

## Mode 6: Full Review (`--all`)

Run all five modes in sequence, then apply the **Author Defense Filter** (see below). Produce a combined report with:
1. Proofread scores (6 categories)
2. Internal consistency findings
3. Methods audit scores (5 dimensions)
4. Scope condition findings
5. Simulated peer review (2 referee reports + editorial synthesis)
6. Author defense results — which criticisms survived, which were dropped
7. Consolidated action items, ranked by severity (surviving criticisms only)

## Author Defense Filter

**Applied automatically at the end of `--all` mode. Can also be run manually after any other mode.**

After generating all criticisms, switch perspective to the paper's author. For each criticism:

1. State the strongest possible defense, drawing on the paper's actual content and design choices
2. Assess whether the defense fully resolves the issue, partially resolves it, or fails entirely
3. If partially resolved or failed: identify precisely what residual problem remains
4. Assign a confidence score (0–100%) that the criticism survives the defense

**Drop** any criticism that:
- Is factually wrong (hallucinated — the paper actually addresses the point)
- Scores below 30% confidence after defense
- Is purely stylistic with no substantive implication

The final report should only contain criticisms that survived this filter. Mark each with its post-defense confidence score.

## Output

Save reports to `quality_reports/`:
- `--proofread` → `quality_reports/proofread_[date].md`
- `--consistency` → `quality_reports/consistency_review_[date].md`
- `--methods` → `quality_reports/methods_review_[date].md`
- `--scope` → `quality_reports/scope_review_[date].md`
- `--peer` → `quality_reports/peer_review_[journal]_[date].md`
- `--all` → `quality_reports/full_review_[date].md`

## Scoring Gates

| Mode | Blocking? | Threshold |
|------|-----------|-----------|
| Proofread | Yes | 80/100 to proceed |
| Methods | Yes | 80/100 to proceed |
| Peer Review | Yes | "Minor Revisions" or better |

If below threshold, the report should state clearly what must be fixed before resubmission.

## Three-Strikes Escalation

When re-running a review after revisions:

1. Track the round number in the report filename (`full_review_round2_[date].md`)
2. Compare scores to the previous round's report — note which issues were resolved and which persist
3. If a scoring gate has been failed **three consecutive times**:
   - Stop the review
   - Produce a focused escalation report listing only the persistent failures
   - Flag explicitly: "This issue has not been resolved after 3 revision cycles. Author review required before re-running."
   - Do NOT continue to subsequent modes

This prevents infinite revision loops. If three rounds of fixes haven't resolved a problem, the issue likely needs human judgment — a different approach, not another attempt at the same fix.

## Principles

- **Critics never edit.** All reviews produce reports only. No changes to the manuscript.
- **Confidence scores on everything.** Every finding gets a 0–100% score. This is how you triage.
- **The author defense filter is mandatory for `--all`.** Unfiltered criticism lists are noisy and waste the user's time.
- **Be constructive.** Every criticism comes with a specific suggestion for improvement.
- **Proportional severity.** A missing robustness check is not the same as a flawed identification strategy.
- **Separate what you know from what you suspect.** If you can't verify a claim without running code, say so.
- **The author decides.** Flag concerns; don't demand specific fixes.
