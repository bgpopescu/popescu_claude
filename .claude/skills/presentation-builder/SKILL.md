---
name: presentation-builder
description: "Build academic Quarto RevealJS slide decks from papers, notes, outlines, or lecture topics. Handles theme design, rhetoric restructuring, figure/table insertion, Mermaid diagrams, and multi-agent review. Supports conference talks, job talks, seminars, and full lecture decks. Use whenever the user mentions slides, deck, presentation, lecture, RevealJS, or wants to turn a paper into a talk."
argument-hint: "[paper-path-or-topic]"
allowed-tools: Bash(which*), Bash(quarto*), Bash(python*), Bash(Rscript*), Bash(pip*), Bash(npx*), Bash(mkdir*), Bash(ls*), Bash(cp*), Read, Write, Edit, Agent
---

# Presentation Builder: Academic Quarto RevealJS Decks

Build publication-quality academic slide decks using Quarto RevealJS.

## Preflight Check

Before doing anything else, verify Quarto is installed:
```bash
which quarto && quarto --version
```
If Quarto is not found, stop and tell the user to install it (https://quarto.org/docs/get-started/).

## Hard Constraints (read these first — most common failures)

These rules override everything else. Violating any of them produces broken or unreadable slides.

| # | Rule | Why |
|---|------|-----|
| 1 | **No dark backgrounds on content slides.** Only `#` section dividers get `{background-color}`. Content slides (`##`) use the default light theme. Title slide uses `data-background-color: "#f9fafb"`. | The SCSS theme uses dark text (slate, graphite, stone). Dark backgrounds make text invisible. |
| 2 | **Mermaid node labels must be simple.** Never start label text with `number.` (e.g., `1.`, `2.`) — this triggers "Unsupported markdown: list" errors. Use `<br/>` for line breaks, never `\n`. When in doubt, use single-word labels and put detail in fragment text below the diagram. | Mermaid's parser interprets `number.` as markdown list syntax inside node labels, even with `<br/>`. |
| 3 | **Mermaid theme is `'neutral'`, never `'dark'`.** | Content slides have light backgrounds. Dark-themed diagrams don't contrast. |
| 4 | **Every Mermaid diagram needs `%%{init:{...}}%%`** with `useMaxWidth: true`, `fontSize: "22px"`, `nodeSpacing: 50`, `rankSpacing: 50`. | Without explicit sizing, diagrams render too small or overflow. |
| 5 | **Output is RevealJS HTML, never PowerPoint or Beamer.** | The `.qmd` renders to `.html` via `quarto render`. |
| 6 | **Use `theme_meridian()` for all ggplot2 figures, never `theme_bw()`.** | Matches the slide SCSS theme colors. |
| 7 | **All figures/tables need a source note.** Use "Author's illustration" or "Simulated example" — never invent sources. | Missing sources look sloppy and fabricated sources are academic misconduct. |

## When to Use

The user wants to create or improve a slide deck for an academic talk. Input is typically:
- A paper (PDF, .tex, .qmd) to restructure into slides
- An outline, notes, or topic description
- An existing .qmd deck to improve

If the input is a PDF, use the Read tool with the `pages` parameter (e.g., `pages: "1-10"`) to read it in chunks of up to 20 pages.

## The Three Laws

Apply these as design constraints on every slide decision:

1. **Beauty is function.** Every element must earn its presence. Decoration without function is noise. The most beautiful slide may be three words on a blank background.
2. **Cognitive load is the enemy.** One idea per slide. Every unnecessary word steals bandwidth from the message.
3. **The slide serves the spoken word.** Slides are visual anchors — attention focal points, memory hooks, evidence supports. They are not scripts to read aloud.

## Core Design Principles

**Titles are assertions, not labels.**
Bad: "Results." Good: "Colonial institutions explain 75% of income variation." If someone reads only your slide titles in sequence, they should understand the entire argument. The titles ARE the argument; everything else is evidence.

**Bullets are a confession of defeat.**
A bullet list says "I couldn't figure out how these ideas relate." Usually there's a structure hiding in the bullets: a sequence, a contrast, a hierarchy, a causal chain. Find it. Make it visible with a diagram, table, fragments, or prose. If a list truly is the right format, maximum 3 items.

**MB/MC equivalence across slides.**
The marginal benefit-to-cost ratio should be equal across all slides. A deck that overloads slide 7 and coasts on slide 12 has a distribution problem. Smooth the cognitive load.
Exception: deliberate "jump scares" — a striking statistic, a provocative claim — spikes used for intentional rhetorical effect.

**Beautiful quantification.**
Numbers should tell a story. Don't dump a regression table — highlight the one or two coefficients that matter. Use color or boxes to draw the eye. If you can't explain what the chart proves in one sentence, the chart is too complex.

**Visual hierarchy.**
Not all information is equal. Primary: the one thing you want remembered (large, prominent). Secondary: supporting evidence (smaller, subordinate). Tertiary: context and sourcing (smallest, footer). If everything is emphasized, nothing is emphasized.

**White space is confidence.**
Crowded slides signal fear — fear that you'll leave something out. White space signals confidence. You know what matters.

## Cognitive Density Constraints

These are hard limits, not guidelines:

- **Maximum 6 bullets per slide.** If you have more, restructure into multiple slides or a table.
- **Maximum 12 words per bullet.** If a bullet needs more words, it's a sentence — put it in prose or break it up.
- **1 visual OR 1 table per slide** (not both), except designated summary slides.
- **Captions must be fewer than 15 words.**

## Section Structure

- **Section header slides are mandatory.** Every major section begins with a dedicated slide using `# Section Title {background-color="#1e293b"}`. No content on these slides — just the section title.
- **Content slides use the default light background.** Do NOT add `{background-color}` to content slides (`##`). The SCSS theme uses dark text colors (slate, graphite, stone) designed for light backgrounds. Dark backgrounds make text invisible or illegible. Only `#` section dividers get colored backgrounds.
- **Mermaid diagrams use `'neutral'` theme, not `'dark'`.** Since content slides have light backgrounds, Mermaid diagrams must use `theme: 'neutral'` (or `'default'`). The `'dark'` theme produces light-on-dark rendering that doesn't contrast on light slide backgrounds.
- **"So what?" bridge slides.** Every section must end with a short bridge slide that motivates the next section. This creates forward momentum and prevents the talk from feeling like a list of disconnected topics.

## Rhetoric and Structure

**Persuasion balance (Ethos / Pathos / Logos):** Conference talk ~ 20/35/45. Job talk: shift toward more Ethos. Ethos = methodology transparency, acknowledging weaknesses. Pathos = audience-relevant problems, human impact. Logos = data, comparisons, logical flow.

**Three-Act Arc:** Act I (Problem/Tension) → Act II (Investigation/Development) → Act III (Resolution/Takeaway).

**Pyramid Principle:** Lead with the conclusion, then support it. Structure: (1) claim, (2) evidence, (3) implications. Never: background → background → complication → analysis → finding.

**Devil's Advocate:** Include a slide addressing the strongest objection to your argument. Builds ethos, preempts questions.

**Openings:** First slide after title is most important. Good: provocative question, surprising statistic, concrete problem. Bad: "Today I'm going to talk about...", 12-item agenda, definitions.

**Closings:** Good: single memorable takeaway, return to opening now resolved. Bad: "Questions?", summary repeating everything, "Thank You."

## Typography Rules

- Minimum 24pt body text (18pt absolute floor)
- Maximum two fonts (one heading, one body), sans-serif for projection
- Never justify text — ragged right is easier to read

## YAML Header

Always use this exact structure (customize title, subtitle, footer):

```yaml
---
title: "Title Here"
subtitle: "Subtitle Here"
format:
  revealjs:
    theme: _extensions/meridian.scss
    self-contained: true
    footer: "Popescu (TEC) — Short Title"
    slide-number: true
title-slide-attributes:
  data-background-color: "#f9fafb"
author:
  - name: Bogdan G. Popescu
    orcid: 0000-0002-7730-7495
    email: bgpopescu@tec.mx
    affiliations: "Tecnológico de Monterrey"
---
```

**Do NOT use dark backgrounds (`#1e293b`) on the title slide or any content slide.** The meridian theme uses dark text colors designed for the cream (`#f9fafb`) background. Dark backgrounds make title, subtitle, and author text invisible.

If `_extensions/meridian.scss` doesn't exist, create an original, professional RevealJS SCSS theme — distinctive but appropriate for academic contexts. Professional color palette, clean typography, appropriate for the audience.

## ggplot Setup Chunk

Immediately after the YAML header, insert this setup chunk verbatim. It defines the color palette and theme function used by all figures in the deck:

````
```{r}
#| label: setup
#| echo: false

library(ggplot2)
library(dplyr)
library(tidyr)
library(scales)

# Meridian palette (matches SCSS)
slate      <- "#1e293b"
graphite   <- "#334155"
stone      <- "#64748b"
terracotta <- "#b44527"
sage       <- "#4a7c6f"
gold       <- "#b7943a"
cream      <- "#f9fafb"

theme_meridian <- function(base_size = 14) {
  theme_minimal(base_size = base_size) +
  theme(
    plot.title    = element_text(color = slate, face = "bold", size = rel(1.15)),
    plot.subtitle = element_text(color = stone, size = rel(0.85), margin = margin(b = 10)),
    plot.caption  = element_text(color = stone, hjust = 0, size = rel(0.7), margin = margin(t = 10)),
    axis.title    = element_text(color = graphite, size = rel(0.9)),
    axis.text     = element_text(color = stone, size = rel(0.8)),
    axis.line     = element_line(color = graphite, linewidth = 0.5),
    axis.ticks    = element_line(color = graphite, linewidth = 0.4),
    axis.ticks.length = unit(3, "pt"),
    panel.grid.minor  = element_blank(),
    panel.grid.major  = element_line(color = "#d6dbe1", linewidth = 0.3),
    plot.background   = element_rect(fill = cream, color = NA),
    panel.background  = element_rect(fill = cream, color = NA),
    legend.position   = "bottom",
    legend.text   = element_text(color = graphite, size = rel(0.8)),
    legend.title  = element_text(color = slate, size = rel(0.85))
  )
}
```
````

Use `theme_meridian()` for all ggplot2 figures. Do NOT use `theme_bw()` or other themes.

## Step 1: Understand the Source Material

Before writing a single slide:

1. Read the paper/notes thoroughly (for PDFs, use Read tool with `pages` parameter in chunks of up to 20 pages)
2. Identify the **argument arc**: What question → why it matters → how you answer it → what you found → why anyone should care
3. List every figure and table that should appear in the deck
4. Note the identification strategy — this is the intellectual core of the talk
5. Detect the author's pedagogy and voice — the restructured deck must maintain it

Do not start building slides until you can state the argument arc in 3 sentences. The goal is to **restructure the rhetoric** of the source material, not simply slide-ify it.

## Step 2: Plan the Slide Structure

Create a slide outline before writing any .qmd content. Each slide entry should have:
- An assertion title (not a label)
- What the slide contains (figure, diagram, text, code)
- Approximate cognitive weight (light / medium / heavy)

Check for MB/MC equivalence: no cluster of consecutive heavy slides, no long stretches of light slides. Redistribute if needed.

Typical conference deck structure (20 minutes), roughly 12–16 content slides:

| Slides | Content | Weight |
|--------|---------|--------|
| 1 | Title | light |
| 1–2 | Motivation (why this question matters) | medium |
| 1 | Literature positioning (what's missing) | medium |
| 1–2 | Data | medium |
| 1–2 | Identification / method (intellectual core) | heavy |
| 1 | Devil's Advocate (strongest objection + response) | medium |
| 3–5 | Results (figures and tables, not text) | heavy |
| 1 | Robustness / extensions | medium |
| 1 | Conclusion (single memorable takeaway) | light |

More than 20 slides signals poor distribution.

## Step 3: Build the Deck

### Figures and Tables

- Run all code FIRST to generate figures. Do not insert placeholders.
- Use R (ggplot2 with `theme_meridian()`) for all data visualizations
- **Use real data** whenever possible: built-in datasets, reputable R packages (e.g., gapminder), or data you can reproducibly download/scrape. Use simulated data sparingly and always label it clearly as "Simulated example" in the caption.
- One message per chart. Remove chartjunk. Label directly (avoid legends requiring eye movement). Use color to highlight the key comparison.
- Every figure or table must include: a clear title, labeled axes/columns, units where applicable, and a source note. Do NOT invent sources — use "Author's illustration" or "Simulated example" when appropriate.
- Insert with explicit width control:
  ```
  ![Caption text](figures/figure_1.png){width=70%}
  ```
- After placing each image, verify: (1) key details visible at presentation scale, (2) full caption visible without overflow. If either fails, adjust width within 60–80% range.

### Mermaid Diagrams

Use Mermaid for all conceptual and causal diagrams. Write inline in the .qmd file:

````
```{mermaid}
graph LR
    A[Treatment] --> B[Outcome]
    C[Instrument] --> A
```
````

Do NOT store Mermaid files separately. Diagrams render natively via Quarto's Mermaid support.

**Auto-Fit Rule (MANDATORY for every Mermaid diagram):**

Every Mermaid chunk MUST include a `%%{init:{...}}%%` block that forces slide-fit:

- `flowchart.useMaxWidth: true`
- Set `themeVariables.fontSize` to 20–24px (readable from back of room)
- Increase spacing: `nodeSpacing: 50`, `rankSpacing: 50` (or higher as needed)
- Set fixed renderer size: `width: 1100` to `1200`, `height: 600` to `700`

Example:
````
```{mermaid}
%%{init: {"theme": "neutral", "flowchart": {"useMaxWidth": true, "nodeSpacing": 50, "rankSpacing": 50}, "themeVariables": {"fontSize": "22px"}}}%%
flowchart LR
    A["Treatment<br/>Assignment"] --> B["Outcome<br/>Variable"]
    C["Instrument"] --> A
```
````

**Mermaid design rules:**
- Use `flowchart LR` by default
- Keep diagrams simple: 5–8 nodes, never more than 10
- Use explicit line breaks in node labels with `<br/>`
- Avoid low-contrast colors — diagrams must be projector-safe
- If a diagram overflows or leaves large whitespace, revise width/height, fontSize, and spacing until it fits cleanly within one slide
- If a diagram fails to compile or remains unreadable after fixes, replace it with a ggplot2 schematic. Do not leave broken or partially rendered diagrams.

### Slide Fragments

Use RevealJS fragments to control cognitive load within dense slides:

```
::: {.fragment}
First point appears...
:::

::: {.fragment}
Then this builds on it.
:::
```

This prevents the audience from reading ahead and lets you control the narrative pace.

### Directory Structure

```
presentations/
├── _extensions/
│   └── meridian.scss
├── presentation.qmd
├── figures/
│   ├── figure_1.png
│   └── figure_2.png
└── tables/
    └── table_1.tex
```

## Step 4: Compile → Fix → Recompile Loop

```
while not all_tests_pass:
    quarto render presentation.qmd
    fix_all_warnings()    # treat ALL warnings as errors
    check_visual_errors() # compilation success ≠ visual success
    check_flow_and_balance()
```

Treat ALL Quarto warnings as errors. Fix every warning before proceeding, no matter how minor.

## Step 5: Check for Silent Visual Errors

Compilation success does not mean visual success. These errors are silent:

**Figures:**
- Axis labels cut off or overlapping
- Legend obscuring data points
- Image too small to read at presentation scale (<40% column width)
- Caption clipped or overflowing

**Mermaid diagrams:**
- Node text truncated
- Arrow directions wrong
- Layout inconsistent with slide aesthetic

**Cognitive load:**
- Slide too dense (too many elements competing)
- Adjacent slides with wildly different density
- A slide with nothing but a title and one sentence (wasted)

**How to verify:** Generate a visual proof PDF from the rendered HTML, then inspect it slide-by-slide:

```bash
npx decktape reveal presentation.html slides-proof.pdf --size 1920x1080
```

If `decktape` is not available, it will be auto-installed via npx on first run. Then use the Read tool with the `pages` parameter to visually inspect each slide:
- `Read("slides-proof.pdf", pages: "1-5")` — check first batch
- `Read("slides-proof.pdf", pages: "6-10")` — check next batch
- Continue until all slides are inspected

Look for every issue listed above in the actual rendered output. This catches problems that code review alone cannot detect.

## Step 6: Multi-Agent Review

After the first complete compile, run a review cycle with three agents:

**Agent 1 (Builder):** Created the deck (already done).

**Agent 2 (Rhetoric & Content Reviewer):** Evaluate the full deck for:
- Narrative flow — does the argument build logically through the three-act arc?
- MB/MC balance — is cognitive load distributed evenly?
- Title quality — are ALL titles assertions, not labels?
- Pyramid principle — does the conclusion appear early, then get supported?
- Technical rigor — is the identification strategy clear?
- Devil's Advocate — is the strongest objection addressed?
- **Informational accuracy** — are all claims pedagogical or supported by real sources? Flag any fabricated data, results, institutions, or citations.
- **Redundancy** — remove or consolidate repetitive information across slides
- **Citation check** — verify that every cited work exists and is accurately described
- Graphics coordinate verification — do labels and positions match intent?

**Agent 3 (Graphics Specialist):** Read `slides-proof.pdf` (from Step 5) page by page using the Read tool with `pages` parameter, and check ONLY visual elements:
- Figure sizing and readability at presentation scale
- Mermaid node positions match intended layout
- ggplot label placement and axis readability
- Caption visibility
- **Numerical accuracy** in all figures and tables
- Consistent visual style across all graphics

Apply the feedback. Recompile. Re-generate the proof PDF and verify again.

## Step 7: Final Compile

After incorporating review feedback:

1. Compile one final time
2. Verify zero warnings
3. Re-generate the proof PDF: `npx decktape reveal presentation.html slides-proof.pdf --size 1920x1080`
4. Read the proof PDF page by page with the Read tool — verify every image and caption is fully visible
5. Confirm the argument arc is intact from first to last slide

Only when the deck passes all checks is it ready for human walkthrough.

## Common Failures Checklist

Before delivering, check for these:

| Failure | Symptom | Fix |
|---------|---------|-----|
| Text walls | Slides that could be printed as paragraphs | Extract the key phrase; everything else is speaker notes |
| Burying the lede | Key point appears on slide 15 | State conclusion on slide 2, then prove it |
| Chartjunk | 3D effects, gradient fills, decorative axes | Remove elements until removing more would remove meaning |
| Agenda overload | Opening agenda with 8–12 items | 3 sections maximum, or cut the agenda entirely |
| "Questions?" ending | Final slide is generic | End with key takeaway or call to action |
| Evidence-free claims | Assertions without support | Every claim gets a source |
| Anxiety overload | Dense slides as security blanket | Sparse slides force you to know your material |

## Customization

### Audience Framing

For a **conference presentation** (20 min):
- Emphasize identification strategy and key results
- Assume the audience knows the literature but not your specific contribution
- Front-load the "why should I care" slide

For a **job talk** (40–60 min):
- More time on data and robustness
- Include a "roadmap" slide after motivation
- The audience is evaluating you, not just the paper

For a **seminar** (60–75 min):
- Room for digressions and alternative specifications
- Include "what I tried that didn't work" if pedagogically useful
- Expect interruptions — each slide should be semi-self-contained

For a **lecture** (90 min, 40–55 slides):
- Target undergraduate students with no specialized background
- Maintain a clear narrative arc: hook → mechanism → evidence → winners/losers → implications
- **Include 3 discussion exercises** — each on a dedicated slide with a prompt and estimated time (5 min). Place at natural section breaks, using `{background-color="#b44527"}` to visually distinguish them.
- Every section ends with a "So what?" bridge slide
- Use `::: fragment :::` blocks extensively for incremental reveal
- Include full APA citations on a references slide at the end
- The slide outline step is critical: produce section titles + 1–2 learning objectives each + estimated slide counts per section BEFORE building

## Citations

- Use full APA citations on a references slide at the end of the deck
- Every in-text citation must appear in the references
- **Do not invent citations.** If you are not confident a specific paper exists (correct authors, title, year, and venue), flag it with "[VERIFY]" rather than including it as fact. Claude cannot reliably confirm whether a given journal article is real — err on the side of flagging uncertainty rather than presenting a plausible-sounding reference as confirmed.

### Code Language

Default is R with ggplot2. If the paper uses Python/matplotlib or Stata, match the paper's language. Generated figures always go in `figures/` as PNG.

## Quick Reference

| Step | Action |
|------|--------|
| **Read** | Understand source material, detect pedagogy and argument arc |
| **Plan** | Outline with assertion titles, check MB/MC balance, include Devil's Advocate |
| **Build** | Generate figures first, then write slides with fragments |
| **Compile** | Fix ALL warnings, loop until zero |
| **Verify** | Generate proof PDF via decktape, Read it page by page to catch visual errors |
| **Review** | 3-agent cycle: builder → rhetoric reviewer → graphics specialist |
| **Final** | One last compile, re-generate proof PDF, visual spot-check |
