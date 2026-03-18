---
plan: 15-01
phase: 15-literature-integration
status: complete
---

# Plan 15-01 Summary: Add Step 2.5 Literature Collection

## What Was Built

Added `### Step 2.5: Literature Collection` to `.claude/skills/repo-to-paper-skill/SKILL.md`, integrating Semantic Scholar MCP batch search into the repo-to-paper workflow at the H2 stage.

## Key Decisions

- Step 2.5 is inserted between Step 3 (H2 confirmation) and Step 4 (H3 generation), using decimal numbering to avoid renumbering the existing steps
- References saved to `{repo_path}/.paper-refs/` (not the project's `references/` directory) with one file per H1 section
- MCP tools called directly within Step 2.5 (not delegating to literature-skill as a sub-Skill)
- BibTeX anti-hallucination: all fields MUST come from MCP-returned data; missing fields omitted

## Changes Made

### `.claude/skills/repo-to-paper-skill/SKILL.md`
- **Frontmatter**: Added `External MCP` to `tools` list; added `literature_refs` to `output_contract`
- **Description**: Added literature collection mention (English and Chinese lines)
- **Step 2.5**: Full Literature Collection step with pre-flight check, per-H2 batch search, ref file writing, and summary table
- **Output Contract**: Added `literature_refs` row
- **Edge Cases**: Added rows for MCP unavailable, zero results, mid-batch failure, Springer abstracts
- **Fallbacks**: Added row for Semantic Scholar MCP unavailable
- Line count: 381 (within 330-400 target)

## Self-Check

- [x] Step 2.5 heading exists
- [x] Pre-flight check calls `papers-search-basic` with `{"query": "test", "limit": 1}`
- [x] Graceful fallback: skip entirely + insert `[CITATION NEEDED]` when MCP unavailable
- [x] References `get-paper-abstract` for empty abstracts
- [x] Ref file directory specified as `{repo_path}/.paper-refs/`
- [x] BibTeX anti-hallucination rule: "All BibTeX fields MUST come from MCP-returned data"
- [x] Citation key format: `firstAuthorLastnameLowercaseYYYYfirstKeyword`
- [x] Progress display with ✓/⚠ prefixes per H2
- [x] Summary table with Section, Subsection, Refs, Top Reference columns
- [x] Edge Cases and Fallbacks tables updated
- [x] Line count 381 within 330-400 range
- [x] Step order: Step 1 → Step 2 → Step 3 → Step 2.5 → Step 4

## key-files

### created
(none — plan modifies existing file only)

### modified
- `.claude/skills/repo-to-paper-skill/SKILL.md` — 96 lines added, 1 replaced

## Requirements Addressed

- REPO-04: repo-to-paper-skill now includes Semantic Scholar MCP batch search at H2 stage with ref file output
