---
phase: 11-convention-tech-debt
plan: 01
subsystem: conventions
tags: [skill-conventions, skill-skeleton, tech-debt, AskUserQuestion, bilingual-output]

# Dependency graph
requires:
  - phase: v1.0 commit 8492320
    provides: DEBT-02 and DEBT-03 fixes already applied
provides:
  - Updated skill-conventions.md with escape hatch Skill names, AskUserQuestion enforcement, bilingual eligibility
  - Synced skill-skeleton.md with AskUserQuestion and bilingual cross-references
  - DEBT-02 and DEBT-03 verified correct
affects: [12-ask-bilingual, 13-bilingual-output, 17-skill-conformance]

# Tech tracking
tech-stack:
  added: []
  patterns: [AskUserQuestion enforcement per-Skill, bilingual eligibility classification by output_contract type]

key-files:
  created: []
  modified:
    - references/skill-conventions.md
    - references/skill-skeleton.md

key-decisions:
  - "AskUserQuestion enforcement is per-Skill audit, not blanket mandate"
  - "Bilingual eligibility uses output_contract classification: 7 eligible, 4 exempt"
  - "Escape hatch enhanced inline with Skill names, not as new subsection"
  - "Batch mode added as AskUserQuestion exemption alongside direct mode"

patterns-established:
  - "AskUserQuestion enforcement: per-Skill audit approach with direct/batch mode exemptions"
  - "Bilingual eligibility: output_contract-based classification (academic text = eligible, analysis/recommendation = exempt)"

requirements-completed: [DEBT-01, DEBT-02, DEBT-03, UXFIX-02]

# Metrics
duration: 3min
completed: 2026-03-17
---

# Phase 11 Plan 01: Convention & Tech Debt Summary

**Updated skill-conventions.md with three additions (escape hatch Skill names, AskUserQuestion enforcement with good/bad comparison, bilingual eligibility classification) and synced skill-skeleton.md**

## Performance

- **Duration:** 3 min
- **Started:** 2026-03-17T05:13:26Z
- **Completed:** 2026-03-17T05:16:16Z
- **Tasks:** 2
- **Files modified:** 2

## Accomplishments
- DEBT-01: Enhanced escape hatch rule with concrete Skill name references (logic-skill, visualization-skill, literature-skill)
- UXFIX-02: Added AskUserQuestion Enforcement subsection with per-Skill audit approach, direct/batch mode exemptions, good/bad comparison example, and fallback cross-reference
- Added Bilingual Output Eligibility classification with 7 eligible and 4 exempt Skills in a table format
- Synced skill-skeleton.md with AskUserQuestion enforcement cross-reference and bilingual eligibility guidance note
- Verified DEBT-02: literature-skill tools field correctly uses "External MCP"
- Verified DEBT-03: cover-letter-skill required field correctly lists references/journals/ceus.md

## Task Commits

Each task was committed atomically:

1. **Task 1: Update skill-conventions.md with escape hatch enhancement, AskUserQuestion enforcement, and bilingual eligibility** - `83f594e` (feat)
2. **Task 2: Sync skill-skeleton.md and verify DEBT-02/DEBT-03 fixes** - `78d138e` (feat)

**Plan metadata:** (pending final commit)

## Files Created/Modified
- `references/skill-conventions.md` - Added 48 lines: escape hatch Skill names (DEBT-01), AskUserQuestion enforcement subsection (UXFIX-02), bilingual output eligibility classification
- `references/skill-skeleton.md` - Added 3 lines: AskUserQuestion cross-reference in Ask Strategy, bilingual eligibility note in Output Contract

## Decisions Made
- AskUserQuestion enforcement follows per-Skill audit approach (no blanket mandate) per CONTEXT.md locked decision
- Bilingual eligibility classified by output_contract type: academic text producers are eligible, analysis/recommendation/citation Skills are exempt
- Batch mode included as AskUserQuestion exemption alongside direct mode (Claude's discretion from CONTEXT.md)
- Escape hatch enhancement kept inline per CONTEXT.md decision (not a new subsection)

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered
None

## User Setup Required
None - no external service configuration required.

## Next Phase Readiness
- Conventions are now clean and complete for v2.0 authoring
- Phase 12 can apply AskUserQuestion fixes to individual Skills using the new enforcement rules
- Phase 13 can implement bilingual output format using the eligibility classification established here
- Phase 17 can audit all Skills against updated conventions

## Verification Evidence

### DEBT-02 (verify-only)
`literature-skill/SKILL.md` line 16: `- External MCP` -- CONFIRMED correct.

### DEBT-03 (verify-only)
`cover-letter-skill/SKILL.md` lines 21-22: `required:` followed by `- references/journals/ceus.md` -- CONFIRMED correct.

## Self-Check: PASSED

- FOUND: references/skill-conventions.md
- FOUND: references/skill-skeleton.md
- FOUND: .planning/phases/11-convention-tech-debt/11-01-SUMMARY.md
- FOUND: commit 83f594e (Task 1)
- FOUND: commit 78d138e (Task 2)

---
*Phase: 11-convention-tech-debt*
*Completed: 2026-03-17*
