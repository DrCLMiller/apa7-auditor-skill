---
name: apa7-auditor
description: >
  Perform a comprehensive APA 7 audit of an uploaded student paper (.docx).
  Produces a formatted Word document correction log and reference verification
  report. Use this skill whenever the user uploads a paper and asks for APA
  review, citation checking, reference verification, or formatting audit —
  even if they just say "check my paper," "fix my references," or "is this
  APA correct?" Always trigger for academic paper review tasks.
metadata:
  author: "Dr. Cari L. Miller"
  institution: "College of Business and Technology, Wilmington University, Wilmington, DE"
  linkedin: "https://www.linkedin.com/in/cari-miller/"
  license: "MIT"
  repository: "https://github.com/DrCLMiller/apa7-auditor-skill"
---

# APA 7 Auditor

Produces a complete **APA 7 Correction Log & Reference Verification Report** as a .docx file.

## Quick Overview

**Input:** Uploaded .docx student paper  
**Output:** .docx report with correction log table, reference verification, and academic quality summary  
**Tools needed:** `extract-text`, web search (for reference verification), `docx` npm library

---

## Workflow

### Step 0 — Pre-Audit Interview (MANDATORY — do before reading the paper)

Before opening the paper, ask the user:

1. **Instructor deviations from APA 7** — "Did your instructor give any formatting instructions that differ from standard APA 7? For example: required bullet points instead of paragraphs, a specific heading structure, page limits, or anything else?" Document all exceptions. These should be noted in the Disclaimer section and NOT flagged as errors in the correction log.

Do not proceed to Step 1 until you have this information.

### Step 1 — Read the Paper

```bash
extract-text /mnt/user-data/uploads/<filename>.docx
```

Extract all content. Note:
- Title page elements (title, author, course, date, institution)
- All headings (and their apparent levels)
- All in-text citations — format: `(Author, Year)` or `Author (Year)`
- All bullet points and body claims
- The full reference list (every entry)

### Step 2 — Audit (Six Categories)

Work through all six categories. Collect every issue as a row for the correction log table.

**Table columns:** `Page / Section` | `Current Version` | `Correction (APA 7)` | `Rule Violated`

Use category header rows (bold, spanning) to divide the table:
- `TITLE PAGE ERRORS`
- `HEADING & STRUCTURE ERRORS`
- `IN-TEXT CITATION ERRORS`
- `UNSUBSTANTIATED CLAIMS`
- `IN-TEXT CITATION AND REFERENCE CROSS-CHECK`
- `REFERENCE VERIFICATION — INTEGRITY FLAGS`
- `REFERENCE FORMATTING ERRORS`
- `TABLES & FIGURES ERRORS`
- `OTHER APA 7 / CONTENT ERRORS`

See `references/apa7-rules.md` for the full rule checklist per category.

### Step 2b — Tables & Figures Audit

If the paper contains no tables or figures, skip this step and note "No tables or figures present" in the correction log under the `TABLES & FIGURES ERRORS` category header.

If tables or figures are present, audit each one using `references/apa7-rules.md` → TABLES & FIGURES section. Check:

**Placement & Callout (both tables and figures):**
- Is the table/figure called out in body text before it appears? (e.g., "Table 1 presents…" or "(see Figure 2)")
- Is it placed after the callout — at bottom of page, top of next page, or on its own page?
- Blank line between body text and table/figure, and between table/figure and next body text?
- Not placed in the middle of a page?

**Tables:** Number (bold, left-aligned) → title (italic, left-aligned, title case, line directly below number) → column headers (centered) → body text/number alignment → same font as paper → acceptable line spacing (single, 1.5, or double) → no vertical borders → note format ("*Note.*" italic, then regular text, left-aligned, double-spaced).

**Figures:** Number (bold, left-aligned) → title (italic, left-aligned, title case) → image left-aligned → font inside image (sans-serif, 8–14pt) → note format if present.

**Numbering:** Tables numbered independently from figures; both sequentially in order of first mention.

Log every issue under the `TABLES & FIGURES ERRORS` category header in the correction log. If no issues are found, add a single row noting "No errors found."

### Step 3 — Reference Verification

For every reference in the paper, perform a **Trinity Check** (Author + Year + Title) and verify the DOI or URL.

