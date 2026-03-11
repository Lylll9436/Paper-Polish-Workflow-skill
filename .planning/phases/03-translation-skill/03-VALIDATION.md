---
phase: 03
slug: translation-skill
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-11
---

# Phase 03 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Manual validation (Skill is a markdown prompt, not executable code) |
| **Config file** | None |
| **Quick run command** | Manual: invoke Skill with test input and verify output |
| **Full suite command** | Manual: run all test scenarios below |
| **Estimated runtime** | ~5 minutes per full manual pass |

---

## Sampling Rate

- **After every task commit:** Manual smoke test with one Chinese paragraph
- **After every plan wave:** Run all 9 test scenarios
- **Before `/gsd:verify-work`:** Full suite must pass
- **Max feedback latency:** ~2 minutes per scenario

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 03-01-01 | 01 | 1 | CORE-01 | manual | Invoke Skill with `.md` file; check `_en.tex` output | N/A | ⬜ pending |
| 03-01-02 | 01 | 1 | CORE-01 | manual | Check YAML frontmatter, line count ≤300, required sections | N/A | ⬜ pending |
| 03-01-03 | 01 | 1 | CORE-01 | manual | Check `_bilingual.tex` produced alongside `_en.tex` | N/A | ⬜ pending |
| 03-01-04 | 01 | 1 | CORE-01 | manual | Input with `$x^2$`, `\cite{ref}`; verify preserved | N/A | ⬜ pending |
| 03-01-05 | 01 | 1 | CORE-01 | manual | Specify CEUS; verify style adaptation | N/A | ⬜ pending |
| 03-01-06 | 01 | 1 | CORE-01 | manual | Specify unknown journal; verify refusal | N/A | ⬜ pending |
| 03-01-07 | 01 | 1 | CORE-01 | manual | Provide glossary; verify terms used | N/A | ⬜ pending |
| 03-01-08 | 01 | 1 | CORE-01 | manual | Paste text input; verify output produced | N/A | ⬜ pending |
| 03-01-09 | 01 | 1 | CORE-01 | manual | Verify self-check summary in output | N/A | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `.claude/skills/translation-skill/` directory created
- [ ] Test input: short Chinese academic paragraph with LaTeX commands
- [ ] Test glossary file for terminology verification

*No automated testing possible — Skill is a prompt document consumed by Claude at runtime.*

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Translation quality | CORE-01 | Subjective assessment of academic English | Read output, verify natural academic tone |
| Glossary adherence | CORE-01 | Requires domain knowledge | Compare glossary terms against output |
| CEUS style match | CORE-01 | Requires journal style knowledge | Compare output against CEUS writing preferences |
| Self-check usefulness | CORE-01 | Subjective assessment | Verify flagged items are genuinely uncertain |
| Bilingual alignment | CORE-01 | Paragraph matching verification | Check each Chinese paragraph maps to correct English |

---

## Validation Sign-Off

- [ ] All tasks have manual verify scenarios
- [ ] Sampling continuity: every task has at least one verification step
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 5 min
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
