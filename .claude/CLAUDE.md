# CLAUDE.md — popescu_claude skills library

## What this repo is

A Claude Code skills library for empirical social science research, used in a tutorial series at Tec de Monterrey. Contains reusable skills in `.claude/skills/` — no data or analysis code.

## Core Principles

- **Plan first** — enter plan mode before non-trivial tasks
- **Verify after** — run code, render output, confirm results before moving on

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

## Quarto Conventions

- Slide decks use RevealJS format with self-contained: true
- Section headers: # Title {background-color="#1e293b"}
- Use :::fragment for progressive disclosure
- Mermaid diagrams: always include %%{init:{...}}%% block with useMaxWidth: true
- Code chunks: echo: false, warning: false, message: false by default
- Maximum 6 bullets per slide, 10 words per bullet
- Slide titles are assertions, not labels
- Lead with the conclusion, then support it (Pyramid Principle)
