# Phase 10: Documentation - Research

**Researched:** 2026-03-12
**Domain:** GitHub README authoring — bilingual markdown documentation
**Confidence:** HIGH

---

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions

**Bilingual Structure**
- Two parallel halves: complete English version in the upper half, complete Chinese version in the lower half
- English first, Chinese second
- Each half is fully self-contained and symmetric — same sections, same structure
- Section headings in each half use their own language (English headings in upper half, Chinese headings in lower half)
- Skill inventory table, installation steps, and quick-start workflow examples are fully duplicated in both halves

**README File Structure**
- Single file: `README.md` at repository root — GitHub auto-renders it
- Contribution guide is a small embedded section at the end of each half (not a separate CONTRIBUTING.md)
- Contribution guide content: issue feedback guidance only (bug reports, feature requests) — not a full PR/Skill-authoring guide
- Page header: project name + one-sentence description + GitHub badges (license, etc.)

**Skill Inventory Table**
- 3 columns: Skill name | Trigger examples (Chinese + English) | One-line description
- Skills grouped by function:
  - **Writing workflow group**: translation-skill, polish-skill, de-ai-skill, reviewer-simulation-skill
  - **Support tools group**: abstract-skill, cover-letter-skill, experiment-skill, caption-skill, logic-skill, literature-skill, visualization-skill
- literature-skill row gets a ⚠️ annotation: `⚠️ Requires Semantic Scholar MCP`
- MCP setup details covered in the installation section, not in the table

**Installation Approach**
- One-liner instruction: give the repository GitHub URL to Claude Code and ask it to install the Skills
- No manual copy steps — Claude Code handles cloning and copying SKILL.md files to `.claude/skills/`
- Installation section briefly explains what Claude Code does: copies each Skill directory to `.claude/skills/`
- MCP setup for literature-skill: one short paragraph with step-by-step Claude Code settings instructions

**Quick Start Workflows**
- Three independent scenario examples (each with step-by-step trigger commands + one-sentence explanation per step):
  1. **Paper submission chain**: translate → polish → de-ai → reviewer (core 4-Skill chain)
  2. **Figure/table assistance**: caption-skill + visualization-skill (support tools for experimental figures)
  3. **Literature search**: literature-skill → generate BibTeX → abstract-skill (research support chain)
- Each step shown as: trigger keyword + dash + what it does in one sentence
- No full example prompts or sample outputs — commands + descriptions only
- No "prerequisites" block before quick-start; installation section covers setup

### Claude's Discretion
- Exact badge selection and badge styling
- Spacing and visual breaks between sections
- Whether to add a table of contents at the top
- Exact wording of one-line Skill descriptions (can draw from SKILL.md descriptions)
- Order of sections within each half (installation → inventory → quick start → contribution)

### Deferred Ideas (OUT OF SCOPE)

None — discussion stayed within phase scope.
</user_constraints>

---

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| DOCS-01 | Project README with installation instructions, Skill inventory, quick start guide, and contribution guidelines (bilingual) | All SKILL.md files have been read; descriptions, trigger examples, and dependency metadata are available for direct use. Installation pattern, table structure, and quick-start chains are fully specified in CONTEXT.md. |
</phase_requirements>

---

## Summary

Phase 10 is a pure markdown authoring task: write a single `README.md` at the repository root that replaces the existing outdated README. The file must contain two complete, symmetric halves (English upper, Chinese lower). All source content already exists in the 11 SKILL.md files — the task is assembly and translation, not invention.

The existing `README.md` documents the old single-skill `paper-polish-workflow/SKILL.md` and references OpenCode. It must be fully overwritten. The new file must describe the 11-Skill suite with the exact structure negotiated in CONTEXT.md. No other files need to be created or modified.

**Primary recommendation:** Read all 11 SKILL.md files, extract description and trigger examples, assemble the README according to the locked structure, write it in a single Write tool call.

---

## What Already Exists (Source Material Inventory)

All content for the README already exists and can be directly lifted:

### Skill Inventory Source Data

