# APA 7 Auditor Skill for Claude

This skill was designed for higher-ed students. It was specifically developed for Claude.ai to:
- review a student's paper, 
- produce an audit report based on APA 7 criteria, and
- verify references for fabrications.

---

## What's On the APA 7 Audit Report

Upload a student paper (.docx) to Claude and this skill will produce an APA7 review of the paper containing:

- **Correction Log** — a categorized table of every APA 7 error found, with the current version, the corrected version, and the rule violated
- **Reference Verification Summary** — a Trinity Check (Author + Year + Title) for every reference, plus DOI/URL validation
- **Academic Quality Summary** — source classification table (peer-reviewed vs. gray literature) with ratio analysis
- **Disclaimer & Issue Summary** — total error count across all categories, with a clear statement that the report is for student self-improvement only

### Error Categories Checked

- Title page errors
- Heading and structure errors
- Body text formatting (spacing, margins, alingment, page numbers)
- In-text citation errors
- Unsubstantiated claims (specific factual claims missing citations)
- In-text citation ↔ reference list cross-check
- Reference integrity flags (wrong authors, wrong titles, AI-generated tracking URLs, ghost references)
- Reference formatting errors
- Appendix cross-check (cited but missing, or present but uncited)

### Planed Updates

- Table errors
- Figure errors

### Not Checked
- Common grammatical, punctuation, and spelling errors
- Proper noun capitalization and abbreviation usage 
- Bias-free language
- Plagiarism

---

## How to Use This Skill

### Requirements
- Access to Claude with computer use / skills support
- A Claude environment that supports skill files (e.g., Claude with a mounted `/mnt/skills/` directory)

### Installation

1. Download or clone this repository
2. Copy the files into your Claude skills directory:

```
/mnt/skills/user/apa7-auditor/SKILL.md
/mnt/skills/user/apa7-auditor/references/apa7-rules.md
```

3. Upload a student `.docx` paper to Claude and say something like:
   - *"Please audit this paper for APA 7"*
   - *"Check my references"*
   - *"Fix my citations"*
   - *"Is this APA correct?"*

Claude will recognize the skill and walk through the full audit workflow automatically.

---

## Files in This Repository

| File | Description |
|------|-------------|
| `SKILL.md` | Main skill instructions — the workflow Claude follows |
| `references/apa7-rules.md` | Complete APA 7 rule checklist used during the audit |
| `LICENSE` | MIT License |

---

## Important Disclaimer

This tool is intended strictly for **student self-improvement**. It is not meant to be used by instructors for grading purposes. The tool is based on a probabilistic AI model and may make errors. Students remain responsible for the work they submit. Critical thinking and knowledge of APA 7 guidelines are still required to maximize accuracy.

---

**Developed by:** Dr. Cari L. Miller  
**Institution:** College of Business and Technology, Wilmington University, Wilmington, DE  
**LinkedIn:** [linkedin.com/in/cari-miller](https://www.linkedin.com/in/cari-miller/)


---

## License

This project is licensed under the **MIT License** — see the [LICENSE](./LICENSE) file for details.

Attribution to **Dr. Cari L. Miller, Wilmington University** must be retained in all copies or substantial portions of this skill.

---

## Contributing

If you find an APA 7 rule that should be added, an edge case that isn't handled, or have a suggestion for improving the workflow, feel free to open an issue or submit a pull request.
