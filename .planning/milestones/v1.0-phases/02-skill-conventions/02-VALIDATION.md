---
phase: 02
slug: skill-conventions
status: draft
nyquist_compliant: true
wave_0_complete: true
created: 2026-03-11
---

# Phase 02 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Shell-based document verification (`rg` + line count) |
| **Config file** | none - markdown and repo docs only |
| **Quick run command** | `rg -n "Frontmatter Contract|Reference Loading Rules|Interaction Modes|Fallback Rules|Line Budget" references\skill-conventions.md references\skill-skeleton.md` |
| **Full suite command** | `rg -n "^name:|^description:|^triggers:|^tools:|^references:|^input_modes:|^output_contract:|## Purpose|## Trigger|## Modes|## References|## Ask Strategy|## Workflow|## Output Contract|## Edge Cases|## Fallbacks|interactive|guided|direct|batch" references\skill-skeleton.md references\skill-conventions.md README.md README_CN.md CONTRIBUTING.md CONTRIBUTING_CN.md` |
| **Estimated runtime** | ~5 seconds |

---

## Sampling Rate

- **After every task commit:** Run the quick run command
- **After every plan wave:** Run the full suite command
- **Before `$gsd-verify-work`:** Full suite must be green
- **Max feedback latency:** 10 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 02-01-01 | 01 | 1 | FNDN-04 | structure | `rg -n "Frontmatter Contract|Reference Loading Rules|Interaction Modes|Fallback Rules|Line Budget" references\skill-conventions.md` | ❌ W0 | ⬜ pending |
| 02-01-02 | 01 | 1 | FNDN-04 | skeleton | `rg -n "^name:|^description:|^triggers:|^tools:|^references:|^input_modes:|^output_contract:|## Purpose|## Trigger|## Modes|## References|## Ask Strategy|## Workflow|## Output Contract|## Edge Cases|## Fallbacks" references\skill-skeleton.md` | ❌ W0 | ⬜ pending |
| 02-01-03 | 01 | 1 | FNDN-04 | discoverability | `rg -n "skill-conventions|skill-skeleton" README.md README_CN.md CONTRIBUTING.md CONTRIBUTING_CN.md` | ✅ | ⬜ pending |
| 02-01-04 | 01 | 1 | FNDN-04 | line-budget | `powershell -NoLogo -NoProfile -Command "(Get-Content 'references\skill-skeleton.md' | Measure-Object -Line).Lines"` | ❌ W0 | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

Existing infrastructure covers all phase requirements.

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| The skeleton feels directly copyable for a later feature phase | FNDN-04 | Copyability is partly editorial, not only structural | Open `references/skill-skeleton.md` and confirm a future phase could start from it without combining multiple docs first |

---

## Validation Sign-Off

- [x] All tasks have `<automated>` verify or existing shell-based verification
- [x] Sampling continuity: no 3 consecutive tasks without automated verify
- [x] Wave 0 covers all MISSING references
- [x] No watch-mode flags
- [x] Feedback latency < 10s
- [x] `nyquist_compliant: true` set in frontmatter

**Approval:** pending 2026-03-11
