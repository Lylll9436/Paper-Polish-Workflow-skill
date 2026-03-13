# Phase 8: Figure/Table and Logic Skills - Research

**Researched:** 2026-03-12
**Domain:** Skill authoring — caption generation (SECT-02) and logic verification (SECT-04)
**Confidence:** HIGH

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions

**Caption Skill — execution paths**
- Support BOTH Generate and Optimize paths (dual-path, same as Abstract Skill pattern)
- Generate path: user provides figure/table content description → new caption produced
- Optimize path: user provides existing weak caption + content description → improved caption produced
- Path selected by trigger inference or Ask Strategy pre-question (if ambiguous)

**Caption Skill — Figure vs Table distinction**
- Distinguish processing logic for figures and tables:
  - Figure captions: describe visual layers, spatial/geometric properties, legend summary, data source
  - Table captions: describe row/column semantics, statistical context, units, data source
- User specifies type (Figure or Table) during Ask Strategy; not inferred silently

**Caption Skill — output**
- Output written directly to the user's .tex file (not conversation-only)
- User must provide the target .tex file path
- Caption appended/inserted at the appropriate LaTeX `\caption{}` command location
- Consistent with Translation Skill's file-write pattern

**Caption Skill — input modes**
- Two input modes: file (CSV, TXT, data table) and pasted text description
- Both modes accepted; Skill infers content from either

**Caption Skill — geography metadata**
- Ask Strategy phase proactively asks for key geography metadata:
  - Figure type (map / chart / diagram / photo?)
  - Study area / city name (if spatial)
  - Data source brief (e.g., OpenStreetMap, Landsat-8, Census 2020)
  - CRS only if user knows it and it's relevant to the figure type
- Caption should include: study area / city name AND data source brief when provided
- CRS notation, legend items, scale bar: ask proactively but only include if user provides them
- Missing metadata: skip gracefully — generate from what user provides, do NOT use [MISSING: ...] placeholders or refuse

**Caption Skill — journal adaptation**
- When target journal specified: load journal template and adapt caption length and style
- Missing journal template → refuse execution (consistent with Translation/Polish/De-AI/Reviewer Skills)

**Logic Verification Skill — verification scope**
- Verify ALL FOUR issue types:
  1. Cross-section argument chain: Does Introduction claim get supported by Methods? Do Results support Discussion conclusions?
  2. Unsupported claims: Statements that assert but provide no evidence or citation
  3. Terminology inconsistency: Same concept named differently across sections
  4. Number/conclusion contradictions: Numbers in Introduction differ from Results, or Conclusion contradicts Discussion

**Logic Verification Skill — input**
- Full paper required — no partial-section input accepted
- Partial sections → refuse with explanation: "Cross-section verification requires the full paper"
- Supports both file path and pasted text input

**Logic Verification Skill — output behavior**
- Identify problems + directional suggestions only — no rewritten text
- Each issue: problem description + why it's a problem + one-sentence directional suggestion
- Section reference format: section title in brackets, e.g., `[Introduction]`, `[Methods, 第3段]`
- Does NOT produce full rewrites (that's Polish Skill's domain)

**Logic Verification Skill — report structure (two-part)**
- Part 1 — Argument Chain View: Maps each section's primary claim in sequence (Introduction → Methods → Results → Discussion → Conclusion). Shows how claims connect or fail to connect.
- Part 2 — Categorized Issue List: Issues grouped by type (Argument Chain Gap / Unsupported Claim / Terminology Inconsistency / Number Contradiction), each with section reference and directional suggestion

**Logic Verification Skill — bilingual output**
- English issue descriptions with inline Chinese blockquote translation after each issue
- Same pattern as Reviewer Simulation Skill
- Argument Chain View: English only (structural, not language-sensitive)

**Logic Verification Skill — output file**
- Written to Markdown file: `{input_filename_without_ext}_logic.md`
- For pasted text input: presented in conversation (no file to write to)
- Consistent with Reviewer Simulation's `*_review.md` naming convention

**Shared decisions (carry-forward from prior phases)**
- ~300 line hard budget per SKILL.md
- Bilingual Chinese/English triggers in frontmatter `examples` field
- Direct mode as default
- Anti-AI patterns NOT loaded by default (review/analysis Skills, not writing Skills)
- Both Skills follow skill-conventions.md frontmatter contract exactly

