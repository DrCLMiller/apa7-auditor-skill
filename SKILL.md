---
name: apa7-auditor
description: >
  Perform a comprehensive APA 7 audit of an uploaded student paper (.docx).
  Produces a formatted plain-text audit report containing a correction log and,
  optionally, a reference verification report and unsubstantiated claims review.
  Use this skill whenever the user uploads a paper and asks for APA review,
  citation checking, reference verification, or formatting audit — even if they
  just say "check my paper," "fix my references," or "is this APA correct?"
  Always trigger for academic paper review tasks.

---

## MANDATORY EXECUTION RULES

**NO GENERALIZATIONS** Never provide a general review. You MUST execute the entire SKILL.md, including using `references/apa7-rules.md`, to complete the check.  
**REFRESH** Treat every uploaded .docx student paper as a NEW audit. Do not retain memory from previous papers unless explicitly asked by the user.  
**WORKFLOW** Always follow the workflow step-by-step. Do not skip or reorder steps.  
**STRUCTURED EXTRACTION FIRST** Complete the full Step 2 inventory before performing any audit judgment. Never begin flagging errors while still reading the paper.  
**OUTPUT FORMAT** Produce a plain-text (.txt) report using the format spec in Step 7. Do NOT use the docx skill, npm, or any binary format.  
**CONDITIONAL STEPS** Steps 4 and 4b are conditional — execute them ONLY if the user confirmed YES during the Step 1 interview. If the user said NO, suppress those steps and their correction log categories entirely.

---

# APA 7 Auditor

Produces a complete **APA 7 Audit Report** as a formatted plain-text (.txt) file.

## Quick Overview

**Input:** Uploaded .docx student paper  
**Output:** Formatted .txt audit report with correction log and optional sections  
**Tools needed:** `extract-text` (paper extraction), `bash` or Python (write .txt output)  
**Optional steps:** Reference Verification (Step 4, activated by Q2 = YES), Unsubstantiated Claims (Step 4b, activated by Q3 = YES)

---

## Workflow

### Step 1 — Pre-Audit Interview (MANDATORY — complete before reading the paper)

Before opening the paper, gather answers to all four questions using the `ask_user_input_v0` tool so the user can respond by clicking buttons. Do not proceed to Step 2 until you have responses to all four.

Write a brief introductory sentence (e.g., "Before I start the audit, I have a few quick questions."), then call `ask_user_input_v0` with all four questions at once:

**Q1. Instructor deviations from APA 7** *(single_select)*  
Question text: "Did your instructor give any formatting instructions that differ from standard APA 7? (e.g., no abstract, no headings, specific page limit)"  
Options: `"No deviations"` / `"Yes — I'll describe them in chat"`  
If the user selects "Yes — I'll describe them in chat", wait for their follow-up message describing the exceptions before proceeding to Step 2. Document all exceptions; they will be noted in the Disclaimer and NOT flagged as errors.

**Q2. Reference verification** *(single_select — determines whether Step 4 runs)*  
Question text: "Would you like me to verify your references? (I'll search the web to check authors, titles, and DOIs — requires extra time)"  
Options: `"Yes, verify references"` / `"No, skip verification"`

**Q3. Unsubstantiated claims check** *(single_select — determines whether Step 4b runs)*  
Question text: "Would you like me to flag factual statements that are missing an in-text citation? (requires extra time)"  
Options: `"Yes, check for unsupported claims"` / `"No, skip this check"`

**Q4. AI acknowledgment** *(single_select — informational only, no bearing on audit execution)*  
Question text: "Do you understand that I am an AI, this audit may not be perfect, and you are ultimately responsible for the accuracy of the paper you submit?"  
Options: `"Yes, I understand"` / `"Tell me more"`  
If the user selects "Tell me more", briefly explain the limitation, then re-ask Q4 before continuing.

**Record and carry forward:**
- All instructor-approved deviations from Q1 (document each one explicitly)
- Step 4 flag: Run reference verification? YES / NO (from Q2)
- Step 4b flag: Run unsubstantiated claims check? YES / NO (from Q3)
- Q4 is for the student's self-awareness only — record but do not act on it

---

### Step 2 — Structured Extraction (MANDATORY — complete before any auditing)

Extract all content from the paper:

```bash
extract-text /mnt/user-data/uploads/<filename>.docx
```

Produce the following structured inventory before proceeding to Step 3. This is your working document for all subsequent audit steps. Do not begin flagging errors until all subsections of this inventory are complete.

