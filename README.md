# popescu_claude

A Claude Code skills library for empirical social science research — from finding literature to submitting to journals.

## What's here

### `.claude/skills/` — Custom skills for Claude Code

Each folder contains a `SKILL.md` file. They are already in the correct location (`.claude/skills/`) for Claude Code to detect them.

#### Discovery and Design

| Skill | Type | What it does |
|-------|------|-------------|
| **discover-lit** | Workflow | Systematic literature search with proximity scoring, BibTeX output, frontier map, and positioning statement |
| **strategize** | Workflow | Designs identification strategy before code — produces strategy memo, pseudo-code, robustness plan, falsification tests, then audits the design |
| **read-paper** | Guardrail | Reads academic PDFs in 4-page chunks to prevent hallucination from full-PDF reading |
| **search-pdf** | Convenience | Targeted search in large PDFs (books, archives) — builds a page index, searches first, reads second |

#### Analysis and Interpretation

| Skill | Type | What it does |
|-------|------|-------------|
| **robustness-battery** | Workflow | Systematic robustness checks across 7 dimensions (samples, controls, FE, SEs, estimators, functional forms, placebos) |
| **regression-interpret** | Guardrail | Prevents causal language slips, log-spec misreading, and significance overstatement when interpreting results |

#### Review and Revision

| Skill | Type | What it does |
|-------|------|-------------|
| **methods-review** | Workflow | Adversarial 5-audit project review: code, cross-language replication, directory structure, output automation, methods. Three-strikes escalation. |
| **argument-review** | Workflow | Adversarial intellectual review of a paper draft: argument gaps, internal consistency, methods-theory alignment, assumptions, notation, simulated referee report. |
| **humanizer** | Guardrail | Removes 11 patterns of AI-generated writing from academic text, based on Wikipedia's "Signs of AI writing" guide |

#### Presentation and Submission

| Skill | Type | What it does |
|-------|------|-------------|
| **presentation-builder** | Workflow | Builds Quarto RevealJS decks from papers or lecture topics with `theme_meridian`, Mermaid auto-fit, multi-agent review, and decktape visual proof. Supports conference, seminar, job talk, and lecture modes. |
| **submit-package** | Workflow | Builds a journal-ready replication package: AEA-format README, master script, dependency documentation, data provenance, and 10-point verification checklist |

**Skill types:** Guardrail skills prevent common errors. Workflow skills enforce multi-step processes. Convenience skills save time on repetitive tasks.

### `.claude/CLAUDE.md` — Project configuration

Tells Claude who you are, how you work, and what conventions to follow. Covers estimation philosophy, tools, folder structure, and Quarto conventions. Customize this for your own profile.

## How to use

> **Seeing the `.claude/` folder:** It starts with a dot, so it's hidden by default. Use `ls -a` in the terminal, or press `Cmd+Shift+.` in macOS Finder to toggle hidden files. If you create `claude/` without the dot, Claude Code won't find it.

1. Copy the `.claude/` folder into your project root (it contains skills and CLAUDE.md)
2. Edit `.claude/CLAUDE.md` to match your profile
3. Use Claude Code as usual — skills trigger automatically when relevant, or invoke manually with `/skill-name`

## Suggested workflow

```
/discover-lit            ← find and organize literature
/read-paper              ← read papers with structured extraction
/strategize              ← design identification strategy before coding
  ... write code ...
/robustness-battery      ← test specification sensitivity
/regression-interpret    ← interpret results with proper hedging
/methods-review          ← adversarial code & methods audit
/argument-review     ← adversarial paper & argument audit
/presentation-builder    ← build conference or lecture deck
/submit-package          ← build replication package
  ... submit ...
```

This is not strictly linear — robustness results may send you back to `/strategize`, referee feedback may require `/discover-lit` again.

## Context

Developed for a tutorial series on agentic AI for social science research at Tec de Monterrey. The skills synthesize ideas from Scott Cunningham's MixtapeTools and Hugo Sant'Anna's Clo-Author, adapted for a political science and economic history workflow using R, Quarto, and ggplot2.