### Claude's Discretion

None recorded — all implementation details were locked in the discussion.

### Deferred Ideas (OUT OF SCOPE)

None — discussion stayed within Phase 8 boundary.
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| SECT-02 | Figure and table caption Skill generates or optimizes captions with geography-specific awareness (study area, CRS, legend description) | Caption Skill follows Abstract Skill dual-path pattern; geography metadata handled via Ask Strategy proactive questions; geography-domain.md reference exists and is directly loadable |
| SECT-04 | Logic verification Skill checks argument chains, identifies gaps, inconsistencies, and unsupported claims across sections | Logic Skill follows Reviewer Simulation two-part report structure and bilingual pattern; four issue types map cleanly to the structured Categorized Issue List in Part 2 |
</phase_requirements>

---

## Summary

Phase 8 builds two new Skills — Caption Skill (SECT-02) and Logic Verification Skill (SECT-04) — that follow patterns already fully established in the project. No new architectural concepts are introduced; both Skills are composites of existing patterns from the six completed Skills.

The Caption Skill is essentially the Abstract Skill's dual-path pattern (generate/optimize) applied to LaTeX caption authoring, with a geography-aware Ask Strategy layer. Its unique requirements are: figure-vs-table branching logic, geography metadata collection (study area, data source, CRS), journal-adaptive caption length, and file output using the Write tool to insert into a `.tex` file. The geography-domain.md reference leaf is already authored and directly loadable.

The Logic Verification Skill is closest to the Reviewer Simulation Skill in structure. The key difference is that it analyzes logical coherence rather than review criteria, produces a two-part report (Argument Chain View + Categorized Issue List) instead of a scoring table, and refuses partial input with a clear guard message. Bilingual output using inline blockquotes follows exactly the Reviewer Simulation pattern.

**Primary recommendation:** Author Caption Skill (Plan 08-01) first as it is simpler (dual-path + Ask Strategy extension). Author Logic Skill (Plan 08-02) second as it has more complex report structure. Both deliver one SKILL.md file and one validation task each.

---

## Standard Stack

### Core

| Asset | Version | Purpose | Why Standard |
|-------|---------|---------|--------------|
| `references/skill-conventions.md` | Current | Frontmatter contract, section requirements, line budget | Required by all Skills — defines the authoring contract |
| `references/skill-skeleton.md` | Current | Copyable starting template | Canonical starting point for all new SKILL.md files |
| `references/expression-patterns/geography-domain.md` | Current | Geography-specific captions (study area, spatial framing, planning language) | Directly relevant to Caption Skill spatial figures; leaf already exists |
| `references/journals/ceus.md` | Current | Caption length and style rules for CEUS journal | Caption Skill loads this when journal specified |
| `.claude/skills/abstract-skill/SKILL.md` | Current | Structural baseline for Caption Skill dual-path pattern | Directly mirrors generate/optimize path structure |
| `.claude/skills/reviewer-simulation-skill/SKILL.md` | Current | Structural baseline for Logic Skill bilingual two-part report | Directly mirrors two-part report + inline Chinese blockquote pattern |

### Supporting

| Asset | Version | Purpose | When to Use |
|-------|---------|---------|-------------|
| `references/expression-patterns.md` | Current | Overview entrypoint for expression references | Caption Skill loads overview; leaf loaded conditionally |
| `.claude/skills/de-ai-skill/SKILL.md` | Current | Two-phase detect-then-act workflow reference | Logic Skill borrows the "refuse partial input" guard and phase-separation model |
| `.claude/skills/experiment-skill/SKILL.md` | Current | Phase 1 confirmation gate pattern | Logic Skill may reference the "present finding list → user confirm → proceed" model |

### No Installation Required

```bash
# This phase is markdown-only — no package installation needed.
# All referenced files already exist in the project.
```

---

## Architecture Patterns

### Recommended File Structure

```
.claude/skills/
├── caption-skill/
│   └── SKILL.md          # Phase 8, Plan 08-01 — SECT-02
└── logic-skill/
    └── SKILL.md          # Phase 8, Plan 08-02 — SECT-04
```

