---
phase: 18
slug: workflow-memory
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-18
---

# Phase 18 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | None — pure Markdown/JSON configuration project with no executable code |
| **Config file** | N/A |
| **Quick run command** | N/A |
| **Full suite command** | N/A |
| **Estimated runtime** | N/A |

---

## Sampling Rate

- **After every task commit:** Read modified files and verify textual content matches spec
- **After every plan wave:** Read all 16 modified files end-to-end
- **Before `/gsd:verify-work`:** Complete file review
- **Max feedback latency:** N/A (manual verification)

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 18-01-01 | 01 | 1 | UXFIX-03 | manual | Read config.json, verify `workflow_memory` key | N/A | ⬜ pending |
| 18-01-02 | 01 | 1 | UXFIX-03 | manual | Read skill-conventions.md, verify Workflow Memory section | N/A | ⬜ pending |
| 18-01-03 | 01 | 1 | UXFIX-03 | manual | Read skill-skeleton.md, verify Step 0 + record-write | N/A | ⬜ pending |
| 18-01-04 | 01 | 2 | UXFIX-03 | manual | Read all 12 SKILL.md files, verify record-write step | N/A | ⬜ pending |
| 18-01-05 | 01 | 2 | UXFIX-03 | manual | Read all 12 SKILL.md files, verify Step 0 pattern detection | N/A | ⬜ pending |
| 18-01-06 | 01 | 2 | UXFIX-03 | manual | Read all 12 SKILL.md files, verify AskUserQuestion dialog | N/A | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

*Existing infrastructure covers all phase requirements. No test framework needed for a pure Markdown/JSON configuration project.*

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Skill writes to workflow-memory.json after first validation | UXFIX-03 | No executable code — Markdown instructions only | Read each SKILL.md, confirm record-write step exists after first-step validation |
| Step 0 detects patterns and offers recommendation | UXFIX-03 | No executable code — Markdown instructions only | Read each SKILL.md, confirm Step 0 with pattern detection and AskUserQuestion |
| User can accept or decline recommendation | UXFIX-03 | No executable code — Markdown instructions only | Read each SKILL.md, confirm accept → direct mode, decline → normal mode |
| Config has workflow_memory key | UXFIX-03 | JSON config file | Read .planning/config.json, confirm workflow_memory key with threshold/max_history/max_chain |
| Conventions document updated | UXFIX-03 | Markdown reference file | Read skill-conventions.md, confirm Workflow Memory section with mandatory rules |

---

## Validation Sign-Off

- [ ] All tasks have manual verification instructions
- [ ] Sampling continuity: every task commit triggers file read verification
- [ ] Wave 0 not needed — no test infrastructure required
- [ ] No watch-mode flags
- [ ] Feedback latency: N/A (manual)
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
