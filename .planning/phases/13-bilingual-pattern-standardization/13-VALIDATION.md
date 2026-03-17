---
phase: 13
slug: bilingual-pattern-standardization
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-18
---

# Phase 13 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Manual verification (no automated test framework in project) |
| **Config file** | None — documentation-only project |
| **Quick run command** | `test -f references/bilingual-output.md && echo PASS` |
| **Full suite command** | Manual: verify spec content against CONTEXT.md decisions, cross-check with existing Skill patterns |
| **Estimated runtime** | ~5 seconds |

---

## Sampling Rate

- **After every task commit:** Verify file exists and is non-empty: `test -f references/bilingual-output.md && echo PASS`
- **After every plan wave:** Manual review of spec content against CONTEXT.md decisions
- **Before `/gsd:verify-work`:** Full manual verification — both format variants documented, examples present, migration checklist complete
- **Max feedback latency:** 5 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 13-01-01 | 01 | 1 | BILN-03 | smoke | `test -f references/bilingual-output.md && echo PASS` | ❌ W0 | ⬜ pending |
| 13-01-02 | 01 | 1 | BILN-02 | manual | Read `references/bilingual-output.md`, verify LaTeX variant section exists with example | ❌ W0 | ⬜ pending |
| 13-01-03 | 01 | 1 | BILN-02 | manual | Read `references/bilingual-output.md`, verify Markdown variant section exists with example | ❌ W0 | ⬜ pending |
| 13-01-04 | 01 | 1 | BILN-02 | manual | Verify format selection rule: output format determines variant, not Skill identity | ❌ W0 | ⬜ pending |
| 13-01-05 | 01 | 1 | SC-3 | manual | Cross-check translation-skill pattern matches LaTeX variant in spec | ❌ W0 | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

*Existing infrastructure covers all phase requirements. No test framework installation needed — this is a documentation-only phase. Verification is manual file content review.*

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| LaTeX variant documented with example | BILN-02 | Content quality check | Read spec, verify `%` comment format, `% --- Paragraph N ---` markers, `_bilingual.tex` naming |
| Markdown variant documented with example | BILN-02 | Content quality check | Read spec, verify `>` blockquote format, `<!-- Paragraph N -->` markers |
| Format selection rule documented | BILN-02 | Prose quality check | Verify rule states output format determines variant, not Skill identity |
| translation-skill consistency | SC-3 | Cross-file comparison | Compare spec LaTeX example with translation-skill SKILL.md lines 145-148 |
| Migration checklist present | BILN-03 | Content completeness | Verify per-Skill checklist table exists with eligible + exempt Skills |

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 5s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