| Skill | Group | One-line Description (from SKILL.md) | English Trigger | Chinese Trigger |
|-------|-------|---------------------------------------|-----------------|-----------------|
| translation-skill | Writing workflow | Translate Chinese academic text into polished English for journal submission. Produces LaTeX output. | "Translate this Chinese draft to English" | "翻译这段中文为英文" |
| polish-skill | Writing workflow | Polish English academic text through quick-fix or guided multi-pass workflow. | "Polish this paragraph" | "润色这段英文" |
| de-ai-skill | Writing workflow | Detect and rewrite AI-generated patterns in English academic text. Two-phase: scan then rewrite. | "De-AI this paragraph" | "降AI这段论文" |
| reviewer-simulation-skill | Writing workflow | Simulate peer review with structured bilingual feedback report. | "Review this paper" | "审稿这篇论文" |
| abstract-skill | Support tools | Generate or optimize abstracts using the 5-sentence Farquhar formula. | "Write an abstract for my paper" | "帮我写摘要" |
| cover-letter-skill | Support tools | Generate submission-ready cover letters with contribution statement and contact block. | "Write a cover letter for my CEUS submission" | "帮我写投稿信" |
| experiment-skill | Support tools | Analyze experiment results and generate grounded discussion paragraphs. | "Analyze my experiment results" | "帮我分析实验结果" |
| caption-skill | Support tools | Generate or optimize figure/table captions with geography-aware metadata. | "Write a caption for my figure" | "帮我写图表标题" |
| logic-skill | Support tools | Verify logical consistency: argument chains, gaps, unsupported claims, number contradictions. | "Check the logic of my paper" | "检查我的论文逻辑" |
| literature-skill | Support tools | Search literature via Semantic Scholar MCP and generate verified BibTeX entries. ⚠️ Requires Semantic Scholar MCP | "Find papers about urban heat island" | "帮我找关于城市热岛的文献" |
| visualization-skill | Support tools | Recommend appropriate chart types for experimental data, geography-aware. | "What chart should I use for this data?" | "帮我选择合适的可视化方式" |

### Existing README to Overwrite

The current `README.md` at repository root describes the old single-skill era (OpenCode, manual copy steps). It must be fully replaced. The new file has no structural dependency on the old content.

### License

MIT License (confirmed from `LICENSE` file).

---

## Architecture Patterns

### Single-File Bilingual Structure

```
README.md
├── Page header (project name + one-sentence description + badges)
├── [Optional table of contents]
│
├── ═══ ENGLISH HALF ═══
│   ├── Installation
│   │   ├── One-liner Claude Code instruction
│   │   └── MCP setup paragraph (literature-skill only)
│   ├── Skill Inventory (grouped table)
│   ├── Quick Start
│   │   ├── Scenario 1: Paper submission chain
│   │   ├── Scenario 2: Figure/table assistance
│   │   └── Scenario 3: Literature search chain
│   └── Contributing (embedded, issue feedback only)
│
└── ═══ CHINESE HALF ═══
    ├── 安装说明
    │   ├── One-liner Claude Code instruction (Chinese)
    │   └── MCP setup paragraph (Chinese)
    ├── 技能清单 (same grouped table, Chinese descriptions)
    ├── 快速上手
    │   ├── 场景一: Paper submission chain (Chinese)
    │   ├── 场景二: Figure/table assistance (Chinese)
    │   └── 场景三: Literature search chain (Chinese)
    └── 参与贡献 (embedded, Chinese)
```

### Visual Separator Pattern

A horizontal rule (`---`) separates the English and Chinese halves. A second `---` separates each major section within a half. This is the standard GitHub README convention for long bilingual documents.

### Badge Pattern

GitHub Shields badges for a MIT-licensed repo with no build/CI:
```markdown
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![Claude Code](https://img.shields.io/badge/Claude%20Code-Skills-blue)
```
Badges are on the page header line, rendered inline. Exact badge selection is Claude's discretion per CONTEXT.md.

### Skill Inventory Table Format

Three-column format as decided:
```markdown
| Skill | Trigger Examples | Description |
|-------|-----------------|-------------|
| `translation-skill` | `Translate this Chinese draft` / `翻译这段中文为英文` | Translate Chinese academic text into polished English. |
```

