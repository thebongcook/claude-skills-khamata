---
name: arxiv-pdf-lookup
description: Download and read an arxiv paper PDF directly. Gives access to figures, tables, and exact layout that text-extraction services may miss.
---

# Arxiv PDF Lookup

Download and read an arxiv paper PDF directly using Claude Code's native PDF reading capability. This gives access to figures, tables, and exact layout that text-extraction services may miss.

## When to Use

- User wants to read the actual PDF of an arxiv paper (not a text summary)
- User shares an arxiv URL (e.g. `arxiv.org/abs/2401.12345`)
- User mentions a paper ID (e.g. `2401.12345`)
- User asks about figures, tables, or layout-dependent content in a paper
- User asks you to explain, summarize, or analyze a research paper

## Workflow

### Step 1: Extract the paper ID

Parse the paper ID from whatever the user provides:

| Input                                      | Paper ID         |
| ------------------------------------------ | ---------------- |
| `https://arxiv.org/abs/2401.12345`         | `2401.12345`     |
| `https://arxiv.org/pdf/2401.12345`         | `2401.12345`     |
| `https://arxiv.org/pdf/2401.12345v2`       | `2401.12345v2`   |
| `2401.12345v2`                             | `2401.12345v2`   |
| `2401.12345`                               | `2401.12345`     |
| `hep-th/9905111`                           | `hep-th/9905111` |
| `https://arxiv.org/abs/hep-th/9905111`     | `hep-th/9905111` |

For old-format IDs containing `/` (e.g. `hep-th/9905111`), replace `/` with `_` in the temp filename to avoid path issues.

### Step 1.5: Validate the paper ID

Before using the paper ID in any shell command, verify it matches one of arXiv's known ID formats:

- **New format:** `YYMM.NNNNN` with optional version suffix `vN` (e.g. `2401.12345`, `2401.12345v2`)
- **Old format:** `category/NNNNNNN` with optional version suffix `vN` (e.g. `hep-th/9905111`, `math.AG/0601001v1`)

The ID must consist only of digits, dots, forward slashes, lowercase letters, hyphens, and the letter `v`. If the extracted ID does not match these patterns, inform the user that the paper ID appears invalid and **stop** — do not pass it to any shell command.

### Step 2: Download the PDF

```bash
curl -sL -o /tmp/arxiv_{SAFE_PAPER_ID}.pdf "https://arxiv.org/pdf/{PAPER_ID}"
```

Where `{SAFE_PAPER_ID}` replaces `/` with `_` (e.g. `hep-th_9905111`).

### Step 3: Verify the download

Arxiv returns an HTML error page (not a 404) for invalid paper IDs. Verify the file is actually a PDF:

```bash
file /tmp/arxiv_{SAFE_PAPER_ID}.pdf
```

If the output does NOT contain "PDF", the paper ID is invalid or the paper doesn't exist. Inform the user and clean up the file.

### Step 4: Check page count

Determine the number of pages so you can read the PDF correctly. Try these approaches in order:

```bash
# Option 1: mdls (macOS)
mdls -name kMDItemNumberOfPages /tmp/arxiv_{SAFE_PAPER_ID}.pdf

# Option 2: pdfinfo (if installed)
pdfinfo /tmp/arxiv_{SAFE_PAPER_ID}.pdf | grep Pages

# Option 3: qpdf (if installed)
qpdf --show-npages /tmp/arxiv_{SAFE_PAPER_ID}.pdf
```

If none of these work, assume the paper is longer than 10 pages and use paginated reading.

### Step 5: Read the PDF

Use the **Read tool** with appropriate `pages` parameter based on page count:

- **1-10 pages**: Read the entire file (no `pages` parameter needed)
- **11-20 pages**: Read with `pages: "1-20"`
- **21-40 pages**: Read in two batches: `pages: "1-20"` then `pages: "21-40"`
- **41-60 pages**: Read in three batches: `pages: "1-20"`, `pages: "21-40"`, `pages: "41-60"`
- **61+ pages**: Read the first 60 pages (three batches), then inform the user that the paper is long and offer to read specific page ranges on demand

**Important**: The Read tool can only handle up to 20 pages per request. For PDFs over 10 pages, you MUST provide the `pages` parameter or the read will fail.

### Step 6: Clean up

```bash
rm /tmp/arxiv_{SAFE_PAPER_ID}.pdf
```

### Step 7: Structured analysis

Present the paper in a structured format:

```
## {Paper Title}

**Authors:** {authors}
**Paper ID:** {paper_id}

### Abstract
{abstract}

### Key Contributions
- {contribution 1}
- {contribution 2}
- ...

### Methodology
{methodology overview}

### Main Results
{key findings and results}

### Limitations
{noted limitations or caveats}

### Key Takeaways
- {takeaway 1}
- {takeaway 2}
- ...
```

If the user asked a **specific question** about the paper, skip the full structured analysis and answer their question directly, citing relevant sections, figures, or tables from the PDF.

## Error Handling

- **`file` command shows HTML/ASCII instead of PDF**: The paper ID is invalid or the paper doesn't exist on arxiv. Inform the user.
- **Download fails**: Check network connectivity. Arxiv may rate-limit — wait a moment and retry once.
- **Read tool fails on large PDF**: Ensure you're using the `pages` parameter. Max 20 pages per read call.

## Notes

- No authentication required — arxiv PDFs are publicly accessible.
- This skill reads the actual PDF, so it can see figures, tables, equations, and layout that text extraction may lose.
