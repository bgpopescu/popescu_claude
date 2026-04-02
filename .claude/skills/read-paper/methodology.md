# Why PDF Splitting Works: The Methodology

This document explains the reasoning behind the read-paper skill. It is reference material for humans — the SKILL.md file contains the actual instructions Claude follows.

---

## The Problem

Claude Code can read PDFs, and modern models have large context windows (1M tokens for Opus 4.6). In principle, a 40-page academic paper should fit comfortably. In practice, it doesn't work well. The core problem is not context size — it's comprehension quality.

**Problem 1: Shallow, unreliable reading.** This is the primary motivation. Even when a PDF fits in context, comprehension degrades badly for long documents. The model's attention over many tokens from a single dense document is not uniform — it attends more carefully to the beginning and end, and less carefully to the middle. The result is that Claude "skims" rather than "reads." It catches the abstract and introduction, gets fuzzy on the methodology, and often misses or fabricates details from the results and appendix. It truncates content silently, hallucinates details it didn't actually parse, or produces shallow summaries that miss critical methodological details.

**Problem 2: Session-breaking "prompt too long" errors.** A secondary concern, less common with larger context windows but still possible. PDF rendering into tokens is expensive — PDFs contain fonts, vector graphics, embedded images, multi-column layouts, mathematical notation, tables, and footnotes. A long PDF combined with conversation context can exceed input limits, breaking the session with no recovery.

These are related but distinct problems. The first is a soft degradation — the session continues but the output is unreliable. The second is a hard constraint — the session dies. Splitting addresses both, but the attention-quality argument is the one that holds regardless of context window size.

## Why Batched Reading Works

Reading 3 splits at a time (~12 pages) does several things:

1. **Forces the model to focus.** With only 12 pages of content, Claude cannot skim. It has to engage with the material at a granular level — the specific equations, the exact data sources, the precise variable definitions.

2. **Creates natural checkpoints.** After each batch, the model writes down what it has learned so far. This means its understanding is externalized into a markdown file. If it makes an error in batch 1, you can catch it before batch 2 builds on it.

3. **Accumulates rather than summarizes.** When you ask Claude to read a full paper at once, it produces a summary — a lossy compression. When you ask it to read in batches and update running notes, it accumulates detail. The final notes are richer than any one-shot summary could be.

4. **Controls for the "front-loading" problem.** Claude's attention is not uniform over a long document. It attends more carefully to the beginning and end. By splitting the paper, every section gets to be "the beginning" of some chunk.

## Why 4-Page Chunks

Four pages is a sweet spot:
- Small enough that Claude attends carefully to every detail
- Large enough that logical sections (a methodology subsection, a results table with discussion) stay together
- Produces a manageable number of chunks (a 40-page paper = 10 chunks = 4 rounds of reading)

## Why the Pause-and-Confirm Protocol

The human pause between batches serves multiple purposes:
- **Review intermediate output** — catch errors before they compound
- **Redirect the reading** — ask follow-up questions, skip sections, or change focus
- **Prevent context drift** — Claude doesn't lose track of where it is in a long session
- **Control pacing** — some papers require more careful reading in specific sections

## Limitations

- **It is slow.** A 37-page paper split into 10 chunks, read 3 at a time, requires 4 rounds. Each round involves the user confirming "yes, continue." This is a 10-15 minute process rather than a 1-minute process.
- **Notes can become repetitive** if the paper revisits themes. Some manual editing of the final notes may be useful.
- **Assumes the paper is worth reading carefully.** For triage — quickly deciding whether a paper is relevant — reading just the first split (pages 1-4, which usually contains the abstract and introduction) is sufficient.