**2A. Title Page Elements**  
List each element present: paper title, author name, institutional affiliation, course name and number, instructor name, due date. Note the formatting of each (e.g., bold, centered, plain text).

**2B. Heading Inventory**  
List every heading in the paper in order of appearance. Record: the heading text, its apparent level (1–5), and its formatting (centered/left, bold, italic, title case/not). Flag any heading that appears to skip a level or violates level format.

**2C. In-Text Citation List**  
List every unique in-text citation found in the body text. Use the format: (Author, Year) or Author (Year). Note any variations in format (e.g., use of "&" vs. "and," "et al." usage, missing years).

**2D. Reference List**  
Copy the full text of every reference entry exactly as written in the paper. Number them sequentially (Ref 1, Ref 2, etc.) for use in Steps 3 and 5.

**2E. Claims Register** *(build only if Step 4b flag = YES)*  
Scan every sentence in the body text. Apply the heuristic filter from `references/apa7-rules.md` → UNSUBSTANTIATED CLAIMS — HEURISTIC FILTER. For each sentence that passes the filter (i.e., it appears to be a specific factual claim), record:
- The section/paragraph where it appears
- The sentence text
- Whether a citation appears immediately at the end of that sentence (YES/NO)

Do not make a final judgment at this stage — only collect candidate sentences. Step 4b will evaluate them.

**2F. Tables and Figures Inventory**  
Note whether the paper contains any tables or figures. If yes, list each one (Table 1, Figure 1, etc.) and the section or page where it appears.

---

### Step 3 — Audit

Using the structured inventory from Step 2, work through each applicable audit category. Record every issue in the correction block format below. Suppress any category with zero errors (list suppressed categories in the Clean Categories Note after the correction log).

**Correction block format:**
```
  Issue N
    Location:    [Page / Section]
    Current:     [What the paper currently says or shows]
    Correction:  [What it should be per APA 7]
    Rule:        [APA 7 rule reference]
```

**Canonical category order** (maintain this order in the output — suppress empty ones):
1. TITLE PAGE ERRORS
2. HEADING & STRUCTURE ERRORS
3. IN-TEXT CITATION ERRORS
4. UNSUBSTANTIATED CLAIMS *(include only if Step 4b flag = YES and Step 4b has run)*
5. IN-TEXT CITATION AND REFERENCE CROSS-CHECK *(populated in Step 5)*
6. REFERENCE VERIFICATION — INTEGRITY FLAGS *(include only if Step 4 flag = YES and Step 4 has run)*
7. REFERENCE FORMATTING ERRORS
8. TABLES & FIGURES ERRORS
9. OTHER APA 7 / CONTENT ERRORS

See `references/apa7-rules.md` for the full per-category rule checklist.

**Reference Formatting Audit — Field Checklist Method**  
For each reference in the Step 2D list, complete the field checklist below. Only generate a correction block for fields that fail (marked ✗). Do not narrate fields that pass.

| Field | Check Question | Rule |
|-------|---------------|------|
| Author format | Is every author in Last, F. I. format? Are mononyms handled correctly? | §9.8 |
| Author count | 20 or fewer: all listed? 21+: first 19, . . ., last? | §9.8 |
| No role labels | Is the author field free of Dr., Prof., or other titles? | §9.8 |
| Date format | Year in parentheses? Full date (Year, Month Day) for webpages? (n.d.) if no date? | §9.15–9.17 |
| Article title case | Sentence case (only first word, subtitle, proper nouns capitalized)? NOT italicized? | §9.19 |
| Book/report/webpage title | Italicized? Sentence case? | §9.25 |
| Journal name | Italicized? Title case (all major words capitalized)? | §9.25 |
| Volume number | Italicized? | §9.25 |
| Issue number | NOT italicized? Enclosed in parentheses? | §9.25 |
| Page range | Present for journal articles? En dash between pages? | §9.26 |
| Bracketed descriptor | Present for non-standard source types (e.g., [Testimony], [Dataset], [Large language model])? | §9.21 |
| Publisher / site name | Proper organization name only — no domain extensions (.com, .org, .ai), no marketing taglines? | §9.29, §9.34 |
| Ampersand in title | "and" used in title (not "&") unless ampersand appears in the original source title? | §9.19 |
| Proper nouns / acronyms | Proper nouns and acronyms capitalized correctly within sentence case titles? | §9.19 |
| DOI format | Formatted as https://doi.org/xxxxx? Not italicized? No trailing period needed? | §9.35 |
| URL cleanliness | No tracking parameters (e.g., utm_source=chatgpt.com)? No URL shorteners? No hyperlink formatting? | §9.34 |
| Alphabetical order | Reference list entries sorted alphabetically by first author last name? | §9.44 |

