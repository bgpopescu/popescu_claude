---
name: read-paper
description: "Deeply read academic PDFs. Splits into 4-page chunks, reads in small batches, produces structured notes. Use when asked to read, review, or summarize a paper."
disable-model-invocation: true
allowed-tools: Bash(python*), Bash(pip*), Bash(curl*), Bash(wget*), Bash(mkdir*), Bash(ls*), Read, Write, Edit, Grep, Glob, WebSearch, WebFetch, Agent
argument-hint: [pdf-path-or-search-query]
---

# Read-Paper: Deep Reading of Academic PDFs

**CRITICAL RULE: Never read a PDF directly — not the original, not the splits.** This skill extracts text to .txt files and reads those. PDFs are 10–50x larger in context than plain text and will crash the session with an unrecoverable "Request too large" error. Always read the .txt files produced by the split step.

## When This Skill Is Invoked

The user wants you to read, review, or summarize an academic paper. The input is either:
- A file path to a local PDF (e.g., `./articles/smith_2024.pdf`)
- A search query or paper title (e.g., `"Gentzkow Shapiro Sinkinson 2014 competition newspapers"`)

**Important:** You cannot search for a paper you don't know exists. The user MUST provide either a file path or a specific search query — an author name, a title, keywords, a year, or some combination that identifies the paper. If the user invokes this skill without specifying what paper to read, ask them. Do not guess.

## Step 1: Acquire the PDF

**If a local file path is provided:**
- Verify the file exists
- If the file is NOT already inside `./articles/`, copy it there (do not move — preserve the original location)
- Proceed to Step 2

**If a search query or paper title is provided:**
1. Use WebSearch to find the paper
2. Use WebFetch or Bash (curl/wget) to download the PDF
3. Save it to `./articles/` in the project directory (create the directory if needed)
4. If the PDF is paywalled, ask the user to provide a local copy
5. Proceed to Step 2

**CRITICAL: Always preserve the original PDF.** The downloaded or provided PDF in `./articles/` must NEVER be deleted, moved, or overwritten at any point in this workflow. The split files are derivatives — the original is the permanent artifact. Do not clean up, do not remove, do not tidy. The original stays.

## Step 2: Check for Existing Extract or Splits

Before splitting, check whether this paper has already been processed.

**Check 1: Final extract.** Look for `<basename>_text.md` in the same folder as the PDF (e.g., `articles/smith_2024_text.md`).

If found, ask:
> "An extract from a previous deep-read exists (`<basename>_text.md`). Use it for this request, or re-read the PDF from scratch?"
- **Use extract**: read the `_text.md` file and use it as the source notes — skip Steps 3 and 4 entirely
- **Re-read**: proceed to Step 3

**Check 2: Existing splits.** If no `_text.md` exists, check whether `articles/articles_build/split_<basename>/` already contains `.txt` files.

If found, ask:
> "Splits already exist for `<basename>` (N chunks). Reuse existing splits, or re-split from scratch?"
- **Reuse**: skip Step 3, proceed to Step 4 using the existing `.txt` files
- **Re-split**: delete the existing split folder, proceed to Step 3

If neither extract nor splits exist, proceed to Step 3.

## Step 3: Split the PDF

Create the splits in `articles/articles_build/split_<basename>/` and run the splitting script:

```python
from pypdf import PdfReader, PdfWriter
import os, sys

def split_pdf(input_path, pages_per_chunk=4):
    folder_path = os.path.dirname(os.path.abspath(input_path))
    foldername = os.path.basename(folder_path)
    pdf_basename = os.path.splitext(os.path.basename(input_path))[0]
    build_dir = os.path.join(folder_path, foldername + '_build')
    output_dir = os.path.join(build_dir, 'split_' + pdf_basename)
    os.makedirs(output_dir, exist_ok=True)

    reader = PdfReader(input_path)
    total = len(reader.pages)

    for start in range(0, total, pages_per_chunk):
        end = min(start + pages_per_chunk, total)

        # Write PDF split (kept as backup for visual inspection)
        writer = PdfWriter()
        for i in range(start, end):
            writer.add_page(reader.pages[i])
        pdf_name = f"{pdf_basename}_pp{start+1}-{end}.pdf"
        pdf_path = os.path.join(output_dir, pdf_name)
        with open(pdf_path, "wb") as f:
            writer.write(f)

        # Extract text to .txt (THIS is what you read in Step 4)
        text = ""
        for i in range(start, end):
            page_text = reader.pages[i].extract_text()
            if page_text:
                text += f"--- Page {i+1} ---\n{page_text}\n\n"
        txt_name = f"{pdf_basename}_pp{start+1}-{end}.txt"
        txt_path = os.path.join(output_dir, txt_name)
        with open(txt_path, "w", encoding="utf-8") as f:
            f.write(text)

    print(f"Split {total} pages into {-(-total // pages_per_chunk)} chunks in {output_dir}")
    print(f"Created both .pdf and .txt files. READ THE .txt FILES, not the .pdf files.")
```

**Directory convention:**
```
articles/
├── smith_2024.pdf                    # original PDF — NEVER DELETE THIS
├── smith_2024_text.md                # final structured extract (created after deep-read)
└── articles_build/                   # build directory — keeps splits separate from sources
    └── split_smith_2024/             # split subdirectory
        ├── smith_2024_pp1-4.pdf      # PDF backup (do NOT read these)
        ├── smith_2024_pp1-4.txt      # extracted text (READ THESE)
        ├── smith_2024_pp5-8.pdf
        ├── smith_2024_pp5-8.txt
        ├── notes.md                  # working notes (updated incrementally)
        └── ...
```

The build directory (`articles_build/`) keeps split artifacts separate from source PDFs and finished extracts. Multiple PDFs in the same folder share one build directory, each with its own `split_<basename>/` subdirectory inside it.

