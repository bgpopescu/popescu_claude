---
name: domain-profile
description: "Create or update a domain profile that calibrates all skills to your specific field. Captures target journals, common data sources, identification strategies, field conventions, and referee concerns. Used by editorial-review, discover-lit, strategize, and other skills. Run once per field, update as needed."
argument-hint: "[field-name-or-subfield]"
allowed-tools: Read, Write, Edit, Glob
---

# Domain Profile: Field Calibration

Create a structured profile that tells all other skills what field you work in, which journals you target, what methods are standard, and what referees care about. This profile is read by `/editorial-review` (for `--peer` calibration), `/discover-lit` (for journal and search scope), `/strategize` (for design conventions), and `/submit-package` (for field-specific norms).

## Input

`$ARGUMENTS` — a field name (e.g., "political economy", "development economics", "comparative politics") or a subfield description. If a CLAUDE.md or README.md exists, read it first for context about the researcher's work.

---

## How It Works

### Interactive Mode (default)

Walk the user through a structured interview. For each section, propose defaults based on the field name and ask the user to confirm, modify, or add entries.

### Quick Mode

If the user provides enough context (field name + a paper or project description), generate the profile directly and present it for review.

---

## Profile Sections

### 1. Field and Adjacent Subfields

```markdown
## Field
**Primary:** [e.g., Political Economy]
**Adjacent subfields:** [e.g., Development Economics, Comparative Politics, Economic History]
**Methodological home:** [e.g., Applied Microeconometrics]
```

### 2. Target Journals (tiered)

```markdown
## Target Journals

### Tier 1 (Top-5 Generals)
- American Economic Review (AER)
- Quarterly Journal of Economics (QJE)
- Econometrica
- Journal of Political Economy (JPE)
- Review of Economic Studies (REStud)

### Tier 2 (Top Field)
- [e.g., American Political Science Review (APSR)]
- [e.g., Journal of Development Economics (JDE)]
- [e.g., Journal of the European Economic Association (JEEA)]

### Tier 3 (Strong Field)
- [e.g., World Development]
- [e.g., Economic Development and Cultural Change]
- [e.g., Journal of Comparative Economics]

### Tier 4 (Solid Outlets)
- [e.g., Economics Letters]
- [e.g., Journal of Development Studies]
```

### 3. Common Data Sources

```markdown
## Common Data Sources in This Field
| Source | What It Covers | Access |
|--------|---------------|--------|
| [e.g., DHS] | [Health, demographics in developing countries] | [Public, registration required] |
| [e.g., World Bank WDI] | [Country-level development indicators] | [Public API] |
| [e.g., V-Dem] | [Democracy indicators, 1789–present] | [Public download] |
```

### 4. Common Identification Strategies

```markdown
## Common Identification Strategies
| Strategy | Typical Use in This Field | Key References |
|----------|--------------------------|----------------|
| [e.g., DiD] | [Policy reforms with staggered adoption] | [Callaway & Sant'Anna 2021] |
| [e.g., IV] | [Historical instruments for institutions] | [Acemoglu, Johnson & Robinson 2001] |
| [e.g., RDD] | [Electoral thresholds, eligibility cutoffs] | [Lee 2008] |
```

### 5. Field Conventions

```markdown
## Field Conventions
- **Standard errors:** [e.g., Clustered at country or district level; report both robust and clustered]
- **Fixed effects:** [e.g., Country + year FE standard for panel data]
- **Tables:** [e.g., Regression tables with stars, means in brackets, N reported]
- **Figures:** [e.g., Event study plots expected for DiD; coefficient plots preferred over regression tables for main results]
- **Notation:** [e.g., $Y_{it}$ for outcomes, $D_{it}$ for treatment, $X_{it}$ for controls]
- **Software:** [e.g., R (fixest, sf) or Stata (reghdfe) standard; Python less common]
```

### 6. Seminal References

```markdown
## Seminal References
Papers that every reviewer in this field knows and expects you to engage with:

- [e.g., Acemoglu, Johnson & Robinson (2001) — Colonial origins of development]
- [e.g., Dell (2010) — Persistent effects of Peru's mining mita]
- [e.g., Nunn (2008) — Long-term effects of Africa's slave trades]
```

### 7. Referee Concerns

```markdown
## What Referees in This Field Care About
- [e.g., Identification must be explicit — "selection on observables" is a hard sell]
- [e.g., External validity beyond the specific context studied]
- [e.g., Mechanisms, not just reduced-form effects]
- [e.g., Historical accuracy when using historical data or natural experiments]
- [e.g., Robustness to alternative estimators for staggered DiD]

## Common Referee Objections
- [e.g., "The parallel trends assumption is not credible because..."]
- [e.g., "The instrument could affect the outcome through channels other than..."]
- [e.g., "The sample is too narrow to generalize to..."]
```

---

## Output

Save the profile to `claude/domain-profile.md` (or `.claude/domain-profile.md` if using the hidden directory convention).

If a profile already exists, show the diff and ask the user to confirm changes before overwriting.

## How Other Skills Use the Profile

| Skill | What It Reads |
|-------|--------------|
| `/editorial-review --peer` | Target journals (tier calibration), referee concerns |
| `/discover-lit` | Target journals (search scope), seminal references (anchor papers), common data sources |
| `/strategize` | Common identification strategies, field conventions, referee concerns |
| `/submit-package` | Field conventions (table format, software norms) |
| `/presentation-builder` | Field conventions (notation, figure style) |

## Principles

- **One profile per field.** If you work across fields (e.g., political economy + economic history), create one profile that covers both, noting where conventions differ.
- **The profile is a living document.** Update it as you learn what referees care about, discover new journals, or shift subfields.
- **Defaults are suggestions.** The user always has final say. Propose smart defaults based on the field name, but ask before committing.
- **Don't overcomplicate.** A 50-line profile that's accurate beats a 200-line profile that's partly wrong. Start with what you know and add as needed.