**Citation/Reference Cross-Check — Three Sequential Mini-Tasks**  
Run these three tasks in strict order. Treat each as a distinct, bounded operation before moving to the next.

*Task A — Compile citation list:* From Step 2C, produce a clean, deduplicated list of every author-year pair cited in the body text (e.g., Smith, 2020; Jones & Lee, 2019).

*Task B — Compile reference list:* From Step 2D, produce a clean, deduplicated list of every author-year pair in the reference list.

*Task C — Diff the two lists:*
- Any author-year pair in Task A with NO matching entry in Task B → flag as "Orphaned in-text citation — no matching reference entry."
- Any author-year pair in Task B with NO matching citation in Task A → flag as "Reference list entry with no in-text citation."
- Any author name that differs in spelling or format between Task A and Task B → flag as "Author name mismatch between citation and reference list."

Record all Task C findings under the **IN-TEXT CITATION AND REFERENCE CROSS-CHECK** category in the correction log (Step 5 will also add appendix checks to this category).

---

### Step 3b — Tables & Figures Audit

If Step 2F confirms no tables or figures are present, skip this step entirely. Add "TABLES & FIGURES ERRORS" to the Clean Categories Note.

If tables or figures are present, audit each one using `references/apa7-rules.md` → TABLES & FIGURES. Check placement and callout, number and title formatting, column/body alignment, border rules, and note format. Log every issue under TABLES & FIGURES ERRORS. If no issues are found within a present table or figure, note "No formatting errors found."

---

### Step 4 — Reference Verification *(CONDITIONAL — run only if Step 4 flag = YES)*

If Step 4 flag = NO: Skip this step entirely. Suppress the **REFERENCE VERIFICATION — INTEGRITY FLAGS** category from the correction log. In the Reference Verification Summary section of the output, write: "Reference verification was not requested for this audit."

If Step 4 flag = YES: For every reference in the Step 2D list, perform a **Trinity Check** (Author + Year + Title) and verify the DOI or URL using web search.

Use web search to:
- Confirm the source exists
- Verify the correct author name(s)
- Confirm the title matches
- Check whether DOIs resolve
- Check URLs for errors (wrong subdomains, tracking parameters such as `utm_source=chatgpt.com`, dead links)

**Flag these integrity issues prominently:**
- Wrong author (institution listed instead of individual, or vice versa)
- Wrong title (reference list title does not match the actual source title)
- AI-generated tracking parameters in URLs (e.g., `utm_source=chatgpt.com`)
- Non-resolving DOIs or broken URLs
- Ghost references (source not verifiable as real)

See `references/apa7-rules.md` → COMMON INTEGRITY FLAGS for the full flag taxonomy.

---

### Step 4b — Unsubstantiated Claims Audit *(CONDITIONAL — run only if Step 4b flag = YES)*

If Step 4b flag = NO: Skip this step entirely. Suppress the **UNSUBSTANTIATED CLAIMS** category from the correction log. No mention is needed in the correction log beyond the Clean Categories Note.

If Step 4b flag = YES: Review the Claims Register from Step 2E. For each candidate sentence flagged there:
1. Confirm whether a supporting citation appears at the end of the sentence or immediately following it.
2. If no citation is present, determine: is this sentence clearly the author's own original synthesis or evaluative judgment? If yes, do not flag. If no, flag it.

See `references/apa7-rules.md` → UNSUBSTANTIATED CLAIMS for claim type guidance and the heuristic filter criteria.

**Do not re-scan the full paper.** Only evaluate sentences already captured in the Step 2E Claims Register.

---

### Step 5 — Cross-Check Citations ↔ References

Using the Task C results from Step 3:
- Report all orphaned in-text citations under **IN-TEXT CITATION AND REFERENCE CROSS-CHECK**
- Report all uncited reference list entries under the same category
- Report all author name mismatches

**Appendix check (two-way):**
1. If the paper includes one or more appendices, verify each is explicitly called out in the body text (e.g., "see Appendix A"). An appendix with no body-text reference is a structural error — flag it.
2. If the body text references an appendix (e.g., "see Appendix B"), verify that appendix actually exists. A reference to a non-existent appendix is a structural error — flag it.
3. Verify appendices are referenced in alphabetical order throughout the body text (A before B, B before C, etc.). Any out-of-order reference is a structural error — flag it with the location of both occurrences.

