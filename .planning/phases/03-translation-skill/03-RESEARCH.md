# Phase 3: Translation Skill - Research

**Researched:** 2026-03-11
**Domain:** Chinese-to-English academic translation as a Claude Code Skill
**Confidence:** HIGH

## Summary

Phase 3 builds the first real Claude Code Skill in this project: a Chinese-to-English academic translation Skill that accepts Chinese draft text (file or pasted) and outputs polished English academic text in LaTeX format. The Skill must follow the conventions established in Phase 2 (YAML frontmatter, ~300 line limit, progressive disclosure) and load references from Phase 1 (expression patterns, journal templates) on demand.

This is primarily a prompt engineering and Skill authoring task, not a software development task. The deliverable is a single `SKILL.md` file at `.claude/skills/translation-skill/SKILL.md` that instructs Claude how to perform academic translation. The complexity lies in encoding the right translation workflow, glossary support, section-aware expression pattern loading, journal-aware style adaptation, and dual-output generation (English-only + bilingual comparison) within the 300-line budget.

**Primary recommendation:** Copy the skeleton from `references/skill-skeleton.md`, fill in all sections following `references/skill-conventions.md`, and focus the Workflow section on the single-pass translation flow with self-check, glossary integration, and dual file output.

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- Single-pass direct translation (no multi-round rough-then-refine pipeline)
- After translation, append a self-check summary: terminology handling notes, potential issues, uncertain translations
- Output written to new files, preserving original input unchanged
- Always produce bilingual paragraph-by-paragraph comparison alongside English-only version
- Input granularity: paragraph or section level (not full-paper at once)
- Input sources: file path or pasted text (two modes)
- Mixed Chinese-English text: translate everything uniformly (do not preserve inline English terms as-is)
- Support user-provided custom glossary file (term mapping)
- Output preserves existing LaTeX formula commands and \cite{} commands as-is
- Bilingual format: paragraph-by-paragraph interleaved (Chinese then English)
- File naming: `input.md` to `input_en.tex` + `input_bilingual.tex`
- Journal adaptation: load journal template and adjust style; refuse if specified journal has no template
- Auto-load relevant expression pattern leaf modules based on content section type
- If specified journal has no template file: refuse translation and suggest user add template first

### Claude's Discretion
- Exact self-check summary format and level of detail
- How to detect which expression pattern module is most relevant for a given input
- Internal translation strategy (sentence-by-sentence vs. paragraph-level)

### Deferred Ideas (OUT OF SCOPE)
None -- discussion stayed within phase scope.
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| CORE-01 | Chinese-to-English translation Skill accepts Chinese draft and outputs polished English academic text in LaTeX format | Entire research supports this: Skill structure, workflow design, reference loading, output format, and glossary handling |
</phase_requirements>

## Standard Stack

### Core

This is a Skill authoring phase, not a library installation phase. The "stack" is the project's own conventions and references.

| Asset | Path | Purpose | Why Standard |
|-------|------|---------|--------------|
| Skill Conventions | `references/skill-conventions.md` | Defines frontmatter contract, body structure, line budget | Phase 2 established this as mandatory for all Skills |
| Skill Skeleton | `references/skill-skeleton.md` | Copyable starting point | Phase 2 deliverable; saves authoring time |
| Expression Patterns | `references/expression-patterns.md` + leaf files | Academic writing patterns by section type | Phase 1 deliverable; Translation Skill must load on demand |
| CEUS Template | `references/journals/ceus.md` | Journal-specific style rules | Phase 1 deliverable; first supported journal |
| Anti-AI Patterns | `references/anti-ai-patterns.md` | Avoid AI-sounding output | Phase 1 deliverable; translation should avoid AI patterns proactively |

### Supporting

| Asset | Purpose | When to Use |
|-------|---------|-------------|
| `references/expression-patterns/introduction-and-gap.md` | Introduction section expressions | When input is introduction content |
| `references/expression-patterns/methods-and-data.md` | Methods section expressions | When input is methods content |
| `references/expression-patterns/results-and-discussion.md` | Results/discussion expressions | When input is results or discussion content |
| `references/expression-patterns/conclusions-and-claims.md` | Conclusion expressions | When input is conclusion content |
| `references/expression-patterns/geography-domain.md` | Geography-specific phrasing | When content involves spatial/urban topics |

