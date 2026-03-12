---
phase: 8
slug: figure-table-and-logic-skills
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-12
---

# Phase 8 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Structural inspection (no runtime test framework — markdown-only Skills) |
| **Config file** | none |
| **Quick run command** | `wc -l .claude/skills/caption-skill/SKILL.md && grep -c "^## " .claude/skills/caption-skill/SKILL.md` |
| **Full suite command** | Run all commands in Per-Task Verification Map below |
| **Estimated runtime** | ~5 seconds |

---

## Sampling Rate

- **After every task commit:** `wc -l .claude/skills/caption-skill/SKILL.md && grep -c "^## " .claude/skills/caption-skill/SKILL.md`
- **After every plan wave:** Full suite (all grep checks in map below) for both Skills
- **Before `/gsd:verify-work`:** All structural checks pass
- **Max feedback latency:** 5 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 8-01-01 | 01 | 1 | SECT-02 | structural | `grep -n "generate\|optimize\|figure\|table\|geography\|CRS\|caption" .claude/skills/caption-skill/SKILL.md` | ❌ Wave 0 | ⬜ pending |
| 8-01-02 | 01 | 1 | SECT-02 | structural | `grep -n "Write\|\.tex\|\\\\caption" .claude/skills/caption-skill/SKILL.md` | ❌ Wave 0 | ⬜ pending |
| 8-01-03 | 01 | 1 | SECT-02 | structural | `grep -c "not found" .claude/skills/caption-skill/SKILL.md` | ❌ Wave 0 | ⬜ pending |
| 8-01-04 | 01 | 1 | SECT-02 | structural | `grep -n "study area\|CRS\|data source\|Ask Strategy" .claude/skills/caption-skill/SKILL.md` | ❌ Wave 0 | ⬜ pending |
| 8-01-05 | 01 | 1 | SECT-02 | structural | `wc -l .claude/skills/caption-skill/SKILL.md` | ❌ Wave 0 | ⬜ pending |
| 8-02-01 | 02 | 1 | SECT-04 | structural | `grep -n "Argument Chain\|Unsupported\|Terminology\|Number Contradiction\|AC-\|UC-\|TI-\|NC-" .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 | ⬜ pending |
| 8-02-02 | 02 | 1 | SECT-04 | structural | `grep -n "Part 1\|Part 2\|Argument Chain View\|Categorized Issue" .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 | ⬜ pending |
| 8-02-03 | 02 | 1 | SECT-04 | structural | `grep -n "requires the full paper\|partial" .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 | ⬜ pending |
| 8-02-04 | 02 | 1 | SECT-04 | structural | `grep -n "_logic\.md\|logic\.md" .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 | ⬜ pending |
| 8-02-05 | 02 | 1 | SECT-04 | structural | `wc -l .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `.claude/skills/caption-skill/SKILL.md` — covers SECT-02
- [ ] `.claude/skills/logic-skill/SKILL.md` — covers SECT-04

*Both files are the deliverables of this phase — they are created in Wave 0 execution.*

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Caption Skill correctly generates caption for a spatial map figure | SECT-02 | Behavioral output requires human invocation | Invoke with a spatial figure description, verify study area + data source appear in caption output |
| Caption Skill correctly writes to .tex file at the right `\caption{}` location | SECT-02 | File write behavior requires runtime invocation | Provide .tex file with single figure, verify caption inserted at correct location |
| Logic Skill produces two-part report for a full paper with known logic gaps | SECT-04 | Argument chain analysis requires human judgment | Provide sample paper, verify Part 1 table maps sections and Part 2 lists at least one issue per type present |
| Logic Skill bilingual output correct (English + Chinese blockquote) | SECT-04 | Language quality requires human verification | Verify Chinese blockquote appears after each issue entry |

---

## Validation Sign-Off

- [ ] All tasks have automated verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 5s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
