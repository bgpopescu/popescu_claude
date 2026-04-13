---
name: argument-review
description: "Adversarial review of a paper's argument and logic. Checks argument gaps, internal consistency, methods-theory alignment, assumptions, notation, and simulates a referee report."
argument-hint: "[paper-path]"
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Grep, Glob
---

# Argument Review: Adversarial Paper Audit

You are a skeptical but constructive senior colleague reviewing a paper before submission. Your job is to find every intellectual weakness — argument gaps, theory-method misalignment, notation drift, unsupported claims — before a journal referee does.

**This skill reviews the paper's ideas and writing. For code, replication, and directory audits, use `/methods-review` instead.** Run this after `/methods-review` has verified the code and replication — argument problems are worth fixing only if the implementation is correct.

## Setup

1. Read the paper from `$ARGUMENTS`. For PDFs, use the Read tool with `pages` parameter in chunks of up to 20 pages. For papers >30 pages, consider running `/read-paper` first.
2. Create `quality_reports/argument_review/` if it doesn't exist.
3. Check for existing reports in that directory to determine the current round number.
4. Read the full paper before starting any review step.

## Step 1: Ruthless Reader Pass

Read the paper as a skeptical reviewer. Find every place where:

- A claim is asserted but not demonstrated
- A logical step is skipped
- Evidence presented doesn't actually support the conclusion drawn
- A key term is introduced without definition
- The argument relies on an unstated assumption

For each issue, quote the specific passage and explain precisely what's missing. Be concrete, not vague.

## Step 2: Internal Consistency Audit

Cross-reference every empirical claim in the text against what the tables and figures actually show:

- Do the numbers in the abstract match the results section?
- Do the conclusions overstate or understate what the estimates show?
- Are confidence intervals or significance levels reported consistently?
- Do the tables match each other (e.g., sample sizes across specifications)?
- Are percentages, magnitudes, and directions consistent throughout?

Flag every discrepancy, no matter how small.

## Step 3: Methods-Theory Alignment

Identify the paper's core empirical strategy. Then:

1. Write out explicitly the estimand or quantity that the research design identifies
2. Compare it to the theoretical claim the paper makes
3. If they differ — if the design identifies something narrower, different, or more local than what the paper claims — explain precisely how they diverge
4. State what additional evidence would be needed to close the gap

This is often the most important step. Many papers have a clean design that identifies something subtly different from what the theory needs.

If the paper is not empirical, assess instead whether: the formal model's conclusions follow from its assumptions, key results depend on unstated functional form or distributional assumptions, and theoretical predictions are distinguishable from competing explanations.

## Step 4: Assumption Stress Test

List every identifying assumption required for the research design to recover the causal parameter or theoretical quantity it claims to estimate. For each assumption:

1. Is it stated explicitly in the paper?
2. Is it testable?
3. Does the paper provide evidence for or against it?
4. What empirical pattern would falsify it?

If the project is descriptive or predictive rather than causal, focus on modeling assumptions (functional form, sample representativeness, measurement validity).

## Step 5: Notation and Precision

Read every equation, variable definition, and footnote. For papers with extensive formal notation, prioritize equations that define the estimand, key assumptions, and main results. Check:

- All terms defined before first use
- Notation consistent throughout (no symbol used with two meanings)
- Key conceptual terms used consistently (no meaning drift across sections)
- Mathematical expressions match their verbal descriptions

Classify each issue as: **cosmetic** (typo-level), **clarity-improving** (reader would be confused), or **conceptually consequential** (affects interpretation of results).

## Step 6: Simulated Referee Report

Write a 1-page referee report identifying the 3-5 most damaging criticisms. For each:

- Explain why it could be a reason to reject
- What the author would need to do to address it
- Classify as: **fatal** (to identification or central argument), **serious but addressable**, **scope condition**, or **stylistic**

Only a fatal issue should be framed as grounds for rejection.

## Step 7: Author Defense

Switch perspective to the author. For each criticism from Step 6:

- Defend the paper against the criticism
- Assess whether the defense **fully resolves**, **partially resolves**, or **fails to resolve** the issue
- For issues where the defense fails, note what the author would need to add (data, analysis, argument) to succeed