Group headings use bold text or HTML comment dividers to label Writing workflow vs. Support tools groups within the same table.

### Quick Start Step Format

Each step on its own line:
```
1. **translation-skill** — Translate your Chinese draft into academic English with LaTeX output.
2. **polish-skill** — Polish the English output for journal submission style.
3. **de-ai-skill** — Scan for AI-generated patterns and rewrite flagged passages.
4. **reviewer-simulation-skill** — Get a structured peer review report before submission.
```

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Badge markup | Custom HTML image tags | Standard Shields.io badge markdown | GitHub renders inline, universally understood |
| Installation instructions | Step-by-step git clone + cp commands | Single one-liner: paste URL to Claude Code | Per locked decision — frictionless install is the intent |
| MCP setup guide | Full screenshot tutorial | Short numbered paragraph in prose | Keeps README concise; Claude Code settings UI is standard |

---

## Common Pitfalls

### Pitfall 1: Asymmetric Halves

**What goes wrong:** English and Chinese halves drift in structure — one has a section the other omits, or table columns differ.

**Why it happens:** Writing halves sequentially and missing a section in the second pass.

**How to avoid:** Write the English half completely first, then translate section by section in order. Verify section count matches before finishing.

**Warning signs:** Chinese half has fewer `##` headings than English half.

---

### Pitfall 2: Stale Skill Descriptions

**What goes wrong:** README describes Skill behavior that doesn't match the actual SKILL.md (e.g., wrong trigger words, missing ⚠️ annotation on literature-skill).

**Why it happens:** Paraphrasing from memory rather than lifting directly from SKILL.md.

**How to avoid:** Use SKILL.md `description` and `triggers.examples` fields verbatim as the source. Never paraphrase from project memory.

**Warning signs:** literature-skill row lacks the ⚠️ annotation.

---

### Pitfall 3: Manual Installation Instructions

**What goes wrong:** README tells users to `git clone` and `cp -r` manually — the old pattern from the existing README.

**Why it happens:** Defaulting to the existing README's approach.

**How to avoid:** The installation approach is locked: one-liner to Claude Code. Do not include manual shell commands for the main install path.

**Warning signs:** README contains `cp -r` or `git clone` commands in the installation section.

---

### Pitfall 4: Wrong File Target

**What goes wrong:** Writing a new separate file instead of overwriting `README.md` at repo root.

**Why it happens:** Confusing with the old bilingual two-file pattern (README.md + README_CN.md).

**How to avoid:** The locked decision is a single `README.md`. The old `README_CN.md` does not need to be updated or removed — focus is README.md only.

**Warning signs:** Creating `README_new.md` or modifying `README_CN.md`.

---

## Code Examples

### Skill Table Row with Group Header

```markdown
**Writing Workflow**

| Skill | Trigger Examples | Description |
|-------|-----------------|-------------|
| `translation-skill` | `Translate this Chinese draft` / `翻译这段中文为英文` | Translate Chinese academic text into polished English for journal submission. |
| `polish-skill` | `Polish this paragraph` / `润色这段英文` | Polish English academic text through quick-fix or guided multi-pass workflow. |
| `de-ai-skill` | `De-AI this paragraph` / `降AI这段论文` | Detect and rewrite AI-generated patterns in English academic text. |
| `reviewer-simulation-skill` | `Review this paper` / `审稿这篇论文` | Simulate peer review with structured bilingual feedback report. |

**Support Tools**

| Skill | Trigger Examples | Description |
|-------|-----------------|-------------|
| `abstract-skill` | `Write an abstract for my paper` / `帮我写摘要` | Generate or optimize abstracts using the 5-sentence Farquhar formula. |
| `literature-skill` | `Find papers about urban heat island` / `帮我找关于城市热岛的文献` | Search literature and generate verified BibTeX entries. ⚠️ Requires Semantic Scholar MCP |
```

### Quick Start Scenario Block

```markdown
### Scenario 1: Paper Submission Chain

1. **translation-skill** — Translate your Chinese draft into academic English with LaTeX output.
2. **polish-skill** — Polish the English text for journal submission style.
3. **de-ai-skill** — Scan for AI-generated patterns and rewrite flagged passages.
4. **reviewer-simulation-skill** — Get a structured peer review report before submission.
```

