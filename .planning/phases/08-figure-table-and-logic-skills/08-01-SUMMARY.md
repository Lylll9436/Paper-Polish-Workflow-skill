---
phase: 08-figure-table-and-logic-skills
plan: "01"
subsystem: skill-authoring
tags: [caption-skill, latex, geography-aware, dual-path, tex-file-output, figure, table]

# Dependency graph
requires:
  - phase: 02-skill-conventions
    provides: skill-conventions.md frontmatter contract, section requirements, line budget rules
  - phase: 07-abstract-and-experiment-skills
    provides: dual-path generate/optimize pattern established in abstract-skill
  - phase: 01-reference-foundation
    provides: references/expression-patterns/geography-domain.md leaf file

provides:
  - ".claude/skills/caption-skill/SKILL.md — caption generation and optimization Skill with geography-aware Ask Strategy"
  - "Geography-conditional branching: spatial figures get study area / CRS / data source questions; non-spatial figures skip entirely"
  - "Read-before-Write pattern for .tex file insertion"
  - "Three-part LaTeX caption format: subject, content description, data source"

affects:
  - phase 09 (literature skill) — establishes file-write pattern consistent with existing Skills
  - future caption tasks — provides complete Skill users can invoke immediately

# Tech tracking
tech-stack:
  added: []
  patterns:
    - "Geography-conditional Ask Strategy: branch on figure type FIRST, apply spatial questions only when type is map/photo/GIS"
    - "Graceful metadata skip: missing CRS, legend, or study area → skip clause; never insert [MISSING: ...] in caption output"
    - "Read-before-Write pattern for .tex file: Read to locate \\caption{} insertion point, then Write"
    - "Dual-path skill: Generate (content description → new caption) and Optimize (existing caption → improved caption)"

key-files:
  created:
    - ".claude/skills/caption-skill/SKILL.md — caption Skill with dual paths, geography-aware Ask Strategy, journal template support"
  modified: []

key-decisions:
  - "Caption Skill skips [MISSING: ...] placeholders entirely — missing optional metadata (CRS, legend, scale bar) produces shorter clause-free caption, not stubbed output"
  - "Ask Strategy gates geography questions behind figure type: only maps, photos, and GIS outputs trigger spatial metadata collection"
  - "Multiple \\caption{} commands in .tex file triggers Ask Strategy question about target \\label{} before writing"
  - "Journal template missing → refuse with locked message, consistent with all prior Skills"

patterns-established:
  - "Geography-conditional Ask Strategy: figure type question gates entire spatial metadata branch"
  - "Three-part LaTeX caption structure: [subject/area]. [content description]. [data source]."
  - "Caption graceful skip rule: missing optional spatial metadata → omit clause, never insert placeholder"

requirements-completed: [SECT-02]

# Metrics
duration: 3min
completed: 2026-03-12
---

# Phase 08 Plan 01: Caption Skill Summary

**Geography-aware dual-path caption Skill (generate/optimize) that writes LaTeX \\caption{} directly to .tex files, with conditional spatial metadata collection and journal template adaptation**

## Performance

- **Duration:** 3 min
- **Started:** 2026-03-12T09:48:36Z
- **Completed:** 2026-03-12T09:51:47Z
- **Tasks:** 2 (1 authored, 1 validated)
- **Files modified:** 1

## Accomplishments

- Authored `.claude/skills/caption-skill/SKILL.md` at 217 lines (under 300 budget)
- Implemented geography-conditional Ask Strategy: spatial figure types (map/photo/GIS) get study area, data source, CRS questions; non-spatial types skip geography entirely
- Applied Read-before-Write pattern for .tex file output: Read to locate `\caption{}` insertion point, then Write with updated content
- Validated all 9 structural checks: line count, 10 required sections, Generate/Optimize path coverage, geography branching, zero actual `[MISSING: ...]` in output, Write/.tex pattern, journal refusal, Chinese triggers, frontmatter completeness

## Task Commits

Each task was committed atomically:

1. **Task 1: Author caption-skill SKILL.md** - `25af857` (feat)
2. **Task 2: Validate caption-skill conventions compliance** - validation only, no file changes

**Plan metadata:** (docs commit — next)

## Files Created/Modified

- `.claude/skills/caption-skill/SKILL.md` — Complete caption Skill with dual paths (Generate/Optimize), geography-aware Ask Strategy, figure-vs-table branching, Read-before-Write .tex output, journal template support, graceful metadata skip rule

## Decisions Made

- Caption Skill explicitly does NOT use `[MISSING: ...]` placeholders — this differentiates it from Abstract Skill and Experiment Skill which do flag missing positions. Caption output must be usable as-is in a .tex file.
- Geography questions appear only when figure type is spatial — bar charts, line charts, and schematics skip study area / CRS questions entirely.

## Deviations from Plan

None — plan executed exactly as written.

## Issues Encountered

Check 5 (no MISSING placeholders) initially returned 3 instead of 0. Investigation confirmed all three occurrences are in rule-documentation context (`do NOT insert [MISSING: ...]`), not in actual caption output. Output Contract and Examples sections have zero actual placeholders. The validation intent is met: no `[MISSING: ...]` stubs will appear in caption output produced by the Skill.

## User Setup Required

None — no external service configuration required.

## Next Phase Readiness

- Caption Skill (SECT-02) is complete and ready for use
- Phase 08 Plan 02 (Logic Verification Skill / SECT-04) can begin immediately
- No blockers

---
*Phase: 08-figure-table-and-logic-skills*
*Completed: 2026-03-12*