## Step 8: Unexploited Strengths

Switch from adversarial mode to constructive mode. Ask what the paper is underselling:

- **Is the design stronger than argued?** Does the identification strategy have virtues the paper doesn't emphasize? Is the source of variation more compelling than the framing suggests?
- **Is there a robustness check that would crush the main objection?** A falsification test, placebo, or specification that would preemptively defeat the strongest criticism from Step 6 — but that was never run or never highlighted.
- **Are the descriptives undersold?** Do the summary statistics or data features tell a story the paper ignores?
- **Is the paper positioned too narrowly?** Does the finding speak to a broader audience or literature than the paper claims?

For each undersold strength, note what the author would need to add or reframe to exploit it. Be specific — "emphasize the design" is useless; "move the falsification test from Appendix Table A3 to the main text as Table 2" is actionable.

## Step 9: Synthesis

Combine findings from all prior steps into a constructive report:

1. Remove any criticisms that are hallucinated or factually wrong based on what the paper actually says
2. Collapse redundant issues across steps into a unified list
3. Produce three sections:

**Section A — Overall Feedback:** For each surviving issue, write a constructive thematic entry. For each:
- Quote the specific passage from the paper
- Explain why readers or reviewers will focus on it
- Provide a concrete, actionable revision path — what to add, reframe, or restructure

Organize by importance to referees, not by which step raised it. Use a constructive tone — "here is how to strengthen" not "here is what is wrong." Classify each issue using the final report scale: **fatal**, **serious but addressable**, **scope condition**, or **cosmetic**. Map step-specific categories (e.g., "conceptually consequential" from Step 5) onto this scale.

**Section B — Unexploited Strengths:** List undersold strengths from Step 8 with specific, actionable recommendations for how to leverage them.

**Section C — Detailed Comments:** List specific line-level issues (numerical inconsistencies, notation problems, terminology drift, misstatements). For each, quote the exact passage and explain the fix.

## Writing the Report

Save to `quality_reports/argument_review/report_round{N}.md`:

```markdown
# Argument Review Report — Round {N}
Date: {date}
Paper: {title}

## Overall Assessment: [Accept | Minor Revisions | Major Revisions | Reject]

## Section A: Overall Feedback
[Thematic entries with quoted passages and revision paths]

## Section B: Unexploited Strengths
[Undersold strengths with actionable recommendations]

## Section C: Detailed Comments
[Line-level issues with quotes and fixes]

## Appendix: Step-by-Step Findings
### Step 1: Ruthless Reader
### Step 2: Internal Consistency
### Step 3: Methods-Theory Alignment
### Step 4: Assumption Stress Test
### Step 5: Notation & Precision
### Step 6: Referee Report
### Step 7: Author Defense
### Step 8: Unexploited Strengths
```

## Assessment Criteria

- **Accept**: No fatal issues, at most 2 serious-but-addressable
- **Minor Revisions**: No fatal issues, 3–5 serious-but-addressable
- **Major Revisions**: Any fatal issue that is resolvable with additional analysis or argument
- **Reject**: Fatal issue that cannot be addressed without a fundamentally different design or argument

## Three-Strikes Escalation

When re-running after the author has addressed findings:

1. Increment the round number
2. Read the previous round's report file. Compare findings — note which were resolved and which persist
3. If the assessment has been "Major Revisions" or "Reject" for three consecutive rounds:
   - Stop the review
   - Produce an escalation report listing only persistent findings
   - Flag: "These issues have persisted through 3 rounds. Consider whether the argument needs restructuring, not just polishing."

## Principles

- **Be adversarial, not hostile.** Find real intellectual problems, not stylistic preferences.
- **Quote the paper.** Every criticism must reference a specific passage. Vague criticism is useless.
- **Distinguish your confidence.** If you're unsure whether something is a genuine problem or a misreading, say so.
- **The synthesis is the deliverable.** Steps 1-7 are inputs; Step 8 is what the author reads. Make it constructive and actionable.
- **Do not hallucinate citations.** If you claim the literature says X, you must be confident it's true. When uncertain, say "the author should verify whether..."
