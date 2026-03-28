---
name: revise-and-resubmit
description: Manage the R&R cycle — classify referee comments, route to appropriate actions, draft point-by-point response letter, and track every change. Handles diplomatic disagreement.
argument-hint: "[referee-report-path] [paper-path (optional)]"
allowed-tools: Read, Grep, Glob, Write, Edit, Bash
---

# Revise and Resubmit: Structured R&R Management

Take a referee report and produce a classified action plan, a point-by-point response letter, and a revision tracker. Handle disagreements diplomatically.

## Input

`$ARGUMENTS` — path to referee report file(s), optionally followed by paper path. If paper path not given, look for `Paper/main.tex` or the most recently modified .tex/.qmd in the project root.

## Step 1: Parse Inputs

1. Read all referee report(s)
2. Read the paper
3. Read any existing revision tracker (if this is a second round)
4. Identify the journal and round from context or ask the user

## Step 2: Classify Every Comment

For each distinct point raised by each referee, assign a class:

| Class | What It Means | Action |
|-------|--------------|--------|
| **NEW ANALYSIS** | Referee wants a test, table, or figure that doesn't exist | Flag for user approval. Create analysis task with specific instructions. |
| **CLARIFICATION** | Referee misunderstood or wants more explanation | Draft rewritten section with clearer language. |
| **REWRITE** | Referee wants structural changes to presentation | Draft restructured section. |
| **DISAGREE** | You believe the referee is wrong or the request is unreasonable | Draft diplomatic pushback. **FLAG PROMINENTLY for user review.** |
| **MINOR** | Typo, formatting, citation, small wording change | Draft the fix directly. |

## Step 3: Build the Revision Tracker

Save to `quality_reports/revision_tracker_round{N}.md`:

```markdown
# Revision Tracker — Round {N}
Journal: [journal]
Date: [date]

## Summary
| Referee | Total Comments | New Analysis | Clarification | Rewrite | Disagree | Minor |
|---------|---------------|-------------|---------------|---------|----------|-------|
| Ref 1   | X             | X           | X             | X       | X        | X     |
| Ref 2   | X             | X           | X             | X       | X        | X     |

## Action Items by Priority

### HIGH — New Analysis (requires user approval)
- [ ] R1.3: Add placebo test using pre-treatment outcomes
- [ ] R2.7: Heterogeneity analysis by region

### MEDIUM — Clarification / Rewrite
- [ ] R1.1: Rewrite identification section to address endogeneity concern
- [ ] R2.2: Add paragraph on data limitations

### FLAGGED — Disagreements (requires user judgment)
- [ ] R1.5: Referee wants individual FE but this absorbs treatment variation
- [ ] R2.4: Referee questions sample restriction — we have good reason

### LOW — Minor
- [ ] R1.8: Fix citation formatting
- [ ] R2.9: Typo in Table 3 header
```

## Step 4: Draft Response Letter

Generate a structured response letter:

```latex
\documentclass[12pt]{article}
\usepackage{xcolor}
\definecolor{response}{RGB}{0,0,139}
\definecolor{quote}{RGB}{100,100,100}

\begin{document}

\title{Response to Referees — Round {N}}
\author{[Author]}
\date{[Date]}
\maketitle

\section*{Summary of Major Changes}
[2-3 paragraphs describing the main revisions]

\section*{Referee 1}

\subsection*{Comment 1.1}
\textcolor{quote}{\textit{``[Exact quote from referee]''}}

\textcolor{response}{[Response — what we did and why. Reference specific page/section/table numbers in the revised manuscript.]}

...
\end{document}
```

**Response formatting rules:**
- Always quote the referee's exact words in italics
- Responses in a distinct color
- Reference specific page/section/table numbers for every change
- For NEW ANALYSIS items not yet done: mark as "[TBD — pending analysis]"
- For DISAGREE items: use the diplomatic protocol (Step 5)

## Step 5: Diplomatic Disagreement Protocol

When you disagree with a referee, NEVER say "the referee is wrong." Instead:

1. **Acknowledge**: "We appreciate the referee's careful attention to [topic]. This is an important point that deserves thorough discussion."
2. **Present evidence**: "Our [test/analysis/reasoning] suggests that [your position], because [specific evidence]."
3. **Offer a partial concession**: "To address this concern, we have [added a robustness check / expanded the discussion / included an additional specification]."
4. **Remain open**: "We are happy to discuss this further if the referee has additional concerns."

**FLAG ALL DISAGREE ITEMS for user review.** Draft the response but mark it clearly:

```
⚠️ DISAGREEMENT — REQUIRES YOUR REVIEW ⚠️
Referee says: [quote]
My draft response: [diplomatic pushback]
Your options: (1) Use this response (2) Concede and make the change (3) Rewrite
```

## Step 6: Save Outputs

1. **Tracker**: `quality_reports/revision_tracker_round{N}.md`
2. **Response letter**: `quality_reports/referee_response_round{N}.tex`
3. **Revised sections**: Only for CLARIFICATION and REWRITE items — save drafts to `quality_reports/revised_sections/` for user review. Do NOT overwrite the actual paper sections without user approval.

## Principles

- **The response letter is the user's voice.** Match their academic tone — formal but not stiff.
- **Never fabricate results.** NEW ANALYSIS items are marked TBD until the analysis exists.
- **Flag all disagreements.** These require human judgment. Period.
- **Track everything.** Every referee comment appears in both the tracker and the response letter. Nothing gets lost.
- **Revisions are iterative.** If this is Round 2+, reference what changed since the previous round.
- **Quote precisely.** Misquoting a referee is worse than ignoring them.
