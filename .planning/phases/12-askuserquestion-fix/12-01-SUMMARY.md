---
phase: 12-askuserquestion-fix
plan: 01
subsystem: skill-authoring
tags: [skill-md, tool-names, askuserquestion, markdown-rewrite]

# Dependency graph
requires:
  - phase: 11-convention-tech-debt
    provides: skill-conventions.md and skill-skeleton.md standards
provides:
  - Rewritten paper-polish-workflow/SKILL.md with correct tool names and skeleton-compliant structure
affects: [paper-polish-workflow]

# Tech tracking
tech-stack:
  added: []
  patterns: [skill-skeleton-compliance, askuserquestion-pseudocode-format]

key-files:
  created: []
  modified:
    - paper-polish-workflow/SKILL.md

key-decisions:
  - "Full rewrite (not incremental edit) due to pervasive structural differences from target skeleton"
  - "Compressed from 348 to 193 lines while preserving all 15 workflow sub-operations"

patterns-established:
  - "AskUserQuestion pseudocode uses generic placeholders, not hardcoded content"
  - "paper-polish-workflow defaults to interactive mode (unlike polish-skill which defaults to direct)"

requirements-completed: [UXFIX-01]

# Metrics
duration: 3min
completed: 2026-03-17
---

# Phase 12 Plan 01: AskUserQuestion Fix Summary

**Rewritten paper-polish-workflow/SKILL.md replacing all mcp_* tool names with correct Claude Code names and restructuring to skeleton-compliant 4-step workflow in 193 lines**

## Performance

- **Duration:** 3 min
- **Started:** 2026-03-17T15:21:18Z
- **Completed:** 2026-03-17T15:24:47Z
- **Tasks:** 2
- **Files modified:** 1

## Accomplishments
- Eliminated all 17 occurrences of incorrect mcp_* tool names (mcp_question, mcp_read, mcp_write, mcp_edit, mcp_look_at)
- Restructured file to match skill-skeleton.md section order: Purpose, Trigger, Modes, References, Ask Strategy, Workflow, Output Contract, Edge Cases, Fallbacks
- Consolidated 6-step workflow into 4 steps while preserving all 15 sub-operations
- Added complete YAML frontmatter with bilingual triggers, tool capabilities, reference declarations
- Compressed from 348 lines to 193 lines (well within 300-line budget)
- All 17 validation checks passed on first attempt

## Task Commits

Each task was committed atomically:

1. **Task 1: Rewrite paper-polish-workflow/SKILL.md** - `703ef4d` (fix)
2. **Task 2: Comprehensive validation** - No commit (validation-only, all checks passed with no fixes needed)

## Files Created/Modified
- `paper-polish-workflow/SKILL.md` - Complete rewrite with correct tool names, skeleton-compliant structure, 4-step workflow

## Decisions Made
- Full rewrite via Write tool rather than incremental edits -- structural differences too pervasive for safe patching
- 193 lines achieved (57% of 300-line budget) by removing redundant sections (Key Features, Core Principles, Tools Used, Journal-Specific Requirements) and using concise prose

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness
- paper-polish-workflow/SKILL.md is now structurally aligned with all other Skills in the project
- All tool references use correct Claude Code names
- Ready for any future phase that depends on skill file consistency

## Self-Check: PASSED

- paper-polish-workflow/SKILL.md: FOUND
- 12-01-SUMMARY.md: FOUND
- Commit 703ef4d: FOUND

---
*Phase: 12-askuserquestion-fix*
*Completed: 2026-03-17*
