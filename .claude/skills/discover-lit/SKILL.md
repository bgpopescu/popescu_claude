---
name: discover-lit
description: "Structured literature discovery. Produces annotated bibliography, BibTeX, frontier map, and positioning statement. Use when starting a lit review or scoping a project."
argument-hint: "[research-question-or-topic]"
disable-model-invocation: true
allowed-tools: Read, Write, Grep, Glob, WebSearch, WebFetch
---

# Discover-Lit: Structured Literature Search

**This supplements, but does not replace, a manual database search (Scopus, Web of Science, Google Scholar).** AI-assisted literature search has real limitations — paywalled content, incomplete metadata, hallucination risk on citations. Always verify results against a proper database.

Structured literature discovery that produces four deliverables: annotated bibliography, BibTeX file, frontier map, and positioning statement. Works best when the user provides seed papers or an existing .bib file; web search supplements but cannot replace a proper database search.

## Input

`$ARGUMENTS` — a research question, topic description, path to a README/strategy memo, or seed papers/bibliography to organize and extend.

---

## Step 1: Define Search Scope

If the input is a single sentence or vague topic, ask the user to clarify the following before searching. If a strategy memo, detailed description, or seed papers are provided, infer these directly:

- **Treatment/intervention**: What is being studied?
- **Outcome**: What effect is measured?
- **Context**: Population, geography, time period
- **Design constraints**: What identification strategy is likely? (This shapes which methods papers to find)
- **Disciplinary scope**: Single discipline or interdisciplinary? If the question spans fields (e.g., economics and political science), search top journals in each and include methods papers from both traditions.
- **Terminology**: Identify synonyms across fields for key concepts (e.g., "instrumental variables" in economics vs. "Mendelian randomization" in epidemiology). Use all variants in search queries.

## Step 2: Search

### Tier 1 — Reliable (web search can do this well)

- **Anchor papers**: Find 2–3 recent or landmark papers directly addressing the question via keyword web search
- **Working paper repositories**: Google Scholar, SSRN, discipline-specific repositories (e.g., NBER/RePEc for economics, APSA Preprints for political science)
- **Keyword search**: Top journals in the relevant discipline plus field journals, last 5 years. Infer the relevant journals from the research question and discipline.

### Tier 2 — Best-effort (attempt but flag as incomplete)

- **Forward/backward citation chains**: Who cites the anchor papers, and what do they cite? Depth: 2 generations. *These results will be partial — flag gaps for the user to verify against a citation database.*
- **Author tracking**: Identify 2–4 prolific researchers on the topic. Scan Google Scholar profiles for working papers and concurrent projects. Flag **scooping risk**. *Profile access is inconsistent — report what you find and what you couldn't access.*
- **Journal archive scans**: Targeted searches within specific journal archives. *Many archives block scraping — note which journals you could and couldn't search.*

### Before searching

If the user provided an existing .bib file or seed papers, read them first. These are your starting point — exclude them from search results and build outward. Extend, don't duplicate.

### Screening
- Remove duplicates (same paper across venues). Prioritize published over working paper versions.
- **Stop condition**: Stop searching when two consecutive search rounds yield no new proximity-4+ papers. On broad topics, cap at the coverage guidelines rather than spiraling.

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
| **Methods Papers** | Foundational papers on the chosen design | 2–5 papers |

Not all categories need to hit the upper bound — these are guidelines for a mature literature. Smaller or newer fields will have fewer papers.

## Step 4: Produce Outputs

### Output 1: Annotated Bibliography (`bibliography.md`)

Organized by relevance category. Each paper uses the template from Step 3.

### Output 2: BibTeX File (`bibliography.bib`)

Machine-readable bibliography. Ensure consistent formatting. Note: web-scraped metadata (volume, pages, DOI) is often incomplete or wrong — flag entries with missing fields so the user can verify against the publisher page.

```bibtex
@article{Card1994,
  author  = {Card, David and Krueger, Alan B.},
  year    = {1994},
  title   = {{Minimum Wages and Employment}},
  journal = {{American Economic Review}},
  volume  = {84},
  number  = {4},
  pages   = {772-793},
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

## Step 5: Verification Pass

Before finalizing, re-search each included paper's exact title to confirm it exists. For every paper:

- If the title, authors, and venue are confirmed: keep. Attempt to complete missing BibTeX metadata (volume, pages, DOI) from the DOI or publisher page when accessible.
- If the paper cannot be found via title search: remove it and note "could not verify" in the audit.
- If metadata differs from what you recorded (different year, different journal): correct it.

This step catches hallucinated citations — the single biggest risk in AI-assisted literature search.

## Step 6: Quality Audit

After producing all outputs, check for:

- **Coverage gaps** — missing subfields, seminal papers, or methods literature
- **Journal quality** — over-reliance on working papers (aim for majority published)
- **Top journal representation** — missing papers from the discipline's top general journals
- **Recency** — missing papers from last 2 years
- **Scope calibration** — too narrow (single subfield) or too broad (unfocused)
- **Frontier map accuracy** — gaps identified are genuine, not just overlooked papers
- **Proximity scores** — consistent and reasonable across papers

If major gaps are found, revise before delivering. Flag any areas where web search limitations prevented thorough coverage.

## Coverage Guidelines

These are guidelines, not hard targets — actual counts depend on the topic's breadth and what web search can access:

- Aim for 15–25 well-verified papers; larger literatures may warrant more, but quality over volume
- Majority published over working papers
- Include recent work (last 3 years) alongside foundational papers

## Output Location

Save all outputs to `quality_reports/literature/` (or project root if that directory doesn't exist):
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
