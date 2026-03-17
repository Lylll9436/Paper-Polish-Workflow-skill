---
phase: 12
slug: askuserquestion-fix
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-17
---

# Phase 12 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Manual validation (Markdown file, no automated test runner) |
| **Config file** | N/A |
| **Quick run command** | `grep -c 'mcp_' paper-polish-workflow/SKILL.md` (must return 0) |
| **Full suite command** | See Per-Task Verification Map below |
| **Estimated runtime** | ~2 seconds |

---

## Sampling Rate

- **After every task commit:** Run `grep -c 'mcp_' paper-polish-workflow/SKILL.md && wc -l paper-polish-workflow/SKILL.md`
- **After every plan wave:** Run all automated commands in verification map
- **Before `/gsd:verify-work`:** Full suite must be green
- **Max feedback latency:** 2 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 12-01-01 | 01 | 1 | UXFIX-01 | smoke | `grep -c 'mcp_' paper-polish-workflow/SKILL.md` — must return 0 | N/A (inline) | ⬜ pending |
| 12-01-02 | 01 | 1 | UXFIX-01 | smoke | `grep -c 'AskUserQuestion' paper-polish-workflow/SKILL.md` — must return >= 1 | N/A (inline) | ⬜ pending |
| 12-01-03 | 01 | 1 | UXFIX-01 | smoke | `grep -cE '\bRead\b\|\bWrite\b\|\bEdit\b' paper-polish-workflow/SKILL.md` — must return >= 1 | N/A (inline) | ⬜ pending |
| 12-01-04 | 01 | 1 | UXFIX-01 | smoke | `wc -l paper-polish-workflow/SKILL.md` — must be <= 300 | N/A (inline) | ⬜ pending |
| 12-01-05 | 01 | 1 | UXFIX-01 | manual | Verify section headers match skeleton order | N/A | ⬜ pending |
| 12-01-06 | 01 | 1 | UXFIX-01 | smoke | `head -50 paper-polish-workflow/SKILL.md` contains `---` delimiters and required fields | N/A (inline) | ⬜ pending |
| 12-01-07 | 01 | 1 | UXFIX-01 | manual | Count `### Step` headers — must be exactly 4 | N/A | ⬜ pending |
| 12-01-08 | 01 | 1 | UXFIX-01 | manual | Verify journal style check, highlights, cross-section consistency, reference consultation, read-aloud all present | N/A | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

*Existing infrastructure covers all phase requirements.* No test infrastructure needed for a Markdown file rewrite. Validation is inline grep/wc commands plus manual review.

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| All skeleton sections present | UXFIX-01 | Section header format varies | Verify headers match skeleton order visually |
| 4-step workflow (not 6) | UXFIX-01 | Semantic check | Count `### Step` headers — must be exactly 4 |
| No lost sub-steps | UXFIX-01 | Content completeness check | Verify journal style check, highlights, cross-section consistency, reference consultation, read-aloud all present |

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 2s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
