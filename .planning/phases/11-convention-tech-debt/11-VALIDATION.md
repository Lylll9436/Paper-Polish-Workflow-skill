---
phase: 11
slug: convention-tech-debt
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-17
---

# Phase 11 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Manual verification (documentation-only phase) |
| **Config file** | none — no test framework needed |
| **Quick run command** | `grep -c "escape hatch" .claude/skills/skill-conventions.md` |
| **Full suite command** | `bash -c 'grep "escape hatch" .claude/skills/skill-conventions.md && grep "External MCP" .claude/skills/literature-skill/SKILL.md && grep "ceus" .claude/skills/cover-letter-skill/SKILL.md && grep "AskUserQuestion" .claude/skills/skill-conventions.md'` |
| **Estimated runtime** | ~1 second |

---

## Sampling Rate

- **After every task commit:** Run quick grep verification
- **After every plan wave:** Run full suite command
- **Before `/gsd:verify-work`:** Full suite must be green
- **Max feedback latency:** 1 second

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 11-01-01 | 01 | 1 | DEBT-01 | grep | `grep -A5 "escape hatch" .claude/skills/skill-conventions.md` | ✅ | ⬜ pending |
| 11-01-02 | 01 | 1 | DEBT-02 | grep | `grep "External MCP" .claude/skills/literature-skill/SKILL.md` | ✅ | ⬜ pending |
| 11-01-03 | 01 | 1 | DEBT-03 | grep | `grep "ceus" .claude/skills/cover-letter-skill/SKILL.md` | ✅ | ⬜ pending |
| 11-01-04 | 01 | 1 | UXFIX-02 | grep | `grep -A10 "AskUserQuestion" .claude/skills/skill-conventions.md` | ✅ | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

*Existing infrastructure covers all phase requirements.*

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Bilingual eligibility classification accuracy | DEBT-01 | Requires reading prose section | Read bilingual subsection, verify 7 eligible + 4 exempt Skills listed |

*All other behaviors have automated grep verification.*

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 1s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