Use web search to:
- Confirm the article/source exists
- Verify the correct author name(s)
- Confirm the title matches
- Check whether DOIs resolve
- Check URLs for errors (wrong subdomains, tracking parameters like `utm_source=chatgpt.com`, dead links)

**Flag these integrity issues prominently:**
- Wrong author (institution listed instead of individual, or vice versa)
- Wrong title (title in reference list does not match the actual source title)
- AI-generated tracking parameters in URLs (e.g., `utm_source=chatgpt.com`)
- Non-resolving DOIs or broken URLs
- Ghost references (reference not verifiable as real)

### Step 3b — Unsubstantiated Claims Audit

Go through the body text bullet by bullet (or paragraph by paragraph). For every specific, non-obvious factual claim, check whether it has an in-text citation. Flag claims that lack citations and are not clearly the author's own original analysis.

**Claim types that REQUIRE a citation:**
- Specific statistics or data points ("X% of companies use...")
- Causal or mechanistic claims about how technology works ("AI systems trained on neurotypical norms disadvantage...")
- Claims about research findings or studies
- Specific legal or regulatory claims
- Claims about systematic patterns or effects at scale

**Claim types that may NOT require a citation (original analysis):**
- Author's own synthesis or conclusions drawn from cited evidence
- Explicit evaluative statements framed as the author's view
- "Perceived risks" bullets that are clearly author analysis rather than sourced claims

When in doubt, flag it. Note these in a dedicated subsection of the correction log: "UNSUBSTANTIATED CLAIMS."

### Step 4 — Cross-Check Citations ↔ References

- Every in-text citation must have a matching reference list entry
- Every reference list entry must have at least one in-text citation (flag at 1+)
- Author names in citations must match reference list exactly
- Flag mismatches in the `IN-TEXT CITATION AND REFERENCE CROSS-CHECK` section
- **Appendix check (two-way):**
  1. If the paper includes one or more appendices, verify each appendix is explicitly referenced in the body text (e.g., "see Appendix A"). An appendix with no corresponding in-text reference is a structural error — flag it.
  2. If the body text references an appendix (e.g., "see Appendix B"), verify that a corresponding appendix actually exists in the appendices section. A reference to a non-existent appendix is a structural error — flag it.
  3. Verify that appendices are referenced in alphabetical order throughout the body text — Appendix A must appear before Appendix B, which must appear before Appendix C, etc. Any out-of-order reference is a structural error — flag it with the location of both the earlier and later references.

### Step 4b — Final Checks (run through before writing the report)

- [ ] Did I ask about instructor deviations before auditing? Are all exceptions documented in the Disclaimer section?
- [ ] Did I check every body claim for citation support (Step 3b)?
- [ ] Did I verify every reference via web search (Trinity Check)?
- [ ] Did I cross-check all citations ↔ references bidirectionally?
- [ ] Did I check whether appendices (if any) are cited in the body, and that all cited appendices actually exist?
- [ ] Did I verify appendices are referenced in alphabetical order in the body text?
- [ ] Did I check for factual errors in content claims (e.g., pending legislation described as enacted)?
- [ ] Did I check all reference formatting (dates, titles, authors, URLs, italics, sentence case)?
- [ ] Did I audit all tables and figures (Step 2b) — or note their absence — and log findings under `TABLES & FIGURES ERRORS`?

### Step 5 — Build Academic Quality Summary

**Source Classification Table:** Classify each reference as:
- ✓ Peer-Reviewed Journal
- Gray Literature (specify: Government Document, Professional Association, Media, Legislative, Testimony, etc.)

**Source Ratio:**
- Count peer-reviewed vs. gray literature
- Express as a ratio and percentage

### Step 6 — Write Disclaimer & Issue Summary

Write the following disclaimer verbatim (or close to it) at the top of this section:

> *This report was generated strictly for student self-improvement only. It is NOT meant to be used by instructors for grading purposes. This tool is based on a probabilistic AI model that may make errors. Ultimately, students are responsible for the work they submit. This tool is meant to be a supportive aide, NOT a final solution. Critical thinking and knowledge of APA 7 guidelines are still required to maximize accuracy.*

Below the disclaimer, include:
- Bold total: `Total errors identified: X individual issues across Y categories.`
- If any instructor-approved exceptions were noted in the pre-audit interview, add a NOTE paragraph below (e.g., "NOTE: Bullet-point format was confirmed as instructor-required and has NOT been flagged").

