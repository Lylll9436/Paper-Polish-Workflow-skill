---
phase: 04
slug: polish-skill
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-11
---

# Phase 04 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Manual validation (Skill is a markdown prompt, not executable code) |
| **Config file** | None |
| **Quick run command** | Manual: invoke Skill with test input and verify behavior |
| **Full suite command** | Manual: run all test scenarios below |
| **Estimated runtime** | ~5 minutes per full manual test |

---

## Sampling Rate

- **After every task commit:** Manual smoke test with one English paragraph (Quick-fix mode)
- **After every plan wave:** Run all test scenarios below
- **Before `/gsd:verify-work`:** Full manual suite must pass
- **Max feedback latency:** N/A (manual validation)

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 04-01-01 | 01 | 1 | CORE-02 | manual | Invoke Skill with .tex file; verify single-pass edit with annotations | N/A | ⬜ pending |
| 04-01-02 | 01 | 1 | CORE-02 | manual | Say "guided polish"; verify three-step flow with checklists | N/A | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

Existing infrastructure covers all phase requirements. Skill is pure markdown — no test framework needed.

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Quick-fix mode polishes text in single pass | CORE-02 | Skill is a prompt, not code | Invoke with .tex file, verify single-pass edit with annotations |
| Guided mode follows structure→logic→expression | CORE-02 | Requires interactive session | Say "guided polish", verify three-step flow with checklists |
| LaTeX comment annotations preserve original | CORE-02 | Check edited file content | Verify `% [Polish] Original:` annotations in output |
| Annotation cleanup after acceptance | CORE-02 | Requires user confirmation flow | Confirm acceptance, verify annotations removed |
| Summary report generated | CORE-02 | Check session output | Verify summary with change count, types, notes |
| Pasted text polished in conversation | CORE-02 | Requires pasted input test | Paste text instead of file path, verify output |
| Journal-specific style applied | CORE-02 | Requires journal-aware check | Specify CEUS, verify cautious verbs, no promotional language |
| Missing journal triggers refusal | CORE-02 | Requires negative test | Specify nonexistent journal, verify refusal |
| Anti-AI patterns avoided | CORE-02 | Check output vocabulary | Verify polished text avoids high-risk AI vocabulary |
| Translationese detected and fixed | CORE-02 | Requires translation-artifact input | Provide text with translation artifacts, verify flagged/fixed |
| Skill follows Phase 2 conventions | CORE-02 | Check file structure | Verify YAML, required sections, line count <= 300 |
| De-AI recommendation in summary | CORE-02 | Check session output | Verify summary includes De-AI Skill recommendation |

---

## Validation Sign-Off

- [ ] All tasks have manual verify instructions
- [ ] Sampling continuity: every task has at least one verification step
- [ ] No watch-mode flags
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