### Pattern 1: Caption Skill — Dual-Path with Geography Ask Strategy

**What:** Mirrors the Abstract Skill's generate/optimize path structure. Path inference uses trigger content + explicit Ask Strategy pre-question if ambiguous. The unique addition is a proactive geography metadata block in Ask Strategy.

**When to use:** Any Skill where two parallel content-generation paths share the same output format.

**Frontmatter shape:**
```yaml
---
name: caption-skill
description: >-
  Generate or optimize figure/table captions for academic papers.
  Geography-aware: study area, CRS notation, data source.
  Writes output to .tex file. 图表标题生成与优化。
triggers:
  primary_intent: generate or optimize figure/table caption
  examples:
    - "Write a caption for my figure"
    - "帮我写图表标题"
    - "Optimize this table caption"
    - "优化我的图表说明"
    - "Generate a CEUS-ready figure caption"
    - "帮我生成符合期刊要求的图题"
tools:
  - Read
  - Write
  - Structured Interaction
references:
  required:
    - references/expression-patterns.md
  leaf_hints:
    - references/expression-patterns/geography-domain.md
input_modes:
  - file
  - pasted_text
output_contract:
  - caption_latex
---
```

**Ask Strategy geography block (before main workflow):**
1. Figure type: map / chart / diagram / photo / table? (determines whether spatial questions apply)
2. Study area / city name (if spatial — e.g., "Beijing Chaoyang District")
3. Data source brief (e.g., "OpenStreetMap 2023", "Landsat-8")
4. CRS (ask only if user knows it AND figure type is a map)
5. Legend items (ask only if figure type is a map or chart with a legend)

Rules:
- Maximum 3 questions before proceeding
- If figure type is NOT spatial (e.g., a bar chart or schematic diagram): skip geography questions entirely
- Missing metadata: generate from what is provided; do NOT insert [MISSING: ...] placeholders or refuse

**Figure vs Table branching logic (in Workflow):**

| Type | Caption Focus |
|------|--------------|
| Map | Spatial extent, CRS (if provided), data source, scale bar mention |
| Chart/Diagram | X/Y axes or variables described, trend or comparison purpose, data source |
| Photo | Subject, location (if provided), data source or photographer credit |
| Table | Row/column semantics, statistical context, units, data source |

**LaTeX output format:**
```latex
\caption{[Study area / subject]. [Key content description]. [Data source statement].}
```
- Insert at appropriate `\caption{}` location in the user's .tex file using Write tool
- Consistent with Translation Skill's file-write pattern
- If Write tool fails: present caption in conversation instead

### Pattern 2: Logic Skill — Two-Part Report with Full-Paper Guard

**What:** Single-pass direct workflow that reads the full paper, analyzes four issue types, and produces a two-section Markdown report. Mirrors the Reviewer Simulation Skill's structure but replaces the scoring table with an Argument Chain View.

**Frontmatter shape:**
```yaml
---
name: logic-skill
description: >-
  Verify logical consistency across paper sections.
  Traces argument chains and identifies gaps, unsupported claims,
  terminology inconsistencies, and number contradictions.
  论文逻辑验证，识别论证链断裂、无支撑声明、术语不一致、数字矛盾。
triggers:
  primary_intent: verify logical consistency of academic paper
  examples:
    - "Check the logic of my paper"
    - "检查我的论文逻辑"
    - "Verify argument chain across sections"
    - "验证论文各章节论证是否自洽"
    - "Find logical inconsistencies in my draft"
    - "帮我找论文里的逻辑问题"
tools:
  - Read
  - Write
  - Structured Interaction
references:
  required: []
  leaf_hints: []
input_modes:
  - file
  - pasted_text
output_contract:
  - logic_report
---
```

Note: Logic Skill does NOT load expression pattern leaves (analysis task, not writing task). No anti-AI patterns loaded (consistent with Reviewer Simulation convention).

**Two-part report structure (locked format):**