### Installation One-Liner Block

```markdown
## Installation

Paste this repository's GitHub URL into Claude Code and say:

> "Please install the skills from this repository to my `.claude/skills/` directory."

Claude Code will clone the repository and copy each Skill directory into `.claude/skills/`.

### Semantic Scholar MCP Setup (for literature-skill)

literature-skill requires the Semantic Scholar MCP server. To enable it:
1. Open Claude Code settings.
2. Navigate to MCP servers.
3. Add the Semantic Scholar MCP server configuration.
4. Restart Claude Code.
```

---

## Validation Architecture

### Test Framework

| Property | Value |
|----------|-------|
| Framework | Manual verification only — this phase produces a markdown file |
| Config file | none |
| Quick run command | Visual inspection of README.md in GitHub preview |
| Full suite command | Manual checklist review against success criteria |

### Phase Requirements → Test Map

| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| DOCS-01 | README contains installation instructions | manual | n/a — markdown visual review | ❌ Wave 0 |
| DOCS-01 | README contains Skill inventory table (all 11 skills) | manual | n/a — markdown visual review | ❌ Wave 0 |
| DOCS-01 | README contains quick start guide (3 scenarios) | manual | n/a — markdown visual review | ❌ Wave 0 |
| DOCS-01 | README is bilingual (English + Chinese) | manual | n/a — markdown visual review | ❌ Wave 0 |
| DOCS-01 | literature-skill row has ⚠️ annotation | manual | n/a — markdown visual review | ❌ Wave 0 |

**Justification for manual-only:** This phase produces a single markdown documentation file. There is no executable code, no imports, no test harness applicable. All verification is structural inspection of the output file.

### Sampling Rate

- **Per task commit:** Visual scan of README.md in rendered GitHub markdown preview
- **Per wave merge:** Full checklist pass against CONTEXT.md decisions
- **Phase gate:** All five DOCS-01 behaviors confirmed present before `/gsd:verify-work`

### Wave 0 Gaps

None — no test infrastructure needed. Verification is human visual review of the output file against the checklist in success criteria.

---

## Open Questions

1. **GitHub repository URL for the installation one-liner**
   - What we know: The installation section should tell users to paste the repo URL to Claude Code
   - What's unclear: The actual GitHub URL (username/repo) — the LICENSE file shows copyright year 2025 but no repo URL is present in any file
   - Recommendation: Write the instruction generically as "Paste this repository's GitHub URL" — the reader is already on GitHub when they see the README, so the URL is implicit. Alternatively, use a placeholder `https://github.com/YOUR_USERNAME/paper-polish-workflow` and note this in the plan as a task to fill in.

2. **Table of contents**
   - What we know: Per CONTEXT.md, whether to add a TOC is Claude's discretion
   - What's unclear: Long bilingual README benefits from TOC for navigation, but adds line count
   - Recommendation: Include a minimal TOC with anchor links to English and Chinese halves only (2 links), not a deep section tree. This matches standard GitHub bilingual README patterns.

---

## Sources

### Primary (HIGH confidence)
- All 11 SKILL.md files in `.claude/skills/` — directly read; all descriptions and trigger examples confirmed
- `.planning/phases/10-documentation/10-CONTEXT.md` — all structural decisions are locked
- `.planning/REQUIREMENTS.md` — DOCS-01 requirement confirmed
- Existing `README.md` at repo root — read to identify content to be overwritten

### Secondary (MEDIUM confidence)
- GitHub Shields.io badge format (`img.shields.io`) — widely-used standard badge service, no verification needed for this task

### Tertiary (LOW confidence)
- None

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — pure markdown, no libraries, no tooling decisions required
- Architecture: HIGH — structure fully specified in locked decisions; source data fully collected from SKILL.md files
- Pitfalls: HIGH — all pitfalls derived directly from known project constraints and existing file state

**Research date:** 2026-03-12
**Valid until:** Stable indefinitely — this is a one-time documentation task with no moving targets
