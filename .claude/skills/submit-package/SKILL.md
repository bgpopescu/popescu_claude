---
name: submit-package
description: "Build a journal-ready replication package and verify reproducibility. Produces AEA-format README, master script, dependency documentation, data provenance, and runs a 10-point verification checklist. Use before journal submission or when preparing a replication archive."
argument-hint: "[project-root-path]"
allowed-tools: Read, Write, Grep, Glob, Bash, Edit
---

# Submit-Package: Replication Package Builder

Build a complete, reproducible replication package and verify that it works. Produces documentation, a master script, and runs mechanical checks to ensure a stranger could reproduce your results.

**This skill does NOT evaluate research quality.** It checks that things compile, scripts run, files exist, and outputs match. Use `/editorial-review` or `/referee2` for quality assessment.

## Input

`$ARGUMENTS` — path to the project root directory. The skill reads the project structure, scripts, and outputs to build the package.

---

## Step 1: Inventory the Project

Scan the project and document:

- All analysis scripts (R, Python, Stata, Julia) — list with execution order
- All data files — raw and derived, with sizes
- All output files — tables, figures, derived datasets
- The manuscript file(s) — LaTeX, Quarto, or Word
- Configuration files — CLAUDE.md, README.md, .gitignore

## Step 2: Build the Master Script

Create a master script (`run_all.R`, `run_all.py`, `run_all.do`, or `run_all.sh`) that:

1. Sets the project root as working directory
2. Checks for required packages and reports any missing
3. Runs every analysis script **in the correct order**
4. Uses relative paths only
5. Reports execution time for each script
6. Produces a completion summary

```r
# Example structure (R)
# run_all.R — Master script for [Project Name]
# Runs all analysis scripts in order. Total time: ~X minutes.

cat("=== Starting replication ===\n")
cat("Working directory:", getwd(), "\n\n")

# Step 1: Data cleaning
cat("Step 1: Cleaning data...\n")
t1 <- system.time(source("code/R/01_clean.R"))
cat("  Done in", round(t1[3], 1), "seconds\n\n")

# Step 2: Main analysis
cat("Step 2: Running main analysis...\n")
t2 <- system.time(source("code/R/02_analysis.R"))
cat("  Done in", round(t2[3], 1), "seconds\n\n")

# ... continue for all scripts

cat("=== Replication complete ===\n")
```

## Step 3: Build the README

Create `README_replication.md` following AEA Data and Code Availability Standard:

```markdown
# Replication Package: [Paper Title]

## Authors
[Name(s), affiliation(s), email(s)]

## Overview
[1–2 sentence description of what the code produces]

## Data Availability

| Dataset | Source | Access | Notes |
|---------|--------|--------|-------|
| [name] | [provider] | [Public/Restricted/Application] | [URL or instructions] |

### Data Not Included
[List any data that cannot be distributed and explain how to obtain it]

## Computational Requirements

### Software
| Software | Version | Required Packages |
|----------|---------|-------------------|
| R | [version] | [package list with versions] |

### Hardware
- **Estimated runtime:** [X minutes/hours on a standard laptop]
- **Memory:** [estimated peak RAM usage]
- **Storage:** [total size of data + outputs]

## Instructions

1. Clone or download this repository
2. Install required packages: [specific instructions]
3. Place restricted data in `data/raw/` (if applicable)
4. Run `run_all.R` from the project root
5. Outputs will be generated in `output/tables/` and `output/figures/`

## File Structure

[Directory tree of the project]

## Script Descriptions

| Script | Purpose | Inputs | Outputs | Runtime |
|--------|---------|--------|---------|---------|
| `code/R/01_clean.R` | Clean raw data | `data/raw/*.csv` | `data/clean/analysis_sample.rds` | ~30s |
| `code/R/02_analysis.R` | Main specification | `data/clean/analysis_sample.rds` | `output/tables/table1.tex` | ~2min |

## Output-to-Paper Mapping

| Paper Element | Output File | Generating Script |
|--------------|-------------|-------------------|
| Table 1 | `output/tables/table1.tex` | `code/R/02_analysis.R` |
| Figure 1 | `output/figures/fig1.png` | `code/R/03_figures.R` |

## Notes
[Any additional information for replicators]
```

## Step 4: Document Dependencies

Create or verify dependency files:

| Language | File | How to Create |
|----------|------|--------------|
| R | `renv.lock` | `renv::snapshot()` |
| Python | `requirements.txt` | `pip freeze > requirements.txt` |
| Stata | `stata_packages.txt` | List all `ssc install` commands |

If lockfiles don't exist, create a package list by parsing all scripts for `library()`, `import`, `require()` statements. Note version numbers where available.

## Step 5: Document Data Provenance

For every dataset used:

- **Source**: Where does it come from?
- **Access**: Public download URL, API, application required, or restricted?
- **Date obtained**: When was it downloaded?
- **Citation**: How should the data be cited?
- **Included in package?**: Yes (small/public) or No (too large/restricted) with access instructions

## Step 6: Verification Checklist

Run these 10 checks and report pass/fail for each:

### Checks 1–4: Standard (always run)

| # | Check | How | Pass Criteria |
|---|-------|-----|---------------|
| 1 | **Manuscript compiles** | Run `quarto render` or `pdflatex` | Exit code 0, no errors |
| 2 | **All scripts execute** | Run each script in order | All exit code 0 |
| 3 | **All referenced files exist** | Parse scripts for file paths, check existence | No missing inputs or outputs |
| 4 | **Outputs are fresh** | Compare output timestamps to script timestamps | Outputs newer than scripts |

### Checks 5–10: Submission (run before journal submission)

| # | Check | How | Pass Criteria |
|---|-------|-----|---------------|
| 5 | **Package inventory** | Parse all scripts for package/library calls | All packages documented |
| 6 | **Dependencies documented** | Check for lockfile or requirements file | Lockfile exists per language |
| 7 | **Data provenance documented** | Check for data README or documentation | Every dataset has source and access info |
| 8 | **End-to-end reproducibility** | Delete all outputs, run master script, verify regeneration | All outputs recreated |
| 9 | **Output-to-paper cross-reference** | Extract `\input{}`, `\includegraphics{}` from manuscript, verify files exist | All referenced files exist, no orphan outputs |
| 10 | **README completeness** | Check README sections against AEA standard | All required sections present |

## Output

Save all outputs to the project root and `quality_reports/submission/`:

**In project root:**
- `run_all.R` (or equivalent master script)
- `README_replication.md`
- Dependency files (`renv.lock`, `requirements.txt`, etc.)

**In `quality_reports/submission/`:**
- `verification_report.md` — full 10-point checklist with pass/fail and details
- `package_inventory.md` — all packages, versions, and sources

## Verification Report Format

```markdown
# Submission Verification Report
**Date:** [YYYY-MM-DD]
**Project:** [project name]

## Overall Status: [PASS / FAIL / PASS with warnings]

| # | Check | Status | Details |
|---|-------|--------|---------|
| 1 | Manuscript compiles | PASS | 0 errors, 2 warnings (overfull hbox) |
| 2 | Scripts execute | PASS | 4/4 scripts, total runtime 3m 42s |
| ... | ... | ... | ... |

## Blocking Issues
[Any FAIL items that must be resolved]

## Warnings
[Non-blocking issues: stale outputs, deprecated packages, etc.]

## Package Manifest
[Full list of packages by language with versions]
```

## Three-Strikes Escalation

If the end-to-end reproducibility check (Check 8) **fails three consecutive times** after fixes:

- Stop
- Produce a focused report listing the persistent failures
- Flag: "Reproducibility issues have persisted through 3 fix cycles. The project may have hidden dependencies, environment-specific behavior, or data that cannot be regenerated from the included scripts. Manual investigation required."

## Principles

- **Mechanical checks only.** This skill verifies that things work — it does not evaluate research quality.
- **A stranger must be able to reproduce.** The test is: could someone with no project knowledge, following only the README, reproduce every output?
- **Relative paths only.** Any absolute path is a failure.
- **Don't modify analysis scripts.** Build the package around the existing code. If a script has a bug, report it — don't fix it (that's the researcher's job).
- **Data sensitivity.** Never include data that should not be shared. If data is restricted, document how to obtain it and provide a synthetic or anonymized sample for testing if possible.