```markdown
# Logic Verification Report

**Paper:** [title or filename]
**Date:** [date]

## Part 1 — Argument Chain View

| Section | Primary Claim | Connects To | Status |
|---------|--------------|-------------|--------|
| Introduction | [claim] | Methods | Connected / Gap |
| Methods | [claim] | Results | Connected / Gap |
| Results | [claim] | Discussion | Connected / Gap |
| Discussion | [claim] | Conclusion | Connected / Gap |
| Conclusion | [claim] | — | Terminal |

## Part 2 — Categorized Issue List

### Argument Chain Gaps

**Issue AC-1: [Descriptive Title]**
**Section:** [Introduction]
**Problem:** [description]
**Why this matters:** [impact on argument coherence]
**Suggestion:** [one-sentence directional suggestion]
> **[中文]** [Chinese translation of problem, why, suggestion]

### Unsupported Claims

**Issue UC-1: [Descriptive Title]**
[same three-part structure with inline Chinese blockquote]

### Terminology Inconsistencies

**Issue TI-1: [Descriptive Title]**
[same three-part structure with inline Chinese blockquote]

### Number Contradictions

**Issue NC-1: [Descriptive Title]**
[same three-part structure with inline Chinese blockquote]
```

**Issue prefix codes:** AC = Argument Chain Gap, UC = Unsupported Claim, TI = Terminology Inconsistency, NC = Number Contradiction. Consecutive numbering within each category.

**Bilingual rule (from Reviewer Simulation):**
- Each issue's three-part English text followed immediately by `> **[中文]** ...` blockquote
- Argument Chain View table: English only (structural, not language-sensitive)
- Preserve technical terms precisely in Chinese (e.g., "ablation study" → "消融实验")

**Full-paper guard (before main workflow):**
- If input appears to be only one or two sections: refuse with "Cross-section verification requires the full paper. Please provide the complete manuscript."
- Minimum length guidance: < 500 words is likely a single section

### Anti-Patterns to Avoid

- **Silently inferring figure type:** Always ask explicitly before branching to figure or table logic — the user knows their content type.
- **Geography questions for non-spatial figures:** Skip study area / CRS questions entirely when figure type is a bar chart, line chart, or schematic.
- **Missing metadata placeholders in captions:** Caption Skill must generate from available content, not insert [MISSING: CRS] stubs that would appear verbatim in the paper.
- **Partial paper logic verification:** Never attempt to verify argument chains from incomplete input — the cross-section relationships are the entire point.
- **Rewriting issues in the Logic Skill:** Logic Skill identifies and suggests only; any rewriting belongs to the Polish Skill.
- **Inventing terminology for logic issues:** When noting a terminology inconsistency, quote the exact terms from the paper verbatim; do not paraphrase.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Dual-path (generate/optimize) | Custom path-selection logic | Abstract Skill pattern | Already proven; same inference rules apply verbatim |
| Bilingual inline blockquotes | New formatting convention | Reviewer Simulation `> **[中文]**` pattern | Established convention ensures consistency across Skills |
| File output to .tex | Custom write-and-insert logic | Write tool (same as Translation Skill) | Consistent with project file-write pattern |
| Journal-missing refusal | Per-Skill custom logic | "Journal template for [X] not found. Available: CEUS." | Locked project-wide refusal message |
| Section reference format | Ad hoc formatting | `[Section Title]` or `[Section Title, 第N段]` | Consistent with CONTEXT.md locked decision |
| Output filename | Custom naming | `{input_filename_without_ext}_logic.md` | Mirrors `*_review.md` convention from Phase 6 |

**Key insight:** Phase 8 has zero new infrastructure requirements. Every pattern is a recombination of existing, proven Skill patterns.

---

## Common Pitfalls

### Pitfall 1: Caption Skill asks geography questions for non-spatial figures

**What goes wrong:** The Skill asks "What is the study area?" for a bar chart showing model accuracy comparison.
**Why it happens:** Geography Ask Strategy is written as an unconditional block.
**How to avoid:** The Ask Strategy must branch on figure type FIRST. Only if figure type is map, photo, or spatial diagram do the geography questions apply.
**Warning signs:** Ask Strategy lists geography questions without a conditional gate.

### Pitfall 2: Caption Skill uses [MISSING: ...] placeholders for optional metadata

