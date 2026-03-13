---
phase: 02-skill-conventions
plan: "01"
subsystem: conventions
tags: [yaml-frontmatter, skill-template, progressive-disclosure, interaction-modes, fallback-rules]

# Dependency graph
requires:
  - phase: 01-reference-libraries
    provides: Modular reference architecture with stable entrypoints and leaf modules
provides:
  - Canonical Skill authoring conventions document
  - Copyable example Skill skeleton for all future phases
  - Updated maintainer docs pointing to conventions artifacts
affects: [03-translation, 04-polish, 05-de-ai, 06-reviewer, 07-abstract-experiment, 08-figure-logic, 09-literature-support, 10-documentation]

# Tech tracking
tech-stack:
  added: []
  patterns: [strict-frontmatter-contract, capability-first-tool-policy, mode-taxonomy, reference-loading-strategy, line-budget-rule]

key-files:
  created:
    - references/skill-conventions.md
    - references/skill-skeleton.md
  modified:
    - README.md
    - README_CN.md
    - CONTRIBUTING.md
    - CONTRIBUTING_CN.md

key-decisions:
  - "Conventions and skeleton kept as separate linked files for independent copyability and maintenance"
  - "Tools listed by capability category (Read, Write, Structured Interaction) rather than vendor-specific names to prevent tool-name lock-in"
  - "~300 line hard budget with justification required for exceptions"
  - "Four interaction modes defined: interactive, guided, direct, batch"

patterns-established:
  - "Strict frontmatter: name, description, triggers, tools, references, input_modes, output_contract"
  - "Semi-templated body: Purpose, Trigger, Modes, References, Ask Strategy, Workflow, Output Contract, Edge Cases, Fallbacks"
  - "Capability-first tool policy: list what the Skill needs, not which vendor tool to call"
  - "Ask first then act: default interaction posture with mode-based exceptions"

requirements-completed: [FNDN-04]

# Metrics
duration: 4min
completed: 2026-03-11
---

# Phase 2 Plan 01: Skill Conventions Summary

**Strict frontmatter contract, four-mode interaction taxonomy, capability-first tool policy, and copyable 155-line skeleton for all future Skills**

## Performance

- **Duration:** 4 min
- **Started:** 2026-03-11T12:49:06Z
- **Completed:** 2026-03-11T12:53:08Z
- **Tasks:** 3
- **Files modified:** 6

## Accomplishments
- Created canonical conventions document covering frontmatter contract, reference loading, interaction modes, fallback rules, line budget, and ask strategy
- Created a 155-line copyable skeleton with all required frontmatter fields and body sections
- Updated all four maintainer docs (README, README_CN, CONTRIBUTING, CONTRIBUTING_CN) to surface conventions as the default authoring starting point

## Task Commits

Each task was committed atomically:

1. **Task 1: Author the canonical Skill conventions document** - `3cb76b0` (feat)
2. **Task 2: Create a copyable example Skill skeleton** - `f3764f8` (feat)
3. **Task 3: Surface the conventions in maintainer-facing docs** - `facabba` (docs)

## Files Created/Modified
- `references/skill-conventions.md` - Normative authoring rules for all future Skills (198 lines)
- `references/skill-skeleton.md` - Copyable example skeleton with all required fields and sections (155 lines)
- `README.md` - Added "Authoring New Skills" section and updated project structure
- `README_CN.md` - Added Chinese equivalent of authoring section and updated structure
- `CONTRIBUTING.md` - Added "Creating a New Skill" guidance with expanded frontmatter example
- `CONTRIBUTING_CN.md` - Added Chinese equivalent of Skill creation guidance

## Decisions Made
- Conventions and skeleton are separate linked files, not a single combined document, for independent copyability
- Tools are described by capability category (Read, Write, Structured Interaction, PDF Analysis) rather than vendor-specific names to avoid tool-name lock-in
- Line budget set at ~300 lines as a hard target requiring justification for exceptions
- Four interaction modes defined (interactive, guided, direct, batch) with mode inference from trigger phrasing

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness
- All downstream phases (3-10) can now start from the skeleton and follow the conventions
- Phase 3 (Translation) and Phase 4 (Polish) are unblocked and can proceed in parallel
- The legacy SKILL.md at `paper-polish-workflow/SKILL.md` is untouched; Phase 4 will handle migration

## Self-Check: PASSED

All created files verified present. All commit hashes verified in git log.

---
*Phase: 02-skill-conventions*
*Completed: 2026-03-11*