The original PDF remains in `articles/` permanently.

**Scanned PDF check:** After splitting, read the first .txt file. If the text is mostly empty or garbled, the PDF is likely scanned images. Tell the user: "This PDF appears to be scanned. Text extraction returned garbled output. Consider running OCR on the PDF first, then re-run /read-paper on the OCR'd version."

If pypdf is not installed, install it: `pip install pypdf --break-system-packages`

**If splits already exist but .txt files do not:** Re-run the split script. The .txt files are essential — they are what you read in Step 4. Never read the .pdf splits directly.

## Step 4: Read the .txt Files (NEVER the .pdf splits)

**CRITICAL: Always read the .txt files, never the .pdf split files.** PDF files are 10–50x larger than their text equivalents and will crash the session. The .pdf splits exist only as backup for visual inspection if text extraction was poor.

Read **up to 5 .txt split files at a time**. After reading, update `notes.md` with structured extraction, then tell the user:

> "Read splits [X–Y] and updated notes. [N] splits remaining. Continue?"

Wait for the user to confirm before reading the next batch.

**If you find yourself about to call Read on a .pdf file in the split directory — STOP. Read the .txt version instead.**

**The correct pattern — the ONLY acceptable pattern:**

✅ Read 5 splits → Update notes.md → "Read splits 1–5 and updated notes. 15 splits remaining. Continue?" → RESPONSE ENDS HERE

The user's original task (e.g. "verify all claims," "read the whole paper," "review everything") does NOT change this limit. You accomplish the full task across multiple responses, 5 splits at a time, with the user saying "continue" between each.

## Step 5: Structured Extraction

As you read, collect information along these dimensions and write them into `notes.md`:

1. **Research question** — What is the paper asking and why does it matter?
2. **Audience** — Which sub-community of researchers cares about this?
3. **Method** — How do they answer the question? What is the identification strategy?
4. **Data** — What data do they use? Where precisely did they find it? What is the unit of observation? Sample size? Time period?
5. **Statistical methods** — What econometric or statistical techniques do they use? What are the key specifications?
6. **Findings** — What are the main results? Key coefficient estimates and standard errors?
7. **Contributions** — What is learned from this exercise that we didn't know before?
8. **Replication feasibility** — Is the data publicly available? Is there a replication archive? A data appendix? URLs for the underlying data?

These questions extract what a researcher needs to **build on or replicate** the work — a structured extraction more detailed and specific than a typical summary.

## The Notes File

The working notes file is `notes.md` in the split subdirectory:

```
articles/articles_build/split_smith_2024/notes.md
```

This file is **updated incrementally** after each batch. Use this template:

```markdown
# Notes: [Paper Title]
**Authors:** ...  |  **Year:** ...  |  **Venue:** ...

## Research Question
## Audience
## Method & Identification
## Data
## Statistical Methods
## Findings
## Contributions
## Replication Feasibility
```

After each batch, update whichever dimensions have new information — do not rewrite from scratch.

By the time all splits are read, the notes should contain specific data sources, variable names, equation references, sample sizes, coefficient estimates, and standard errors. Not a summary — a structured extraction.

**After all batches are complete**, write the final notes to `<basename>_text.md` in the same folder as the source PDF:

```
articles/smith_2024_text.md
```

Then notify the user:
> "Extract saved to `smith_2024_text.md` alongside the source PDF. Future requests on this paper can reuse it without re-reading."

Both files are kept — `notes.md` is the working copy, `_text.md` is the persistent, reusable artifact. Never delete either.

## Agent Isolation Protocol

**When read-paper is invoked by another skill or workflow** (any process that continues working after the PDF has been read), the reading MUST run inside a subagent to prevent context accumulation in the parent conversation.

**Why:** Even though this skill reads `.txt` files (not PDF images), a long reading session still accumulates significant context. When chained from a literature review or multi-paper workflow, this can degrade the parent conversation's reliability. Subagent isolation keeps the parent context clean.

**Pattern:** The parent skill handles acquisition (Step 1) and splitting (Step 3) in its own context. Then it launches an Agent to perform all the reading:

```
Read .txt split files and produce structured extraction notes.

Split directory: <split_dir>
Files (read in this order, 5 at a time): <file_list>
Notes output: <notes_path>
Text output: <text_path>

Process:
1. Read 5 .txt files at a time using the Read tool
2. After each batch, update the notes file with extracted content
3. Extract: research question, audience, method, data, statistical methods,
   findings, contributions, replication feasibility
4. Write the final structured extraction to the text output path

Report when done: pages read, one-sentence content summary.
```

After the agent returns, the parent reads the output files (plain markdown) and continues its workflow.

**Standalone invocations** (user calls `/read-paper` directly) use the interactive pause-and-confirm protocol in the main conversation.

## When NOT to Split

- Papers shorter than ~15 pages: extract text with the same script but skip batching — read the single `.txt` output in one pass
- Policy briefs or non-technical documents: a rough summary is fine
- Triage only: read just the first split (pages 1-4) for abstract and introduction

## Quick Reference

| Step | Action |
|------|--------|
| **Acquire** | Download to `./articles/` or use existing local file |
| **Check** | Look for existing `_text.md` extract or existing `.txt` splits — offer to reuse |
| **Split** | 4-page chunks → .pdf + .txt files into `./articles/articles_build/split_<name>/` |
| **Read** | Read the .txt files (NEVER .pdf splits), up to 5 at a time |
| **Write** | Update `notes.md` with structured extraction |
| **Persist** | Save final extraction to `<basename>_text.md` alongside the source PDF |
| **Confirm** | Ask user before continuing to next batch |

For detailed explanation of why this method works, see [methodology.md](methodology.md).