**What goes wrong:** When the user does not know the CRS, the output caption contains `[MISSING: CRS]` which then appears verbatim in their LaTeX file.
**Why it happens:** Copying the Abstract Skill's [MISSING: ...] convention without reading the CONTEXT.md "skip gracefully" rule.
**How to avoid:** For Caption Skill, missing optional metadata = skip the clause entirely. Only Abstract Skill and Experiment Skill use [MISSING: ...] flags; Caption Skill explicitly does not.
**Warning signs:** Any [MISSING: ...] placeholder appears in caption output or output contract description.

### Pitfall 3: Logic Skill attempts cross-section analysis on one section

**What goes wrong:** User pastes only the Introduction. Skill attempts to verify "argument chain" but has no other sections to cross-reference, producing hallucinated connections.
**Why it happens:** No full-paper guard at input collection step.
**How to avoid:** Step 1 of Logic Skill Workflow must include a guard: if input appears to be partial (single section headings, too short, no Methods/Results/Discussion markers), refuse with the locked message.
**Warning signs:** No input guard in Workflow Step 1.

### Pitfall 4: Argument Chain View table claims connections that don't exist

**What goes wrong:** The Argument Chain View shows "Connected" for Introduction → Methods when the Methods section never addresses the Introduction's stated research question.
**Why it happens:** The Skill generates an optimistic table before running detailed issue analysis.
**How to avoid:** Generate the Argument Chain View LAST (after identifying issues in Part 2), not first. The Status column is derived from the issues found.
**Warning signs:** The Workflow generates Part 1 before running issue analysis.

### Pitfall 5: Caption Skill writes to wrong location in .tex file

**What goes wrong:** The caption is appended to the end of the file instead of inserted at the `\caption{}` location.
**Why it happens:** Write tool used without Read-first to locate the insertion point.
**How to avoid:** Always Read the .tex file first, locate the `\caption{}` or `\caption[]{} ` command, and then Write the updated file with the caption inserted at that location.
**Warning signs:** Workflow does not include a Read step before Write for .tex output.

### Pitfall 6: Logic Skill issue descriptions paraphrase rather than quote

**What goes wrong:** Issue TI-1 says "The paper uses different words for the same concept" without quoting what those words are.
**Why it happens:** Generic issue description pattern not grounded in actual paper text.
**How to avoid:** Every terminology inconsistency issue must quote the exact terms verbatim (e.g., "Model A [Introduction, 第2段] vs. the proposed model [Methods, 第1段] vs. our approach [Discussion, 第3段]").
**Warning signs:** Issue descriptions use abstract language without quoting specific passages.

---

## Code Examples

Verified patterns adapted from official project Skills:

### Caption Skill Ask Strategy (geography-conditional)

```markdown
## Ask Strategy

**Before starting, ask about:**
1. Figure or table type: map / chart / diagram / photo / data table?
   (Required — determines whether spatial metadata questions apply)
2. Target journal if not specified (ask once; if declined, use general academic style)
3. **If figure type is spatial (map, photo, GIS output):**
   - Study area / city name
   - Data source brief (e.g., OpenStreetMap 2023, Landsat-8 imagery)
   - CRS (ask only if user knows it; skip if uncertain)
   - Key legend items (ask only if map has a visible legend)
4. **If figure type is non-spatial (bar chart, line chart, schematic):**
   Skip geography questions. Ask only for data source if not mentioned.

**Rules:**
- Never ask more than 3 questions before proceeding
- Missing spatial metadata: generate from available content; do NOT insert [MISSING: ...] stubs
- In direct mode with sufficient context, proceed without pre-questions
```

### Caption Skill Output Contract (LaTeX)

```markdown
## Output Contract

| Output | Format | Condition |
|--------|--------|-----------|
| `caption_latex` | LaTeX `\caption{...}` string inserted into .tex file | Always — file input |
| `caption_latex` | LaTeX `\caption{...}` string in conversation | Fallback — Write tool fails or pasted text input |

**LaTeX caption format examples:**

Figure (spatial):
```latex
\caption{Distribution of [phenomenon] in [study area]. Data derived from [source]. [CRS/projection note if provided.]}
```

Figure (non-spatial):
```latex
\caption{[Variable/comparison description] across [conditions/groups]. [Data source].}
```

Table:
```latex
\caption{[Statistical measure] of [subject] by [grouping variable]. All values are [unit]. Data from [source].}
```
```

### Logic Skill: Argument Chain View derivation rule

