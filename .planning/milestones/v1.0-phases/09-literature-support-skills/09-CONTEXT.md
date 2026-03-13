# Phase 9: Literature and Support Skills - Context

**Gathered:** 2026-03-12
**Status:** Ready for planning

<domain>
## Phase Boundary

Build three independent Support Skills following Phase 2 conventions:
1. **Literature Search Skill** (SUPP-02): Search academic literature via Semantic Scholar MCP, present results interactively, generate BibTeX for selected papers, prompt citation verification
2. **Cover Letter Skill** (SUPP-01): Generate submission-ready cover letters from full paper + target journal, with all required content blocks
3. **Visualization Recommendation Skill** (SUPP-03): Recommend appropriate chart types for experimental data with rationale and tool hints, geography-specific options included alongside general types

This phase does not implement automated literature review writing, paper revision, or data visualization code generation.

</domain>

<decisions>
## Implementation Decisions

### Literature Skill — MCP availability strategy
- Pre-flight check: attempt a lightweight Semantic Scholar MCP probe call before starting the main workflow
- If MCP unavailable: refuse execution with a clear error message containing both (1) the reason (MCP not connected) and (2) concrete setup steps (how to connect Semantic Scholar in Claude Code settings)
- No degraded manual fallback mode — consistent with Translation/Polish/Reviewer Skills' journal-missing → refuse pattern
- Error message format: "Semantic Scholar MCP is not available. To use this Skill: [step-by-step setup guidance]"

### Literature Skill — Search to BibTeX interaction flow
- Interactive two-step flow: (1) display results list → (2) user confirms selection → generate BibTeX
- Each search result card shows: Title, Author(s), Year, Citation count, 1-sentence abstract excerpt
- After displaying results, add a verification prompt: "Please confirm that the selected paper actually supports the claim you are citing — do not rely solely on the title"
- Single-shot search: if user needs different results, re-trigger the Skill with different keywords
- No in-session iterative query refinement

### Cover Letter Skill — Input requirements and content scope
- Required inputs: full paper text + target journal name
- Journal template loaded for scope alignment — missing journal template → refuse execution (consistent with all prior Skills)
- Cover letter content blocks (all included):
  1. Contribution statement — explicitly aligned with target journal's scope and aims
  2. Data/code availability statement — links to public repositories if applicable; "not available" if not
  3. Conflict of interest statement — standard declaration
  4. Correspondence author contact block (name, email, institution)
- Output: complete, ready-to-use letter text with placeholders ([Editor Name], [Date]) for user to fill in
- No supplementary checklist — placeholders are self-evident

### Visualization Skill — Input and recommendation depth
- Required inputs: data description (data type, variables, sample size) + research question (what the figure should communicate)
- Output: 2–3 recommended chart types, each with: (1) type name, (2) reason it fits this data/question, (3) common Python/R tool hints (e.g., geopandas, matplotlib, seaborn, ggplot2)
- Geography-specific chart types (choropleth maps, spatial scatter plots, hot spot maps, kernel density) listed alongside general types — no separate section; judge based on data description
- No code generation — tool name hints only
- If data description mentions spatial coordinates, regions, or admin boundaries: proactively include choropleth / spatial scatter as candidates

### Shared decisions (carry-forward from prior phases)
- ~300 line hard budget per SKILL.md (Phase 2 locked)
- Bilingual Chinese/English triggers in frontmatter `examples` field (Phase 2 convention)
- Direct mode as default (Phase 3 locked)
- Anti-AI patterns NOT loaded — these are support/search/generation Skills, not writing-correction Skills
- All three Skills follow skill-conventions.md frontmatter contract exactly
- Journal missing → refuse (consistent with Phase 3–8 pattern)

</decisions>

<specifics>
## Specific Ideas

- Literature Skill's interactive result display should feel like a mini "paper selection" step before commitment — users should be in control of what gets cited
- The citation verification prompt is a guardrail against hallucination, aligned with CLAUDE.md principle: "绝不幻觉引用"
- Cover Letter's contribution statement should explicitly reference the journal's Aims & Scope section from the loaded journal template — not generic praise
- Visualization Skill's tool hints should favor the geography/urban science toolstack (geopandas, contextily, pysal, QGIS) when spatial data is detected

</specifics>

<code_context>
## Existing Code Insights

### Reusable Assets
- `.claude/skills/abstract-skill/SKILL.md`: dual-path + interactive selection pattern — Literature Skill can follow the "ask then act" structure
- `.claude/skills/reviewer-simulation-skill/SKILL.md`: structured output format, file write pattern — Cover Letter Skill can mirror output delivery
- `.claude/skills/translation-skill/SKILL.md`: journal template loading + refuse-if-missing pattern — Cover Letter and Literature Skills follow same convention
- `references/skill-conventions.md` + `references/skill-skeleton.md`: authoring contract for all three new Skills
- `references/journals/ceus.md`: journal Aims & Scope content — Cover Letter Skill loads this for contribution statement alignment

### Established Patterns
- Frontmatter with `references.required` and `references.leaf_hints` (Phase 2)
- Bilingual triggers in `triggers.examples` (Phase 2 convention)
- Journal template missing → refuse execution (Phase 3/4/5/6/8)
- Ask-first posture with fallback to plain-text questions (Phase 2)
- File output naming: `*_review.md`, `*_logic.md` → Cover Letter: `*_cover_letter.md`

### Integration Points
- Literature Skill: `.claude/skills/literature-skill/SKILL.md`
  - Calls `mcp__semantic-scholar__*` tools directly
  - No expression pattern refs needed (search task, not writing task)
  - Output: BibTeX block in conversation (no file write unless user requests)
- Cover Letter Skill: `.claude/skills/cover-letter-skill/SKILL.md`
  - Loads `references/journals/[journal].md` for scope alignment
  - Reads paper via Read tool (file input) or pasted text
  - Writes output to `{input_filename_without_ext}_cover_letter.md`
- Visualization Skill: `.claude/skills/visualization-skill/SKILL.md`
  - No reference file loads needed (recommendation task)
  - Output presented in conversation (no file write)

</code_context>

<deferred>
## Deferred Ideas

None — discussion stayed within Phase 9 boundary.

</deferred>

---

*Phase: 09-literature-support-skills*
*Context gathered: 2026-03-12*