Add all appendix findings to the **IN-TEXT CITATION AND REFERENCE CROSS-CHECK** category.

---

### Step 5b — Final Checks (run through before writing the report)

- [ ] Did I ask all four Q1–Q4 questions before reading the paper?
- [ ] Did I complete the full Step 2 structured extraction before auditing anything?
- [ ] Did I use the field-checklist method for every reference in Step 2D?
- [ ] Did I run the three-task cross-check (Task A → Task B → Task C) in Step 3?
- [ ] Did I run Step 4 (Reference Verification) only if Q2 = YES?
- [ ] Did I run Step 4b (Unsubstantiated Claims) only if Q3 = YES?
- [ ] Are all instructor-approved deviations from Q1 documented in the Disclaimer?
- [ ] Did I audit all tables/figures (Step 3b), or confirm their absence?
- [ ] Did I run the appendix two-way check in Step 5?
- [ ] Did I check for missing punctuation (e.g., period after a sentence ending with a parenthetical citation)?
- [ ] Did I suppress all empty categories and list them in the Clean Categories Note?
- [ ] Did I total the error count correctly across all categories?

---

### Step 6 — Write Disclaimer & Issue Summary

Write the following disclaimer verbatim (or close to it):

  This report was generated strictly for STUDENT SELF-IMPROVEMENT only. It is
  NOT meant to be used by instructors for grading purposes. This tool is based
  on a probabilistic AI model that may make errors. Ultimately, students are
  responsible for the work they submit. This tool is meant to be a supportive
  aide, NOT a final solution. Critical thinking and knowledge of APA 7
  guidelines are still required to maximize accuracy.

Below the disclaimer, include:
- `Total errors identified: X individual issues across Y categories.`
- If instructor-approved exceptions exist, add a NOTE line for each (e.g., "NOTE: Bullet-point format was confirmed as instructor-required and has NOT been flagged as an error.").
- If Q2 = NO, add: "NOTE: Reference verification (Step 4) was not requested and has been skipped."
- If Q3 = NO, add: "NOTE: Unsubstantiated claims check (Step 4b) was not requested and has been skipped."

---

### Step 7 — Generate the Plain-Text Report

Write the audit report as a formatted .txt file using Python:

```python
report = """..."""   # build the full report string per the Output Format spec below
with open("/mnt/user-data/outputs/APA7_Audit_<AuthorLastName>.txt", "w", encoding="utf-8") as f:
    f.write(report)
```

Then call `present_files` on the .txt file.

---

## Output Format

### Plain-Text Structure and ASCII Conventions

- `=` (80 chars) for major section dividers
- `-` (80 chars) for subsection or category dividers
- Category headers use the pattern: `--- CATEGORY NAME ---`
- Issue blocks are indented 2 spaces; field labels are indented 4 spaces
- Field labels are left-padded to align colons (use spaces)
- Blank line between each issue block for readability
- Line wrap at approximately 100 characters for long correction text
- Plain ASCII only — no Unicode box-drawing characters, no markdown bold/italic

### Full Report Template

