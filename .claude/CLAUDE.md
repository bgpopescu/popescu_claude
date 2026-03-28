# CLAUDE.md — Bogdan G. Popescu

**Institution:** Tec de Monterrey
**Branch:** main

---

## Core Principles

- **Plan first** — enter plan mode before non-trivial tasks
- **Verify after** — run code, render output, confirm results before moving on
- **Design before results** — focus on specification correctness, not point estimates
- **Cross-replicate** — implement key analyses in R (primary) and Python (replication)
- **[LEARN] tags** — when corrected, note `[LEARN:category] wrong → right` in this file

---

## Estimation Philosophy

**Design before results.** During estimation and analysis:

- Do NOT express concern or excitement about point estimates
- Do NOT interpret results as "good" or "bad" until the design is intentional
- Focus entirely on whether the specification is correct
- Results are meaningless until we're confident the design is right
- Objectivity means being attached to getting the design right, not to any particular finding

---

## Communication Guidelines

- Refer to me as Bogdan in informal contexts, "the author" in formal outputs
- Be concise (whenever possible) and clear — I prefer concise and clear content over verbose explanations
- Do not hedge or pad responses with unnecessary caveats
- When suggesting code, include comments explaining non-obvious choices

---

## Tools and Software

| Tool | Use | Notes |
|------|-----|-------|
| R | Primary analysis, visualization (ggplot2, fixest, sf, dplyr, tidyr) | Always use |
| Python | Cross-language replication (statsmodels, linearmodels, pandas) | For verification |
| Quarto | Slide decks (RevealJS), documents, website, tutorials (.qmd) | NOT Beamer |
| LaTeX | Paper drafts when needed | |
| Git + GitHub | Version control | Commit frequently with meaningful messages |

---

## Folder Structure and File Conventions

project/
├── code/R/          — R scripts (01_clean.R, 02_analysis.R, ...)
├── code/python/     — Replication scripts
├── data/raw/        — Original data (NEVER modify)
├── data/clean/      — Cleaned datasets
├── output/tables/   — Generated tables
├── output/figures/  — Generated figures

- Projects live in ~/Library/CloudStorage/Dropbox/ (e.g., Dropbox/project_gulag2/)
- Use relative paths in all code — absolute paths break reproducibility
- Quarto theme: use _extensions/meridian.scss when available
- ggplot2: use theme_bw() when defined in the project

---

## Session Startup

At the start of every session:
1. Read README.md and this CLAUDE.md before doing anything else
2. State the session goal and confirm alignment before writing code
3. Note any problems encountered — append to MEMORY.md if it exists

---

## Quarto Conventions

- Slide decks use RevealJS format with self-contained: true
- Section headers: # Title {background-color="#1e293b"}
- Exercises: {background-color="#b44527"}
- Use :::fragment for progressive disclosure
- Mermaid diagrams: always include %%{init:{...}}%% block with useMaxWidth: true
- Code chunks: echo: false, warning: false, message: false by default
- Maximum 6 bullets per slide, 10 words per bullet
- Slide titles are assertions, not labels ("Trade increased GDP by 12%" not "Results")
- Cognitive load should be roughly equal across all slides (MB/MC equivalence)
- Lead with the conclusion, then support it (Pyramid Principle)

---

## Project Overview and Status

See README.md for the current research question, method, data, and status.

---

## Quality Thresholds

| Score | Gate | Meaning |
|-------|------|---------|
| 80 | Commit | Good enough to save |
| 90 | Review | Ready for coauthor/self-review |
| 95 | Submit | Ready for journal or classroom |

---

## Referee 2 Correspondence

Status: [Not yet audited / In progress / Accepted]
Reports at: quality_reports/referee2/

Critical Rule: Referee 2 NEVER modifies author code. It only reads, runs,
and creates replication scripts in code/replication/.
