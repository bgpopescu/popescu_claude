---
name: discover-lit
description: "Systematic literature discovery for a research question. Searches databases, produces annotated bibliography with proximity scoring, BibTeX entries, a frontier map identifying gaps, and a positioning statement. Use when starting a literature review, scoping a new project, or checking for scooping risk."
argument-hint: "[research-question-or-topic]"
allowed-tools: Read, Write, Grep, Glob, WebSearch, WebFetch
---

# Discover-Lit: Structured Literature Search

Systematic literature discovery that produces four deliverables: annotated bibliography, BibTeX file, frontier map, and positioning statement. Includes a built-in quality audit.

## Input

`$ARGUMENTS` — a research question, topic description, or path to a README/strategy memo. If a domain profile exists (from `/domain-profile`), read it first to calibrate journal targets and field conventions.

---

## Step 1: Define Search Scope

Before searching, clarify with the user (or infer from the research question):

- **Treatment/intervention**: What is being studied?
- **Outcome**: What effect is measured?
- **Context**: Population, geography, time period
- **Design constraints**: What identification strategy is likely? (This shapes which methods papers to find)

## Step 2: Search Systematically

Execute searches in this order:

### 2a. Anchor Papers

Find 2–3 recent (last 3 years) or landmark papers directly addressing the question. These are seeds for citation chains.

### 2b. Forward and Backward Citation Chains

- Who cites the anchor papers? (forward)
- What do the anchor papers cite? (backward)
- Depth: 2 generations

### 2c. Top Journal Scan

Search the top-5 general journals (AER, Econometrica, JPE, QJE, REStud) plus the top 3–5 field journals relevant to the topic. Last 5 years, using topic keywords.

If a domain profile exists, use its journal tiers. Otherwise, infer the relevant field journals from the research question.

### 2d. Working Paper Repositories

- NBER working papers (keyword search, sort by recency)
- SSRN (field-specific collections)
- RePEc (author + topic search)

### 2e. Author Tracking

Identify 2–4 prolific researchers on the topic. Scan their recent CVs or Google Scholar profiles for working papers and concurrent projects. Flag **scooping risk** — ongoing work that addresses the same question.

### 2f. Screening and Deduplication

Remove duplicates (same paper across venues). Prioritize published over working paper versions.

## Step 3: Document Each Paper

For every paper included, record:

```markdown
### [Author(s)] ([Year]). "[Title]"
**Venue:** [Journal or Working Paper series]
**Proximity:** [1–5] (see scale below)
**Category:** [Directly Related | Methodological Precedent | Complementary Evidence | Theoretical Foundations | Methods Paper]

**Summary:** [2–3 sentences: research question, method, key finding]

**Key Variables/Data:** [Treatment, outcome, data source — one sentence]

**Relevance to This Project:** [Why included — one sentence]
```

### Proximity Scoring

| Score | Meaning |
|-------|---------|
| **5** | Directly related — same outcome/treatment/context |
| **4** | Closely related — same method or outcome, different context |
| **3** | Methodologically relevant — different outcome, same identification strategy |
| **2** | Tangentially related — theoretical foundation or broader context |
| **1** | Included for completeness or background |

### Relevance Categories

| Category | What It Covers | Target Count |
|----------|---------------|-------------|
| **Directly Related** | Same or very similar research question | 5–15 papers |
| **Methodological Precedent** | Uses your identification strategy in a different context | 5–10 papers |
| **Complementary Evidence** | Adjacent outcomes, pieces of the causal chain | 5–15 papers |
| **Theoretical Foundations** | Theory relevant to the causal mechanism | 3–10 papers |
| **Methods Papers** | Foundational econometric papers on the chosen design | 2–5 papers |

## Step 4: Produce Outputs

### Output 1: Annotated Bibliography (`bibliography.md`)

Organized by relevance category. Each paper uses the template from Step 3.

### Output 2: BibTeX File (`bibliography.bib`)

Machine-readable bibliography. Ensure consistent formatting:

```bibtex
@article{Card1994,
  author  = {Card, David and Krueger, Alan B.},
  year    = {1994},
  title   = {Minimum {W}ages and {E}mployment},
  journal = {American Economic Review},
  volume  = {84},
  number  = {4},
  pages   = {772--793},
  doi     = {10.2307/2118030}
}
```

### Output 3: Frontier Map (`frontier_map.md`)

Identify what's been done, what's active, and what's missing:

```markdown
# Literature Frontier Map

## Established Territory
- [What is well-studied and settled]

## Active Research Frontiers
- [Recent papers, emerging consensus, ongoing debates]

## Gaps
- [What nobody has done — potential contributions]
- [Scooping risk: who is working on related questions right now]
```

### Output 4: Positioning Statement (`positioning.md`)

1–2 paragraphs placing the project in the literature:
- Paragraph 1: What the existing literature establishes
- Paragraph 2: What this project contributes that is novel

## Step 5: Quality Audit

After producing all outputs, switch perspective to a literature quality critic and check:

| Check | What to Look For | Deduction if Missing |
|-------|-----------------|---------------------|
| **Coverage gaps** | Missing subfields, seminal papers, methods literature | -15 to -20 |
| **Journal quality** | Over-reliance on working papers (>50% unpublished) | -10 |
| **Top-5 representation** | Missing papers from AER/Econometrica/JPE/QJE/REStud that address the topic | -10 |
| **Recency** | Missing papers from last 2 years | -10 |
| **Scope calibration** | Too narrow (single subfield) or too broad (unfocused) | -10 |
| **Frontier map accuracy** | Gaps identified are genuine, not just overlooked papers | -5 |
| **Proximity scores** | Consistent and reasonable across papers | -5 |

Score starts at 100. If below 80, revise the bibliography before delivering.

## Coverage Targets

| Metric | Target |
|--------|--------|
| Total papers | 25–40 |
| Published vs. working paper | 60–70% published |
| Directly related (proximity 5) | 8–12 papers |
| Methods/precedent | 5–8 papers |
| Recency (last 3 years) | 30% of papers |

## Output Location

Save all outputs to `quality_reports/literature/`:
- `bibliography.md`
- `bibliography.bib`
- `frontier_map.md`
- `positioning.md`
- `literature_audit.md` (the quality audit results)

## Principles

- **Verify every citation.** Do not invent papers. If you cannot confirm a paper exists (title, authors, venue), do not include it. Say "I could not verify this reference" rather than fabricating a plausible-sounding citation.
- **Proximity scores are honest.** A proximity-5 paper must genuinely address the same question. Don't inflate scores to pad the bibliography.
- **The frontier map is the most valuable output.** It tells the researcher where the contribution lies. Get this right.
- **Working papers are not second-class.** Include them, but note that results may change before publication. Flag any where a published version exists.
- **Flag scooping risk explicitly.** If someone is working on the same question right now, the researcher needs to know.
