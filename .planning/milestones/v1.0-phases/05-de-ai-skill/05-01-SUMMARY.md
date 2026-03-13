---
phase: 05-de-ai-skill
plan: 01
subsystem: skill
tags: [de-ai, anti-ai-patterns, risk-tagging, academic-writing, latex-annotation]

# Dependency graph
requires:
  - phase: 01-reference-libraries
    provides: Anti-AI patterns library (vocabulary, sentence-patterns, transitions-and-tone) with three-tier risk model
  - phase: 02-skill-conventions
    provides: Skill conventions, skeleton template, frontmatter contract
provides:
  - De-AI Skill SKILL.md with two-phase detect-then-rewrite workflow
  - Risk-tagged AI pattern detection across three dimensions
  - Batch rewrite selection by risk level
affects: [06-reviewer-simulation-skill, 10-documentation]

# Tech tracking
tech-stack:
  added: []
  patterns: [two-phase-detect-then-rewrite, risk-level-batch-selection, domain-term-protection]

key-files:
  created:
    - .claude/skills/de-ai-skill/SKILL.md
  modified: []

key-decisions:
  - "Two-phase workflow: detect first with risk-tagged results, user selects, then rewrite (locked decision from CONTEXT.md)"
  - "Domain term protection uses dynamic inference rather than hardcoded wordlist"
  - "Optional-tier patterns flagged only when appearing 3+ times (repetition threshold)"
  - "Expression pattern leaves loaded during rewrite phase for quality beyond anti-AI replacement column"

patterns-established:
  - "Two-phase detect-then-rewrite: separate detection presentation from rewrite action with user decision between phases"
  - "Risk-level batch selection: user selects by tier (High/Medium/Optional) rather than per-item confirmation"
  - "De-AI LaTeX annotation: % [De-AI] Original: prefix distinct from Polish Skill's % [Polish] Original:"

requirements-completed: [CORE-03]

# Metrics
duration: 4min
completed: 2026-03-12
---

# Phase 5 Plan 01: De-AI Skill Summary

**Two-phase detect-then-rewrite De-AI Skill with three-tier risk tagging, batch selection, and domain term protection using anti-AI patterns library**

## Performance

- **Duration:** 4 min
- **Started:** 2026-03-11T23:48:50Z
- **Completed:** 2026-03-11T23:52:23Z
- **Tasks:** 2
- **Files modified:** 1

## Accomplishments
- Created complete De-AI Skill SKILL.md (213 lines, under 300-line budget) with two-phase workflow
- All locked decisions from 05-CONTEXT.md encoded: three-tier risk tagging, batch selection, De-AI annotation format, dual input modes, journal template refusal, domain term protection
- All three anti-AI pattern leaves declared as proactively loaded for full-text scanning
- Expression pattern leaves loaded conditionally during rewrite phase for section-appropriate alternatives

## Task Commits

Each task was committed atomically:

1. **Task 1: Author the De-AI Skill SKILL.md** - `b417584` (feat)
2. **Task 2: Validate Skill against conventions and locked decisions** - no separate commit (validation-only, SKILL.md passed all checks without edits)

## Files Created/Modified
- `.claude/skills/de-ai-skill/SKILL.md` - Complete De-AI Skill with detect-then-rewrite workflow, risk tagging, and batch selection

## Decisions Made
- Two-phase workflow with user decision between detect and rewrite phases (locked decision, implemented as specified)
- Domain term protection via dynamic context inference rather than hardcoded wordlist (Claude's discretion item)
- Optional-tier repetition threshold set at 3+ occurrences per research recommendation
- Expression pattern leaves loaded during rewrite phase based on detected section type for richer alternatives

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered
None

## User Setup Required
None - no external service configuration required.

## Next Phase Readiness
- De-AI Skill complete and follows established patterns from Translation and Polish Skills
- Phase 6 (Reviewer Simulation Skill) can proceed; it depends only on Phase 2 conventions
- All four core Skills (Translation, Polish, De-AI, Reviewer) share consistent patterns: dual input, LaTeX annotations, journal-aware, reference loading

## Self-Check: PASSED

- FOUND: .claude/skills/de-ai-skill/SKILL.md
- FOUND: .planning/phases/05-de-ai-skill/05-01-SUMMARY.md
- FOUND: commit b417584

---
*Phase: 05-de-ai-skill*
*Completed: 2026-03-12*
