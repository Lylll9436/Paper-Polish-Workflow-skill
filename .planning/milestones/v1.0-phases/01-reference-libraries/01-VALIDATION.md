---
phase: 01
slug: reference-libraries
status: draft
nyquist_compliant: true
wave_0_complete: true
created: 2026-03-11
---

# Phase 01 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Shell-based artifact validation (`rg` + `Test-Path`) |
| **Config file** | none - existing shell tools are sufficient |
| **Quick run command** | `rg -n "^## " references\expression-patterns.md references\expression-patterns\*.md references\journals\ceus.md references\anti-ai-patterns.md references\anti-ai-patterns\*.md` |
| **Full suite command** | `rg -n "Submission Requirements|Writing Preferences|Quality Checks|High Risk|Medium Risk|Optional|Recommended Expressions|Avoid Expressions|Usage Scenarios" references\*.md references\expression-patterns\*.md references\anti-ai-patterns\*.md references\journals\*.md README.md README_CN.md CONTRIBUTING.md CONTRIBUTING_CN.md` |
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
| 01-01-01 | 01 | 1 | FNDN-01 | structure | `rg -n "How to Use This Library|Module Map" references\expression-patterns.md` | ✅ | ⬜ pending |
| 01-01-02 | 01 | 1 | FNDN-01 | content | `rg -n "Recommended Expressions\|Avoid Expressions\|Usage Scenarios\|中文\|Example\|示例" references\expression-patterns\*.md` | ❌ W0 | ⬜ pending |
| 01-01-03 | 01 | 1 | FNDN-01 | linkage | `rg -n "introduction-and-gap|methods-and-data|results-and-discussion|conclusions-and-claims|geography-domain" references\expression-patterns.md` | ✅ | ⬜ pending |
| 01-02-01 | 02 | 2 | FNDN-02 | contract | `rg -n "Submission Requirements|Writing Preferences|Quality Checks|Section Guidance" references\journals\ceus.md` | ✅ | ⬜ pending |
| 01-02-02 | 02 | 2 | FNDN-03 | content | `rg -n "High Risk|Medium Risk|Optional|Problem expression|Replacement" references\anti-ai-patterns.md references\anti-ai-patterns\*.md` | ❌ W0 | ⬜ pending |
| 01-02-03 | 02 | 2 | FNDN-02,FNDN-03 | docs | `rg -n "expression-patterns/|anti-ai-patterns|journals/ceus.md" README.md README_CN.md CONTRIBUTING.md CONTRIBUTING_CN.md` | ✅ | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

Existing infrastructure covers all phase requirements.

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Each leaf reference file feels narrowly scoped and independently readable | FNDN-01,FNDN-03 | Scope quality is partly semantic, not just structural | Open each new leaf file and confirm it can be loaded alone without relying on hidden context from sibling files |

---

## Validation Sign-Off

- [x] All tasks have `<automated>` verify or existing shell-based verification
- [x] Sampling continuity: no 3 consecutive tasks without automated verify
- [x] Wave 0 covers all MISSING references
- [x] No watch-mode flags
- [x] Feedback latency < 10s
- [x] `nyquist_compliant: true` set in frontmatter

**Approval:** pending 2026-03-11

