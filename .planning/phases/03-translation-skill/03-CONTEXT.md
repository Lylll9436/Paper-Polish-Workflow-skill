# Phase 3: Translation Skill - Context

**Gathered:** 2026-03-11
**Status:** Ready for planning

<domain>
## Phase Boundary

Build a Chinese-to-English academic translation Skill that accepts Chinese draft text and outputs polished English academic text. The Skill produces both an English-only file and a bilingual paragraph-by-paragraph comparison file. It follows the conventions established in Phase 2 and loads references from Phase 1 on demand.

</domain>

<decisions>
## Implementation Decisions

### Translation workflow
- Single-pass direct translation — no multi-round rough-then-refine pipeline
- After translation, append a self-check summary: terminology handling notes, potential issues, uncertain translations
- Output is written to new files (not displayed inline in session), preserving the original input unchanged
- Always produce a bilingual paragraph-by-paragraph comparison version alongside the English-only version

### Input handling
- Input granularity: paragraph or section level (not full-paper at once)
- Input sources: file path or pasted text (two modes)
- Mixed Chinese-English text: translate everything uniformly (do not preserve inline English terms as-is; re-translate for stylistic consistency)
- Support user-provided custom glossary file (term mapping like "城市热岛" → "urban heat island"); Skill prioritizes glossary translations when available

### LaTeX output format
- Output is plain English text that preserves existing LaTeX formula commands (`$...$`, `\begin{equation}` etc.) untouched
- `\cite{}` and other citation commands are preserved as-is
- Bilingual comparison format: paragraph-by-paragraph interleaved (Chinese paragraph followed by English translation)
- Output file naming based on original filename: `input.md` → `input_en.tex` + `input_bilingual.tex`

### Journal adaptation
- When user specifies a target journal: load the journal template (e.g., `references/journals/ceus.md`) and adjust vocabulary, sentence style, and tone accordingly
- When no journal specified: use generic formal academic English style
- Auto-load relevant expression pattern leaf modules based on content section type (e.g., introduction content → load `introduction-and-gap.md`)
- If specified journal has no template file: refuse translation and suggest user add the journal template first

### Claude's Discretion
- Exact self-check summary format and level of detail
- How to detect which expression pattern module is most relevant for a given input
- Internal translation strategy (whether to mentally parse sentence-by-sentence or paragraph-level)

</decisions>

<specifics>
## Specific Ideas

- The Skill should feel like a professional human translator: one clean pass, not iterative machine-translation refinement
- Self-check summary should flag genuinely uncertain translations, not just repeat what was done
- Journal template loading should happen early in the workflow so style influences the entire translation, not just post-processing
- Glossary support is important for geography/urban science terms that have established translations in the field

</specifics>

<code_context>
## Existing Code Insights

### Reusable Assets
- `references/expression-patterns.md` + leaf modules: organized by writing scenario (introduction, methods, results, conclusions, geography-domain); Translation Skill can auto-load the relevant leaf based on input content type
- `references/journals/ceus.md`: stable journal contract with writing preferences and style guidelines; loadable when CEUS is the target journal
- `references/skill-conventions.md`: defines the frontmatter contract and body structure the Skill must follow
- `references/skill-skeleton.md`: copyable starting point for creating the SKILL.md file

### Established Patterns
- References follow stable-entrypoint + optional-leaf-module structure (Phase 1 decision)
- Skills declare references in frontmatter `references.required` and `references.leaf_hints` (Phase 2 convention)
- Interaction modes: interactive, guided, direct, batch (Phase 2 convention)
- Ask-first posture with fallback to 1-3 plain-text questions (Phase 2 convention)

### Integration Points
- Skill will live at `.claude/skills/translation-skill/SKILL.md`
- Must declare `references.required` pointing to `references/expression-patterns.md` and optionally journal templates
- Output files written alongside or near the input file
- Phase 4 (Polish) and Phase 5 (De-AI) may consume Translation Skill output as input

</code_context>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 03-translation-skill*
*Context gathered: 2026-03-11*