### Step 7 — Generate the .docx Report

Use the `docx` npm library (see docx skill for setup). See output format spec below.

---

## Output Format

### Document Structure

1. **Header block** (no heading style — just formatted paragraphs):
   - Title: `APA 7 Correction Log & Reference Verification Report` — bold, centered
   - Italic line: `Paper: [paper title]`
   - Author | Course line
   - `Audit Date: [today's date]`

2. **Disclaimer & Issue Summary** (Heading 1)
   - Italicized disclaimer paragraph (see Step 6 for wording)
   - Bold total: `Total errors identified: X individual issues across Y categories.`
   - If any instructor-approved exceptions, add a NOTE paragraph below.

3. **Correction Log** (Heading 1)
   - Single large table with category header rows spanning all columns
   - Column headers: **Page / Section** | **Current Version** | **Correction (APA 7)** | **Rule Violated**
   - Category headers: bold text in merged/spanned row (or use shaded row)
   - Categories (in order): `TITLE PAGE ERRORS` → `HEADING & STRUCTURE ERRORS` → `IN-TEXT CITATION ERRORS` → `UNSUBSTANTIATED CLAIMS` → `IN-TEXT CITATION AND REFERENCE CROSS-CHECK` → `REFERENCE VERIFICATION — INTEGRITY FLAGS` → `REFERENCE FORMATTING ERRORS` → `TABLES & FIGURES ERRORS` → `OTHER APA 7 / CONTENT ERRORS`
   - Each issue = one row

4. **Reference Verification Summary** (Heading 1)
   - **Phase 1 — Metadata Completeness Audit** (Heading 2): bullet list, one entry per reference
   - **Phase 2 — Metadata Entity Verification (Trinity Check)** (Heading 2): table with columns: Reference | Author Match | Year Match | Title Match | Verdict

5. **Academic Quality Summary** (Heading 1)
   - **Source Classification** (Heading 2): table (Reference | Source | Classification)
   - **Source Ratio** (Heading 2): prose

### Formatting Rules
- Font: Times New Roman 12pt throughout
- Page size: US Letter (12240 × 15840 DXA), 1-inch margins (1440 DXA)
- Line spacing: 1.15 outside tables (`line: 276, lineRule: "auto"` in docx-js)
- Table header rows: set `tableHeader: true` on all header rows so they repeat across page breaks
- Table borders: light gray (`CCCCCC`), single, size 1
- Header row shading: light blue (`D5E8F0`), `ShadingType.CLEAR`
- Category header rows in correction log: light gray shading (`F2F2F2`), bold text
- Column widths for correction log (DXA, total = 9360):
  - Page/Section: 1200
  - Current Version: 2520
  - Correction: 3240
  - Rule Violated: 2400

---

## Reference Rules Cheatsheet

See `references/apa7-rules.md` for the complete per-category audit checklist.

Key rules to always check:
- **In-text block quotes**: Required for direct quotations of 40 or more words; indented 0.5 in., no quotation marks, citation after closing punctuation (§8.27)
- **Author format**: Last, F. I. — never First Last (§9.8)
- **Individual vs. institution**: If individual author is identifiable, use them, not the institution (§8.21)
- **DOIs**: Formatted as `https://doi.org/xxxxx`, not italicized (§9.35)
- **URLs**: No tracking parameters; must resolve; use production URLs (§9.34)
- **Webpage titles**: Italicized; sentence case (§9.25)
- **Journal name + volume**: Italicized; issue number NOT italicized (§9.25)
- **Hanging indent**: All reference entries (§2.12)
- **Level 1 headings**: Centered, bold, title case (§2.27)
- **Title page**: Title bold; no running head for student papers unless required (§2.3, §2.18)
- **In-text citations**: Use last name only; `&` inside parens, "and" outside (§8.17)
- **Page numbers**: Top-right corner, every page (§2.18)

---

## Attribution

This skill was developed by **Dr. Cari L. Miller**, College of Business and Technology, Wilmington University, Wilmington, DE.  
LinkedIn: https://www.linkedin.com/in/cari-miller/

Licensed under the MIT License. Attribution must be retained in all copies or substantial portions of this skill.
