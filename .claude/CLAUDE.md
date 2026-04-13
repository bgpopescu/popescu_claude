# CLAUDE.md

## Core Principles

- **Plan first** — enter plan mode before non-trivial tasks
- **Verify after** — run code, render output, confirm results before moving on

---

## Estimation Philosophy

- Do NOT express concern or excitement about point estimates
- Do NOT interpret results as "good" or "bad" until the design is intentional
- Focus entirely on whether the specification is correct — results are meaningless until the design is right
- `/methods-review` NEVER modifies author code — it only reads, runs, and creates replication scripts

---

## Tools and Software

| Tool | Use | Notes |
|------|-----|-------|
| R | Primary analysis, visualization (ggplot2, fixest, sf, dplyr, tidyr) | Always use |
| Python | Cross-language replication (statsmodels, linearmodels, pandas) | For verification |
| Quarto | Slide decks (RevealJS), documents, website, tutorials (.qmd) | NOT Beamer |
| LaTeX | Paper drafts when needed | |

When suggesting code, include comments explaining non-obvious choices.

---

## R Code Style

- Write linear, top-to-bottom scripts — a reader should follow the logic by reading downward
- Use direct column access (`df$var`) over tidy evaluation when it's clearer
- Section headers with `#####` comment blocks, terse step labels (`#Step 3: Run regression`)
- Only create helper functions when the same logic is genuinely reused — not for one-off operations
- Keep comments short — say where you are, not what standard functions do
- No conditional execution paths (`if (!exists(...))`) — scripts should assume they run top to bottom in order
- No lengthy output manifests in script headers — a one-line purpose comment is enough
- Use `setwd()` at the top then relative paths throughout — not `base <- "..." + file.path(base, ...)` everywhere
- When generating ggplot figures, also write the underlying plot data to a CSV in `data/intermediate/` (e.g., `figure_X_data.csv`). This allows accurate interpretation of figures without relying on image parsing.

---

## Folder Structure

```
project/
├── code/R/          — R scripts (01_clean.R, 02_analysis.R, ...)
├── code/python/     — Replication scripts
├── data/raw/        — Original data (NEVER modify)
├── data/clean/      — Cleaned datasets
├── output/tables/   — Generated tables
├── output/figures/  — Generated figures
```

- Use relative paths in all code
- Quarto theme: use _extensions/meridian.scss when available
- ggplot2: use theme_bw() when defined in the project (exception: presentation-builder uses theme_meridian())

---

## Quarto Conventions

- Slide decks use RevealJS format with self-contained: true
- Section headers: # Title {background-color="#1e293b"}
- Exercises: {background-color="#b44527"}
- Use :::fragment for progressive disclosure
- Mermaid diagrams: always include %%{init:{...}}%% block with useMaxWidth: true
- Code chunks: echo: false, warning: false, message: false by default
- Maximum 6 bullets per slide, 12 words per bullet
- Slide titles are assertions, not labels ("Trade increased GDP by 12%" not "Results")
- Cognitive load should be roughly equal across all slides
- Lead with the conclusion, then support it (Pyramid Principle)