```
================================================================================
           APA 7 CORRECTION LOG & REFERENCE VERIFICATION REPORT
================================================================================
Paper:      [Full paper title]
Author:     [Author name]  |  Course: [Course]  |  Instructor: [Instructor]
Audit Date: [Month Day, Year]
================================================================================


DISCLAIMER & ISSUE SUMMARY
================================================================================

  [Disclaimer text — wrap at ~90 chars]

  Total errors identified: X individual issues across Y categories.

  [NOTE lines for instructor exceptions and/or skipped optional steps, if any]


CORRECTION LOG
================================================================================

--- TITLE PAGE ERRORS ---------------------------------------------------------

  Issue 1
    Location:    [Page / Section]
    Current:     [What the paper currently shows]
    Correction:  [What it should be, and why]
    Rule:        [APA 7 §X.XX]

  Issue 2
    Location:    ...
    Current:     ...
    Correction:  ...
    Rule:        ...

--- HEADING & STRUCTURE ERRORS ------------------------------------------------

  [Issue blocks, or this category header is suppressed if no errors]

--- IN-TEXT CITATION ERRORS ---------------------------------------------------

  [Issue blocks, or suppressed]

--- UNSUBSTANTIATED CLAIMS ----------------------------------------------------
  [Include only if Step 4b flag = YES and claims were found]
  [Suppress this entire section if Step 4b flag = NO]

  Issue N
    Location:    [Section]
    Current:     "[Sentence text as written]"
    Correction:  This statement appears to be a specific factual claim and
                 requires an in-text citation. Add a supporting source or
                 reframe as the author's original analysis.
    Rule:        APA 7 §8.1

--- IN-TEXT CITATION AND REFERENCE CROSS-CHECK --------------------------------

  [Issue blocks from Task C and appendix check, or suppressed]

--- REFERENCE VERIFICATION -- INTEGRITY FLAGS ---------------------------------
  [Include only if Step 4 flag = YES and integrity issues were found]
  [Suppress this entire section if Step 4 flag = NO]

  Issue N
    Reference:   [Author (Year)]
    Flag:        [Flag type — see apa7-rules.md COMMON INTEGRITY FLAGS]
    Detail:      [Specific finding]

--- REFERENCE FORMATTING ERRORS -----------------------------------------------

  [Issue blocks from field-checklist audit, or suppressed]

--- TABLES & FIGURES ERRORS ---------------------------------------------------

  [Issue blocks, or suppressed if no tables/figures in paper]

--- OTHER APA 7 / CONTENT ERRORS ----------------------------------------------

  [Issue blocks, or suppressed]

--------------------------------------------------------------------------------
[If one or more categories had zero errors:]
Great work! The following categories were audited and no errors were found:
[comma-separated list of suppressed category names].
--------------------------------------------------------------------------------


REFERENCE VERIFICATION SUMMARY
================================================================================

[If Step 4 flag = NO:]
  Reference verification was not requested for this audit.

[If Step 4 flag = YES:]

  Phase 1 -- Metadata Completeness Audit
  --------------------------------------
  For each reference, one line:
  - [Author (Year)]: [completeness note — e.g., "Complete." or "Missing page range."]

  Phase 2 -- Trinity Check (Author / Year / Title Verification)
  -------------------------------------------------------------
  Reference                   | Author Match | Year Match | Title Match | Verdict
  [Author (Year)]             | YES/NO       | YES/NO     | YES/NO      | PASS / FLAG
  ...


================================================================================
                             END OF AUDIT REPORT
================================================================================
```

### Formatting Notes
- Use consistent spacing so all colon-delimited field labels in issue blocks align
- If a Correction field runs long, wrap it and indent continuation lines to align with the text start
- The Reference Verification Phase 2 table may use plain pipe characters for columns
- Do not include a blank "Academic Quality Summary" section — that section has been removed from this skill

---

## Reference Rules Cheatsheet

See `references/apa7-rules.md` for the complete per-category audit checklist and the reference field-checklist table.

Key rules to always check:
- **In-text block quotes**: Required for direct quotations of 40 or more words; indented 0.5 in., no quotation marks, citation after closing punctuation (§8.27)
- **Author format**: Last, F. I. — never First Last; no role titles (§9.8)
- **Individual vs. institution**: If an individual author is identifiable, cite the individual, not the institution (§8.21)
- **DOIs**: Formatted as `https://doi.org/xxxxx`, not italicized, no trailing period (§9.35)
- **URLs**: No tracking parameters; no domain extensions in publisher name; use production URLs (§9.34)
- **Webpage titles**: Italicized; sentence case (§9.25)
- **Journal name + volume**: Italicized; issue number NOT italicized (§9.25)
- **Hanging indent**: All reference entries (§2.12)
- **Level 1 headings**: Centered, bold, title case (§2.27)
- **Title page**: Title bold; all other elements plain text; no running head for student papers (§2.4, §2.18)
- **In-text citations**: Last name only; `&` inside parentheses, "and" outside (§8.17)
- **Page numbers**: Top-right corner, every page, beginning page 1 on the title page (§2.18)

---

## Attribution

This skill was developed by **Dr. Cari L. Miller**, College of Business and Technology, Wilmington University, Wilmington, DE.  
LinkedIn: https://www.linkedin.com/in/cari-miller/

Licensed under the MIT License. Attribution must be retained in all copies or substantial portions of this skill.

---

metadata:
  author: "Dr. Cari L. Miller"
  institution: "College of Business and Technology, Wilmington University, Wilmington, DE"
  linkedin: "https://www.linkedin.com/in/cari-miller/"
  license: "MIT"
  repository: "https://github.com/DrCLMiller/apa7-auditor-skill"
  last_updated: "April 29, 2026"
  changelog: "v1.1 — Step 1 now uses ask_user_input_v0 for clickable yes/no answers"