## Architecture Patterns

### Project Structure

```
.claude/skills/translation-skill/
  SKILL.md              # The Skill file (~300 lines)
```

That is the entire deliverable. The Skill references files in `references/` at runtime; it does not duplicate them.

### Pattern 1: Section-Aware Expression Loading

**What:** The Skill detects which section the input belongs to (introduction, methods, results, discussion, conclusion) and loads the matching expression pattern leaf module.

**When to use:** Every translation invocation.

**Detection heuristics (Claude's Discretion area):**
- Keyword scanning in Chinese input: look for section markers like "引言", "研究方法", "数据", "结果", "讨论", "结论"
- If input contains study area description or data source description: load `methods-and-data.md` + `geography-domain.md`
- If input discusses findings with numbers/statistics: load `results-and-discussion.md`
- If input opens with background/motivation: load `introduction-and-gap.md`
- If uncertain: load the overview `expression-patterns.md` and use Quick Picks

**Recommended approach for the Skill body:**
```markdown
### Reference Selection Logic

1. Scan the input for section keywords (引言, 方法, 数据, 结果, 讨论, 结论).
2. If a clear match is found, load the corresponding leaf module directly.
3. If the input spans multiple sections or is ambiguous, load the overview
   and select patterns from Quick Picks.
4. Always load `geography-domain.md` when the input mentions spatial concepts,
   cities, districts, or planning-related terms.
5. When a target journal is specified, load its template alongside.
```

### Pattern 2: Glossary Integration

**What:** User provides a glossary file with term mappings (Chinese to English). The Skill loads and prioritizes these mappings during translation.

**Format recommendation for glossary file:**
```
# Custom Glossary
城市热岛 -> urban heat island
建成环境 -> built environment
街景图像 -> street view imagery
空间自相关 -> spatial autocorrelation
```

**Workflow integration:** Load glossary at Step 1 (Collect Context), apply during Step 2 (Translate). When a Chinese term matches a glossary entry, use the glossary translation. The self-check should note which glossary terms were applied.

### Pattern 3: Dual Output Generation

**What:** Every translation produces two files: English-only and bilingual comparison.

**English-only format (`input_en.tex`):**
```latex
% Translated from: input.md
% Target journal: CEUS
% Generated: 2026-03-11

The spatial distribution of perceived walkability reveals substantial
variation across central districts and newly developed suburban communities.

\begin{equation}
  UHI = T_{urban} - T_{rural}
\end{equation}

As shown in previous studies~\cite{Li2023}, ...
```

**Bilingual format (`input_bilingual.tex`):**
```latex
% Bilingual comparison: input.md
% Format: Chinese paragraph followed by English translation

% --- Paragraph 1 ---
% [ZH] 城市热岛效应是城市化进程中的重要环境问题。
% [EN]
The urban heat island effect is a critical environmental issue
in the urbanization process.

% --- Paragraph 2 ---
% [ZH] 本研究利用遥感数据分析了...
% [EN]
This study analyzes ... using remote sensing data.
```

**Key design decision:** Chinese text in bilingual version should be in LaTeX comments (`%`) so the file compiles as valid LaTeX showing only the English text, but the Chinese is visible when reading the source.

### Pattern 4: Self-Check Summary

**What:** After translation, append a summary flagging terminology decisions, uncertain translations, and potential issues.

**Recommended format (Claude's Discretion):**
```markdown
---
## Translation Notes

### Terminology Decisions
- "城市热岛" -> "urban heat island" (from glossary)
- "感知安全性" -> "perceived safety" (standard geography term)

### Uncertain Translations
- "宜居性指数" -> "livability index" — verify if the target journal
  uses "liveability" (British) or "livability" (American)

### Potential Issues
- Paragraph 3 contains a very long sentence (>60 words in Chinese);
  the English version was split into two sentences for readability.
- The \cite{Wang2022} reference appears but was not in the original
  text — preserved as-is from LaTeX source.
```

### Anti-Patterns to Avoid

- **Duplicating expression patterns into SKILL.md:** The Skill must load them at runtime, never inline them. This would blow the 300-line budget.
- **Multi-pass translation pipeline:** The user explicitly locked single-pass translation. Do not design a rough-draft-then-refine workflow.
- **Preserving inline English as-is:** The user decided to re-translate everything uniformly for stylistic consistency. The Skill must not skip English fragments in mixed text.
- **Guessing journal style:** If a journal is specified but no template file exists, refuse and tell the user to add the template first.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Expression patterns | Inline pattern tables in SKILL.md | Load from `references/expression-patterns/*.md` | Patterns are maintained centrally; duplication causes drift |
| Journal style rules | Hardcode CEUS rules in Skill | Load from `references/journals/ceus.md` | Journal contract is the single source of truth |
| Anti-AI vocabulary | Inline avoidance lists | Load from `references/anti-ai-patterns.md` | Same principle; centralized maintenance |
| Glossary format parser | Complex parsing logic | Simple `term -> translation` line format | Keep it trivial; Claude can parse simple delimiter formats |

**Key insight:** This Skill is a prompt document, not code. "Don't hand-roll" means "don't duplicate reference content into the Skill file."

## Common Pitfalls

### Pitfall 1: Exceeding the 300-Line Budget

**What goes wrong:** The translation Skill has many concerns (glossary, journal awareness, dual output, self-check, section detection) that tempt verbose documentation.
**Why it happens:** Trying to encode every edge case and example inline.
**How to avoid:** Keep Workflow steps concise. Use bullet lists, not paragraphs. Rely on references for expression patterns. The Skill tells Claude *what to do*, not *how to translate* (Claude already knows how to translate).
**Warning signs:** Draft exceeds 250 lines before Edge Cases section.

### Pitfall 2: Over-Specifying Translation Rules

**What goes wrong:** The Skill tries to teach Claude translation techniques (sentence splitting, passive voice conversion, etc.) that Claude already handles well.
**Why it happens:** Treating the Skill as a translation textbook instead of a workflow orchestrator.
**How to avoid:** Focus the Skill on *workflow* (what to load, what to ask, what format to output) not *translation technique*. Trust Claude's translation capabilities and constrain them with style rules from journal templates and expression patterns.
**Warning signs:** Skill contains extensive grammar or translation-technique guidance.

### Pitfall 3: Forgetting LaTeX Command Preservation

**What goes wrong:** Translation inadvertently modifies LaTeX formulas, `\cite{}` keys, `\ref{}` labels, or environment names.
**Why it happens:** Not explicitly stating preservation rules for LaTeX syntax.
**How to avoid:** The Skill must state clearly: preserve all `$...$`, `$$...$$`, `\begin{...}...\end{...}`, `\cite{...}`, `\ref{...}`, `\label{...}`, and custom LaTeX commands verbatim. Only translate the natural language text.
**Warning signs:** Test with input containing inline math and citations.

### Pitfall 4: Ambiguous Section Detection

**What goes wrong:** The Skill cannot determine which expression pattern module to load because the input doesn't contain explicit section headers.
**Why it happens:** User pastes a paragraph without context about which paper section it belongs to.
**How to avoid:** In the Ask Strategy, include "which section is this from?" as one of the up-to-3 pre-questions (only when section is ambiguous). In direct mode, default to overview patterns.
**Warning signs:** Expression patterns loaded are mismatched to the content.

### Pitfall 5: File Path Construction for Output

**What goes wrong:** Output file naming fails for edge cases (pasted text has no filename, input path has unusual extension, path contains spaces).
**Why it happens:** The naming rule `input.md -> input_en.tex` assumes a clean filename.
**How to avoid:** Define fallback naming: for pasted text, use `translation_en.tex` and `translation_bilingual.tex` in the current working directory. For file input, strip the extension and append `_en.tex` / `_bilingual.tex`.

## Code Examples

These are Skill content examples, not code. They show what specific SKILL.md sections should look like.

### Frontmatter

```yaml
---
name: translation-skill
description: >-
  Translate Chinese academic text into polished English for journal submission.
  Produces LaTeX output with bilingual comparison. 将中文学术草稿翻译为投稿级英文。
triggers:
  primary_intent: translate Chinese academic draft to English
  examples:
    - "Translate this Chinese draft to English"
    - "翻译这段中文为英文"
    - "Help me translate my paper for CEUS submission"
    - "把这段翻译成学术英文"
tools:
  - Read
  - Write
  - Structured Interaction
references:
  required:
    - references/expression-patterns.md
  leaf_hints:
    - references/expression-patterns/introduction-and-gap.md
    - references/expression-patterns/methods-and-data.md
    - references/expression-patterns/results-and-discussion.md
    - references/expression-patterns/conclusions-and-claims.md
    - references/expression-patterns/geography-domain.md
    - references/anti-ai-patterns.md
input_modes:
  - file
  - pasted_text
output_contract:
  - english_tex
  - bilingual_tex
  - self_check_summary
---
```

### Ask Strategy Section

```markdown
## Ask Strategy

**Before starting, ask about:**
1. Target journal (if not specified in trigger) -- determines style loading
2. Custom glossary file path (if the user has domain-specific term mappings)
3. Which section this text belongs to (if not obvious from content or headers)

**Rules:**
- In `direct` mode, skip pre-questions when the user provides enough context.
- If the user specifies a journal with no template in `references/journals/`,
  refuse and explain: "No template found for [journal]. Please add a template
  at `references/journals/[journal].md` following the CEUS template format."
- Never ask more than 3 questions before producing output.
```

### Workflow Outline

```markdown
## Workflow

### Step 1: Collect Context
- Ask pre-questions (target journal, glossary, section type) per Ask Strategy.
- Load required references: expression patterns overview.
- If target journal specified: Read `references/journals/[journal].md`.
  If file missing, stop and inform user.
- If glossary file specified: Read glossary and build term mapping.
- Select and load the appropriate expression pattern leaf module
  based on section detection logic.
- If content involves geography/urban topics: also load geography-domain.md.

### Step 2: Translate
- Single-pass translation of Chinese input to English.
- Apply journal style preferences (tone, vocabulary, claim calibration).
- Use loaded expression patterns to guide phrasing choices.
- Prioritize glossary terms when a match is found.
- Re-translate all text uniformly including any inline English fragments.
- Preserve all LaTeX commands verbatim: $...$, \begin{}, \end{},
  \cite{}, \ref{}, \label{}, and custom commands.
- Avoid anti-AI vocabulary patterns (high-risk terms from anti-AI library).

### Step 3: Generate Output Files
- Write English-only version to `[name]_en.tex`.
- Write bilingual comparison to `[name]_bilingual.tex`
  (Chinese in % comments, English as body text).
- For pasted text input: use `translation_en.tex` / `translation_bilingual.tex`.
- Append self-check summary to session output (not to the .tex files).

### Step 4: Self-Check
- Report terminology decisions (especially glossary applications).
- Flag uncertain translations with explanation.
- Note any structural changes (sentence splitting, reordering).
- Warn about potential issues (very long sentences, missing references).
```

## State of the Art

| Aspect | Approach | Rationale |
|--------|----------|-----------|
| Translation method | Single-pass with style constraints | User decision; Claude's translation quality is high enough for single-pass |
| Expression guidance | On-demand leaf module loading | Phase 1 architecture; keeps Skill lean |
| Journal adaptation | Template-driven style adjustment | Phase 1 CEUS contract provides the style rules |
| Anti-AI awareness | Proactive avoidance during translation | Better to avoid AI patterns during translation than fix them after |
| Glossary | User-provided simple format | Domain terminology in geography/urban science has established translations |

## Validation Architecture

### Test Framework

| Property | Value |
|----------|-------|
| Framework | Manual validation (Skill is a markdown prompt, not executable code) |
| Config file | None |
| Quick run command | Manual: invoke Skill with test input and verify output |
| Full suite command | Manual: run all test scenarios below |

### Phase Requirements to Test Map

| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| CORE-01a | Skill accepts file path input and produces English LaTeX output | manual | Invoke Skill with a `.md` file containing Chinese text; check `_en.tex` output exists | N/A |
| CORE-01b | Skill accepts pasted text and produces output | manual | Paste Chinese text in session; check `translation_en.tex` output | N/A |
| CORE-01c | Bilingual comparison file is always produced | manual | Check `_bilingual.tex` exists alongside `_en.tex` | N/A |
| CORE-01d | LaTeX commands preserved in output | manual | Input contains `$x^2$`, `\cite{ref}`, `\begin{equation}`; verify preserved in output | N/A |
| CORE-01e | CEUS style applied when specified | manual | Specify CEUS as target; verify output avoids promotional language, uses cautious verbs | N/A |
| CORE-01f | Missing journal template triggers refusal | manual | Specify nonexistent journal; verify Skill refuses with guidance | N/A |
| CORE-01g | Glossary terms prioritized | manual | Provide glossary with "城市热岛 -> urban heat island"; verify term used in output | N/A |
| CORE-01h | Skill follows Phase 2 conventions | manual | Check YAML frontmatter, line count <= 300, required sections present | N/A |
| CORE-01i | Self-check summary produced | manual | Verify translation notes appear after output | N/A |

### Sampling Rate

- **Per task commit:** Manual smoke test with one Chinese paragraph
- **Per wave merge:** Run all 9 test scenarios above
- **Phase gate:** All scenarios pass before `/gsd:verify-work`

### Wave 0 Gaps

- [ ] `.claude/skills/translation-skill/` directory needs to be created
- [ ] Test input files needed: a short Chinese academic paragraph with LaTeX commands, a glossary file, a pasted-text scenario
- [ ] No automated testing possible (Skill is a prompt document consumed by Claude at runtime)

## Open Questions

1. **Expression pattern module auto-detection accuracy**
   - What we know: Chinese section keywords (引言, 方法, 结果, etc.) can signal section type
   - What's unclear: How reliably can content-based detection work when headers are absent
   - Recommendation: Include section type as an Ask Strategy question (question 3), fall back to overview patterns when ambiguous

2. **Anti-AI pattern integration depth**
   - What we know: Translation should proactively avoid AI-sounding vocabulary
   - What's unclear: Whether to load anti-AI reference at translation time or just rely on journal style preferences (which already discourage promotional language)
   - Recommendation: Reference anti-AI patterns in `leaf_hints` and load when doing final vocabulary review in self-check, but do not make it a blocking dependency. The CEUS template's "Avoid" section already covers the most critical anti-AI concerns for that journal.

3. **Glossary file location convention**
   - What we know: User provides a glossary file path
   - What's unclear: Whether there should be a default location or naming convention
   - Recommendation: Accept any path the user provides. Do not enforce a convention. Mention in Examples section that a glossary could live at `glossary.txt` in the project root.

## Sources

### Primary (HIGH confidence)
- `references/skill-conventions.md` -- full Skill authoring contract
- `references/skill-skeleton.md` -- copyable skeleton structure
- `references/expression-patterns.md` + leaf files -- expression pattern library structure and content
- `references/journals/ceus.md` -- CEUS journal contract
- `references/anti-ai-patterns.md` + vocabulary leaf -- anti-AI vocabulary
- `.planning/phases/03-translation-skill/03-CONTEXT.md` -- locked user decisions

### Secondary (MEDIUM confidence)
- Phase 2 plan (`02-01-PLAN.md`) -- confirms conventions were implemented as designed

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH -- all assets are project-internal and already built in Phases 1-2
- Architecture: HIGH -- Skill conventions are fully defined; this Skill follows the established pattern
- Pitfalls: HIGH -- pitfalls are specific to the locked decisions and known project constraints

**Research date:** 2026-03-11
**Valid until:** 2026-04-11 (stable; no external dependencies that change frequently)
