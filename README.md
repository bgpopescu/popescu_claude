# popescu_claude

A Claude Code skills library for empirical social science research — from finding literature to submitting to journals.

## What's here

### `.claude/skills/` — Custom skills for Claude Code

Each folder contains a `SKILL.md` file. They are already in the correct location (`.claude/skills/`) for Claude Code to detect them.

#### Discovery and Design

| Skill | Type | What it does |
|-------|------|-------------|
| **discover-lit** | Workflow | Systematic literature search with proximity scoring, BibTeX output, frontier map, and positioning statement |
| **domain-profile** | Setup | Creates a field calibration file (journals, data sources, methods, conventions) used by other skills |
| **strategize** | Workflow | Designs identification strategy before code — produces strategy memo, pseudo-code, robustness plan, falsification tests, then audits the design |
| **split-pdf** | Guardrail | Reads academic PDFs in 4-page chunks to prevent hallucination from full-PDF reading |
| **search-book** | Convenience | Targeted search in large PDFs (books, archives) — builds a page index, searches first, reads second |

#### Analysis and Interpretation

| Skill | Type | What it does |
|-------|------|-------------|
| **robustness-battery** | Workflow | Systematic robustness checks across 7 dimensions (samples, controls, FE, SEs, estimators, functional forms, placebos) |
| **regression-interpret** | Guardrail | Prevents causal language slips, log-spec misreading, and significance overstatement when interpreting results |

#### Review and Revision

| Skill | Type | What it does |
|-------|------|-------------|
| **editorial-review** | Workflow | Multi-pass paper review with 6 modes (proofread, consistency, methods, scope, peer, all), confidence scoring, and author-defense filter. Three-strikes escalation. |
| **referee2** | Workflow | Adversarial 5-audit project review in a fresh terminal: code, cross-language replication, directory structure, output automation, econometrics. Three-strikes escalation. |
| **revise-and-resubmit** | Workflow | Classifies referee comments into 5 categories (new analysis, clarification, rewrite, disagree, minor), generates revision tracker and LaTeX response letter |
| **humanizer** | Guardrail | Removes 25 patterns of AI-generated writing based on Wikipedia's "Signs of AI writing" guide |

#### Presentation and Submission

| Skill | Type | What it does |
|-------|------|-------------|
| **presentation-builder** | Workflow | Builds Quarto RevealJS decks from papers or lecture topics with `theme_meridian`, Mermaid auto-fit, multi-agent review, and decktape visual proof. Supports conference, seminar, job talk, and lecture modes. |
| **submit-package** | Workflow | Builds a journal-ready replication package: AEA-format README, master script, dependency documentation, data provenance, and 10-point verification checklist |

**Skill types:** Guardrail skills prevent common errors. Workflow skills enforce multi-step processes. Convenience skills save time on repetitive tasks. Setup skills configure the environment.

### `.claude/CLAUDE.md` — Project configuration

Tells Claude who you are, how you work, and what conventions to follow. Covers estimation philosophy, tools, folder structure, Quarto conventions, quality thresholds, and session startup routine. Customize this for your own profile.

### `.claude/MEMORY.md` — Lessons learned across sessions

A running log of corrections and patterns discovered while working. Claude reads this at session startup (via the Session Startup section in CLAUDE.md) so mistakes don't repeat. Entries use the format `[LEARN:category] wrong → right`. This file is not auto-generated — you tell Claude to add entries when it makes a mistake.

## How to use

> **Seeing the `.claude/` folder:** It starts with a dot, so it's hidden by default. Use `ls -a` in the terminal, or press `Cmd+Shift+.` in macOS Finder to toggle hidden files. If you create `claude/` without the dot, Claude Code won't find it.

1. Copy the `.claude/` folder into your project root (it contains skills, CLAUDE.md, and MEMORY.md)
2. Edit `.claude/CLAUDE.md` to match your profile
3. Edit or clear `.claude/MEMORY.md` (start fresh or keep the examples)
4. Optionally run `/domain-profile` to create a field calibration file
5. Use Claude Code as usual — skills trigger automatically when relevant, or invoke manually with `/skill-name`

## Suggested workflow

```
/domain-profile          ← once per field
/discover-lit            ← find and organize literature
/split-pdf               ← read papers with structured extraction
/strategize              ← design identification strategy before coding
  ... write code ...
/robustness-battery      ← test specification sensitivity
/regression-interpret    ← interpret results with proper hedging
/referee2                ← adversarial audit (fresh terminal)
/editorial-review --all  ← paper-level review with author defense
/presentation-builder    ← build conference or lecture deck
/submit-package          ← build replication package
  ... submit ...
/revise-and-resubmit     ← handle R&R when it comes back
```

## Context

Developed for a tutorial series on agentic AI for social science research at Tec de Monterrey. The skills synthesize ideas from Scott Cunningham's MixtapeTools and Hugo Sant'Anna's Clo-Author, adapted for a political science and economic history workflow using R, Quarto, and ggplot2.
