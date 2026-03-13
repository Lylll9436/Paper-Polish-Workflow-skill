---
phase: 05
slug: de-ai-skill
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-11
---

# Phase 05 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Manual review (markdown Skill, no executable code) |
| **Config file** | none |
| **Quick run command** | Read SKILL.md and verify against skill-conventions.md checklist |
| **Full suite command** | End-to-end test: invoke De-AI Skill on sample academic text |
| **Estimated runtime** | ~2 minutes (manual) |

---

## Sampling Rate

- **After every task commit:** Read SKILL.md, verify frontmatter fields, section presence, line count
- **After every plan wave:** Invoke Skill on a test paragraph with known High/Medium/Optional patterns
- **Before `/gsd:verify-work`:** Full invocation test covering both input modes (file + pasted text)
- **Max feedback latency:** ~120 seconds (manual review)

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 05-01-01 | 01 | 1 | CORE-03a | manual | Invoke on sample text with known anti-AI patterns | N/A | ⬜ pending |
| 05-01-02 | 01 | 1 | CORE-03b | manual | Select "fix all High Risk" and verify rewrites | N/A | ⬜ pending |
| 05-01-03 | 01 | 1 | CORE-03c | manual | Read rewritten text for coherence and meaning preservation | N/A | ⬜ pending |
| 05-01-04 | 01 | 1 | CORE-03d | manual | Check LaTeX annotations or conversation diff output | N/A | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `.claude/skills/de-ai-skill/SKILL.md` — the deliverable itself (does not exist yet)
- No test infrastructure needed — this is a markdown Skill file, validation is manual invocation

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| AI pattern detection with risk tagging | CORE-03a | Markdown Skill, no executable tests | Invoke on text with known anti-AI patterns, verify output shows risk levels |
| Rewriting using anti-AI alternatives | CORE-03b | Requires reading rewritten text quality | Select "fix all High Risk", verify rewrites use anti-AI library alternatives |
| Academic quality preservation | CORE-03c | Subjective quality assessment | Read rewritten paragraphs for coherence and meaning |
| Before/after comparison review | CORE-03d | UI/output format verification | Check LaTeX annotations in file mode, diff in conversation mode |

---

## Validation Sign-Off

- [ ] All tasks have manual verify instructions
- [ ] Sampling continuity: every task has verification criteria
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 120s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