```markdown
### Step 2: Generate Argument Chain View

- Run Step 3 (issue analysis) FIRST.
- Use identified AC-type issues to determine "Gap" status in the table.
- A section pair is "Connected" only when no AC issue spans that pair.
- Then write Part 1 table based on the analysis evidence.

This ordering prevents the table from reflecting wishful connections
rather than actual cross-section support.
```

### Reviewer Simulation bilingual pattern (reused by Logic Skill)

```markdown
**Issue TI-1: "Model A" vs. "the proposed model" vs. "our approach"**
**Section:** [Introduction, 第2段], [Methods, 第1段], [Discussion, 第3段]
**Problem:** Three different terms refer to the same model across sections...
**Why this matters:** Reviewers may interpret these as distinct systems...
**Suggestion:** Standardize on one term (e.g., "Model A") in all sections...
> **[中文]** 问题：论文在不同章节使用了三种不同术语指代同一模型...为什么重要：...建议：...
```
Source: `.claude/skills/reviewer-simulation-skill/SKILL.md` — inline blockquote convention

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Monolithic caption prompts in conversation | Skill with explicit figure/table branching and geography metadata collection | Phase 8 design | Consistent, structured caption quality; no ad hoc prompting |
| Logic checking via general "review this" prompts | Dedicated Logic Skill with four typed issue categories and section references | Phase 8 design | Precise, actionable output instead of vague "improve logic" feedback |
| File output requiring manual copy-paste | Write tool insertion into .tex file | Translation Skill (Phase 3) established | Caption written directly to submission file without user transcription errors |
| English-only issue reports | Bilingual reports with inline Chinese blockquotes | Reviewer Simulation Skill (Phase 6) established | Non-native English readers get issue context in their primary language |

**Deprecated/outdated for this phase:**
- Asking users to manually paste captions back into their .tex file: the Write tool handles this
- Logic issues reported as prose paragraph: the typed category + section reference format is far more actionable

---

## Validation Architecture

`nyquist_validation` is enabled in `.planning/config.json`.

### Test Framework

| Property | Value |
|----------|-------|
| Framework | Structural inspection (no runtime test framework — markdown-only Skills) |
| Config file | none |
| Quick run command | `wc -l .claude/skills/caption-skill/SKILL.md && grep -c "^## " .claude/skills/caption-skill/SKILL.md` |
| Full suite command | See Phase Requirements → Test Map below |

### Phase Requirements → Test Map

| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| SECT-02 | Caption Skill generates captions for figures (spatial + non-spatial) | structural | `grep -n "generate\|optimize\|figure\|table\|geography\|CRS\|caption" .claude/skills/caption-skill/SKILL.md` | ❌ Wave 0 |
| SECT-02 | Caption output written to .tex file | structural | `grep -n "Write\|\.tex\|\\\\caption" .claude/skills/caption-skill/SKILL.md` | ❌ Wave 0 |
| SECT-02 | Journal template refusal pattern present | structural | `grep -c "not found. Available: CEUS" .claude/skills/caption-skill/SKILL.md` | ❌ Wave 0 |
| SECT-02 | Geography metadata ask strategy present | structural | `grep -n "study area\|CRS\|data source\|Ask Strategy" .claude/skills/caption-skill/SKILL.md` | ❌ Wave 0 |
| SECT-02 | Line budget ≤ 300 | structural | `wc -l .claude/skills/caption-skill/SKILL.md` | ❌ Wave 0 |
| SECT-04 | Logic Skill: all 4 issue types documented | structural | `grep -n "Argument Chain\|Unsupported\|Terminology\|Number Contradiction\|AC-\|UC-\|TI-\|NC-" .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 |
| SECT-04 | Two-part report format present | structural | `grep -n "Part 1\|Part 2\|Argument Chain View\|Categorized Issue" .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 |
| SECT-04 | Full-paper guard present | structural | `grep -n "requires the full paper\|partial" .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 |
| SECT-04 | Bilingual blockquote pattern present | structural | `grep -n "\[中文\]\|\[Chinese\]" .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 |
| SECT-04 | Output filename convention _logic.md | structural | `grep -n "_logic\.md\|logic\.md" .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 |
| SECT-04 | Line budget ≤ 300 | structural | `wc -l .claude/skills/logic-skill/SKILL.md` | ❌ Wave 0 |

### Sampling Rate

- **Per task commit:** `wc -l .claude/skills/caption-skill/SKILL.md && grep -c "^## " .claude/skills/caption-skill/SKILL.md`
- **Per wave merge:** Full suite above for both Skills
- **Phase gate:** All structural checks pass before `/gsd:verify-work`

### Wave 0 Gaps

- [ ] `.claude/skills/caption-skill/SKILL.md` — covers SECT-02
- [ ] `.claude/skills/logic-skill/SKILL.md` — covers SECT-04

*(Both files are the deliverables of this phase — they are created in Wave 0 execution.)*

---

## Open Questions

1. **Argument Chain View ordering: write Part 1 before or after Part 2 issue analysis?**
   - What we know: CONTEXT.md specifies the report "shows how claims connect or fail to connect" — this is diagnostic output derived from issue analysis.
   - What's unclear: Whether the report presents Part 1 first (table) or Part 2 first (issues), even though the table is derived last.
   - Recommendation: Workflow runs issue analysis first, then populates the table. In the final report, Part 1 appears first (better readability). This is an internal ordering concern, not a user-visible one.

2. **Caption Skill — What if the user's .tex file has multiple `\caption{}` commands?**
   - What we know: CONTEXT.md says "appended/inserted at the appropriate LaTeX `\caption{}` command location" — implies single caption per invocation.
   - What's unclear: How the Skill identifies which `\caption{}` to target when multiple figures exist in the file.
   - Recommendation: Ask Strategy should include "Which figure/table label (e.g., `\label{fig:study_area}`) is the target?" or ask for the line number range. Document in Edge Cases.

3. **Logic Skill — section reference format for papers without numbered sections?**
   - What we know: Format is `[Section Title]` or `[Section Title, 第N段]`.
   - What's unclear: Papers with Chinese section titles — should references use the Chinese title or translate to English?
   - Recommendation: Use the section title as it appears in the paper. If Chinese paper → Chinese section title. If English paper → English section title. Document in Edge Cases.

---

## Sources

### Primary (HIGH confidence)

- `.planning/phases/08-figure-table-and-logic-skills/08-CONTEXT.md` — all locked decisions for Phase 8
- `.planning/REQUIREMENTS.md` — SECT-02 and SECT-04 requirement definitions
- `.claude/skills/abstract-skill/SKILL.md` — dual-path pattern baseline for Caption Skill
- `.claude/skills/reviewer-simulation-skill/SKILL.md` — two-part report and bilingual blockquote baseline for Logic Skill
- `.claude/skills/de-ai-skill/SKILL.md` — full-paper guard and two-phase workflow reference
- `references/skill-conventions.md` — frontmatter contract, section requirements, line budget rules
- `references/skill-skeleton.md` — copyable frontmatter template
- `references/expression-patterns/geography-domain.md` — confirmed geography leaf exists with spatial framing patterns
- `references/journals/ceus.md` — confirmed journal template exists with caption length context

### Secondary (MEDIUM confidence)

- `.planning/phases/07-abstract-and-experiment-skills/07-01-PLAN.md` — plan structure and task format to replicate for Phase 8 plans
- `.planning/STATE.md` — project decisions log confirming established conventions (refusal pattern, file-write pattern, bilingual pattern)
- `.claude/skills/experiment-skill/SKILL.md` — Phase 1/Phase 2 confirmation gate reference

### Tertiary (LOW confidence — needs validation)

- None — Phase 8 is governed by local project contracts and existing Skills; no external dependencies.

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — all referenced files confirmed to exist; no new libraries required
- Architecture patterns: HIGH — both Skills are composites of proven existing patterns
- Common pitfalls: HIGH — identified directly from cross-pattern analysis of existing Skills and CONTEXT.md constraints
- Code examples: HIGH — derived directly from existing SKILL.md files and locked CONTEXT.md decisions
- Validation architecture: HIGH — structural grep checks are deterministic for markdown-only Skills

**Research date:** 2026-03-12
**Valid until:** 2026-04-12 (stable project; no external dependencies to expire)

---

*Phase: 08-figure-table-and-logic-skills*
*Research completed: 2026-03-12*
*Ready for planning: yes*
