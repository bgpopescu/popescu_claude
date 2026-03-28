---
name: search-book
description: Search large PDFs (books, reports, dissertations) for specific information. Extracts full text, searches by keyword and semantic context, returns page numbers with surrounding passages, then reads only the relevant pages carefully.
argument-hint: "[pdf-path] [search-query]"
allowed-tools: Bash(python*), Bash(pip*), Read, Write, Edit, Grep
---

# Search-Book: Targeted Retrieval from Large PDFs

Find specific information in a large PDF without reading the whole thing. Extract text, search, return page numbers with context, then read only the relevant pages.

**This is NOT split-pdf.** Split-pdf reads sequentially for comprehensive notes. Search-book finds a needle in a haystack — where in this 600-page book does the author discuss Habsburg land tenure, or where is the regression table for regional outcomes?

## Input

`$ARGUMENTS` — a PDF file path and a search query. Examples:
- `/search-book books/acemoglu_why_nations_fail.pdf "extractive institutions in Congo"`
- `/search-book books/scott_seeing_like_a_state.pdf "data on agricultural yields"`
- `/search-book books/piketty_capital.pdf "Table 2.1 wealth-to-income ratio"`

If the user gives only a PDF, ask what they're looking for. If the user gives only a query, ask which book.

## Step 1: Extract Full Text

```python
from PyPDF2 import PdfReader
import os, json

def extract_book_text(pdf_path, output_dir):
    os.makedirs(output_dir, exist_ok=True)
    reader = PdfReader(pdf_path)
    pages = []
    for i, page in enumerate(reader.pages):
        text = page.extract_text() or ""
        pages.append({"page": i + 1, "text": text})

    # Save full index for reuse
    index_path = os.path.join(output_dir, "page_index.json")
    with open(index_path, "w") as f:
        json.dump(pages, f)

    print(f"Extracted {len(pages)} pages to {index_path}")
    return pages
```

Save the extracted text to `books/index_<name>/page_index.json`. **Check if this index already exists before re-extracting** — for a 600-page book, extraction takes time and the index is reusable across searches.

Install PyPDF2 if needed: `pip install PyPDF2 --break-system-packages`

## Step 2: Search

Run three search strategies and combine results:

### 2a. Keyword search
Search for exact terms and close variants from the query. Return every page that contains a match, with the matching line and ±3 lines of context.

### 2b. Chapter/section detection
Look for table of contents pages, chapter headings, and section headers. Map the book's structure so you can report not just "page 247" but "Chapter 8, Section 3 — Land Reform." Detect chapters by patterns like:
- "Chapter N" or "CHAPTER N"
- Roman numeral headings
- All-caps lines followed by body text
- Page numbers that follow a TOC listing

### 2c. Table/figure search
If the query mentions data, tables, figures, or specific numbers, search for:
- "Table" or "Figure" followed by a number
- Column headers or variable names
- Numeric patterns matching what the user described

## Step 3: Rank and Report

Present results ranked by relevance:

```markdown
## Search Results: "[query]" in [book title]

### Book Structure
[Chapter list with page ranges, if detected]

### Matches Found: N pages

#### Match 1 — Page 247 (Chapter 8: Land Reform)
> [3-5 lines of context around the match]
**Why this matches:** [brief explanation]

#### Match 2 — Page 312 (Chapter 10: Regional Outcomes)
> [3-5 lines of context around the match]
**Why this matches:** [brief explanation]

...

### Suggested pages to read carefully: [list]
```

**Do NOT read the full pages yet.** Present the search results first and let the user decide which pages to examine in detail.

## Step 4: Deep Read (on user request)

When the user says "read page 247" or "show me more from Chapter 8":

1. Read the specific pages from the PDF (use PyPDF2 to extract just those pages)
2. Present the full text of those pages
3. If the user asked a question, answer it using only what those pages contain
4. Note the exact page numbers for citation

If the content spans multiple pages, read the full passage (don't stop mid-paragraph at a page boundary).

## Handling Scanned PDFs

If text extraction returns mostly empty strings or garbled characters, the PDF is likely scanned images. In that case:

1. Tell the user: "This PDF appears to be scanned. Text extraction returned garbled output. I can try OCR, but it will be slower and less accurate."
2. If the user agrees, use Tesseract:
   ```bash
   pip install pytesseract pdf2image --break-system-packages
   # Also needs: apt-get install tesseract-ocr (may not be available in sandbox)
   ```
3. If Tesseract isn't available, suggest the user run OCR locally or use an online tool first, then re-run search-book on the OCR'd PDF.

## Repeated Searches

The page index (`books/index_<name>/page_index.json`) persists between searches. If the user searches the same book again, load the existing index instead of re-extracting. This makes follow-up searches fast.

## Principles

- **Search first, read second.** Never read a 600-page book sequentially to find one passage.
- **Report page numbers always.** The user needs these for citations.
- **Show context, not just matches.** A keyword hit without surrounding text is useless.
- **Let the user decide what to read.** Present search results, then wait. Don't auto-read 50 pages.
- **Reuse the index.** Extraction is expensive. Don't redo it.
- **Be honest about OCR limits.** Scanned PDFs produce noisy text. Say so.
