# Phase 9: Literature and Support Skills - Research

**Researched:** 2026-03-12
**Domain:** Skill authoring — Literature Search (SUPP-02), Cover Letter (SUPP-01), Visualization Recommendation (SUPP-03)
**Confidence:** HIGH

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions

**Literature Skill — MCP availability strategy**
- Pre-flight check: attempt a lightweight Semantic Scholar MCP probe call before starting the main workflow
- If MCP unavailable: refuse execution with a clear error message containing both (1) the reason (MCP not connected) and (2) concrete setup steps (how to connect Semantic Scholar in Claude Code settings)
- No degraded manual fallback mode — consistent with Translation/Polish/Reviewer Skills' journal-missing → refuse pattern
- Error message format: "Semantic Scholar MCP is not available. To use this Skill: [step-by-step setup guidance]"

**Literature Skill — Search to BibTeX interaction flow**
- Interactive two-step flow: (1) display results list → (2) user confirms selection → generate BibTeX
- Each search result card shows: Title, Author(s), Year, Citation count, 1-sentence abstract excerpt
- After displaying results, add a verification prompt: "Please confirm that the selected paper actually supports the claim you are citing — do not rely solely on the title"
- Single-shot search: if user needs different results, re-trigger the Skill with different keywords
- No in-session iterative query refinement

**Cover Letter Skill — Input requirements and content scope**
- Required inputs: full paper text + target journal name
- Journal template loaded for scope alignment — missing journal template → refuse execution (consistent with all prior Skills)
- Cover letter content blocks (all included):
  1. Contribution statement — explicitly aligned with target journal's scope and aims
  2. Data/code availability statement — links to public repositories if applicable; "not available" if not
  3. Conflict of interest statement — standard declaration
  4. Correspondence author contact block (name, email, institution)
- Output: complete, ready-to-use letter text with placeholders ([Editor Name], [Date]) for user to fill in
- No supplementary checklist — placeholders are self-evident

**Visualization Skill — Input and recommendation depth**
- Required inputs: data description (data type, variables, sample size) + research question (what the figure should communicate)
- Output: 2–3 recommended chart types, each with: (1) type name, (2) reason it fits this data/question, (3) common Python/R tool hints (e.g., geopandas, matplotlib, seaborn, ggplot2)
- Geography-specific chart types (choropleth maps, spatial scatter plots, hot spot maps, kernel density) listed alongside general types — no separate section; judge based on data description
- No code generation — tool name hints only
- If data description mentions spatial coordinates, regions, or admin boundaries: proactively include choropleth / spatial scatter as candidates

**Shared decisions (carry-forward from prior phases)**
- ~300 line hard budget per SKILL.md (Phase 2 locked)
- Bilingual Chinese/English triggers in frontmatter `examples` field (Phase 2 convention)
- Direct mode as default (Phase 3 locked)
- Anti-AI patterns NOT loaded — these are support/search/generation Skills, not writing-correction Skills
- All three Skills follow skill-conventions.md frontmatter contract exactly
- Journal missing → refuse (consistent with Phase 3–8 pattern)

### Deferred Ideas (OUT OF SCOPE)

None — discussion stayed within Phase 9 boundary.
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| SUPP-01 | Cover letter Skill generates submission cover letters based on paper content and target journal requirements | Journal template loading pattern from Phase 3–8; reviewer-simulation output format as structural reference |
| SUPP-02 | Literature search Skill uses Semantic Scholar MCP to find relevant papers, verify citations, and generate BibTeX entries | Semantic Scholar MCP confirmed in global settings.json; tool names verified from allowed permissions list |
| SUPP-03 | Visualization recommendation Skill suggests appropriate chart types for experimental results with geography-specific options (choropleth, spatial plots) | No reference file dependencies; pure recommendation task; tool hints from Python/R geography stack |
</phase_requirements>

---

## Summary

Phase 9 delivers three independent Support Skills that extend the paper-polish workflow into adjacent submission tasks. The authoring challenge is not technical (no new infrastructure is introduced) but structural: each Skill has a distinct interaction contract and output format that must be documented precisely within the 300-line budget.

The Semantic Scholar MCP is confirmed available in the user's Claude Code global settings (`~/.claude/settings.json`). The MCP is registered as `semanticscholar` server at `https://server.smithery.ai/@hamid-vakilzadeh/mcpsemanticscholar`, and all tool names (`mcp__semantic-scholar__papers-search-basic`, `mcp__semantic-scholar__search-paper-title`, etc.) are explicitly in the permissions allowlist. The Literature Skill can call these tools directly; no installation or configuration step is required during execution.

All three Skills follow the frontmatter contract and conventions established in Phase 2. The Cover Letter Skill mirrors the Reviewer Simulation Skill's file-write pattern (`*_cover_letter.md`). The Literature Skill introduces the only interactive (two-step) flow in the project — results list then selection confirmation — which can be modeled on the Abstract Skill's "ask-then-act" posture. The Visualization Skill is the simplest: a pure recommendation task with no file writes and no reference file loads.

**Primary recommendation:** Author all three Skills as independent PLAN files (three plans in Phase 9), each producing one SKILL.md. The Literature Skill is the most complex and should be authored first to nail the MCP interaction pattern.

---

## Standard Stack

### Core

| Component | Version/Path | Purpose | Why Standard |
|-----------|-------------|---------|--------------|
| `mcp__semantic-scholar__papers-search-basic` | MCP tool | Search papers by keyword | Already in allowlist; primary discovery tool |
| `mcp__semantic-scholar__search-paper-title` | MCP tool | Lookup by exact title for citation verification | Precise match for known titles |
| `mcp__semantic-scholar__get-paper-abstract` | MCP tool | Fetch full abstract for a paper ID | Used in result card construction |
| `references/journals/ceus.md` | Stable path | Journal scope alignment for Cover Letter | Only journal template currently available |
| `references/skill-conventions.md` | Stable path | Frontmatter contract and body structure | Authoring contract for all three Skills |
| `references/skill-skeleton.md` | Stable path | Copyable starting point | Used by all prior Skill phases |

### Supporting MCP Tools (available, use as needed)

| Tool | Purpose | When to Use |
|------|---------|-------------|
| `mcp__semantic-scholar__paper-search-advanced` | Advanced filtered search | If basic search returns poor results |
| `mcp__semantic-scholar__papers-citations` | Get papers that cite a paper | Citation context (if user requests) |
| `mcp__semantic-scholar__papers-references` | Get references of a paper | Not needed for Phase 9 scope |
| `mcp__semantic-scholar__papers-batch` | Batch paper lookups | Not needed; single-shot search design |

**Installation:** No installation needed. MCP server is pre-configured in `~/.claude/settings.json` under key `semanticscholar`. All tool permissions are pre-allowed.

---

## Architecture Patterns

### Recommended Skill Directory Structure

```
.claude/skills/
├── literature-skill/
│   └── SKILL.md          # SUPP-02 — MCP search + BibTeX generation
├── cover-letter-skill/
│   └── SKILL.md          # SUPP-01 — journal-aware cover letter generation
└── visualization-skill/
    └── SKILL.md          # SUPP-03 — chart type recommendation
```

### Pattern 1: MCP Pre-flight Check (Literature Skill)

**What:** Before executing main workflow, attempt a lightweight MCP call to confirm availability. If it fails, refuse with setup guidance.

**When to use:** Any Skill that depends on an external MCP tool that may not be connected.

**Example — pre-flight check structure in Workflow section:**

```markdown
### Step 1: MCP Pre-flight Check

- Attempt: call `mcp__semantic-scholar__papers-search-basic` with a trivial query (e.g., `{"query": "test", "limit": 1}`).
- If call succeeds: proceed to Step 2.
- If call fails or tool is unavailable: **refuse immediately** with:
  > "Semantic Scholar MCP is not available. To use this Skill:
  > 1. Open Claude Code Settings → MCP Servers
  > 2. Add the Semantic Scholar server (server key: `semanticscholar`)
  > 3. Restart Claude Code and retry"
```

**Confidence:** HIGH — MCP tool calling follows standard Claude Code patterns; refuse-on-unavailable matches Phase 3–8 journal-missing pattern.

### Pattern 2: Interactive Two-Step Flow (Literature Skill)

**What:** Display search results list, wait for user selection, then generate BibTeX for the selected paper. This is the only interactive (non-direct-mode) pattern in Phase 9.

**When to use:** Search/selection tasks where user must confirm before committing to output.

**Example — result card format:**

```markdown
**[1] Title of the Paper**
Authors: Smith, J.; Lee, K. · Year: 2023 · Citations: 142
> "One sentence from abstract describing the core contribution..."

**[2] Another Relevant Paper**
Authors: Zhang, W.; Chen, L. · Year: 2022 · Citations: 89
> "One sentence from abstract..."

---
Please select a paper (enter number), or type "none" to cancel.

> After selection, verification prompt:
> "Please confirm that the selected paper actually supports the claim you are citing — do not rely solely on the title."
```

**Confidence:** HIGH — mirrors Abstract Skill's ask-then-act posture; verified against CONTEXT.md locked decisions.

### Pattern 3: Journal-Aware Generation with Refuse-on-Missing (Cover Letter Skill)

**What:** Load journal template for scope alignment. If journal template not found, refuse — never fall back to generic style. This is the established convention from Phases 3–8.

**When to use:** Any Skill that requires journal-specific content alignment.

**Example — journal loading in Workflow:**

```markdown
### Step 1: Collect Context

- Ask: target journal name (if not specified in trigger).
- Load `references/journals/[journal].md`. If file missing: **refuse** with:
  "Journal template for [X] not found. Available: CEUS."
- Read paper: file via Read tool, or pasted text from conversation.
- Ask: correspondence author details (name, email, institution) if not provided.
```

**Confidence:** HIGH — exact pattern used in translation-skill, polish-skill, abstract-skill, caption-skill.

### Pattern 4: Pure Recommendation Output (Visualization Skill)

**What:** No reference file loads, no file writes. Collect data description + research question, return 2–3 chart recommendations with reasoning and tool hints in conversation.

**When to use:** Advisory tasks where output is prescriptive guidance, not generated content.

**Example — recommendation format:**

```markdown
**Recommendation 1: Choropleth Map**
- Why it fits: Your data has administrative unit polygons and a continuous outcome variable — choropleth encodes magnitude by color fill across spatial units.
- Tool hints: Python: `geopandas` + `matplotlib`; R: `ggplot2` + `sf`

**Recommendation 2: Spatial Scatter Plot**
- Why it fits: Point-level GPS coordinates allow precise placement; scatter shows clustering and spatial autocorrelation.
- Tool hints: Python: `geopandas.plot()`, `contextily` for basemap; R: `ggplot2` + `geom_sf()`

**Recommendation 3: Kernel Density Map**
- Why it fits: Large point datasets benefit from density smoothing to reveal hotspots rather than overplotting.
- Tool hints: Python: `seaborn.kdeplot()`, `scipy.stats.gaussian_kde`; R: `ggplot2` + `stat_density_2d()`
```

**Confidence:** HIGH — follows pattern from Logic Skill (no reference file loads) and CONTEXT.md locked decisions.

### Anti-Patterns to Avoid

- **Iterative search refinement in Literature Skill:** The decision is single-shot search only. Do not build re-query logic.
- **Code generation in Visualization Skill:** Tool name hints only. Do not generate Python/R code blocks.
- **Generic fallback mode for Literature Skill when MCP unavailable:** Refuse immediately, consistent with journal-missing pattern.
- **Verification prompt omission in Literature Skill:** The anti-hallucination verification prompt is mandatory per CLAUDE.md principle ("绝不幻觉引用").
- **Batch mode for Literature Skill or Cover Letter Skill:** These Skills require full context per invocation. Batch is not supported.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Paper search | Custom HTTP calls to Semantic Scholar API | `mcp__semantic-scholar__papers-search-basic` | MCP already configured, handles auth, rate limiting |
| BibTeX formatting | Manual BibTeX string construction | MCP returns structured paper data; format from it | MCP returns DOI, author list, year, title — format from these fields directly |
| Citation verification | AI inference about paper content | `mcp__semantic-scholar__get-paper-abstract` + user confirmation | Avoids hallucination; aligns with CLAUDE.md anti-hallucination principle |
| Journal scope lookup | Inline prose about journal aims | `references/journals/[journal].md` | Already exists for CEUS; consistent with all prior Skills |

**Key insight:** The Semantic Scholar MCP eliminates all paper data fetching complexity. The Literature Skill's job is interaction design (result cards, selection flow, BibTeX formatting), not API integration.

---

## Common Pitfalls

### Pitfall 1: Assuming MCP Is Always Available

**What goes wrong:** Skill starts main workflow before checking MCP availability. User sees a cryptic MCP error mid-workflow rather than a helpful upfront message.

**Why it happens:** MCP availability is environment-dependent. The MCP server requires an active network connection to `server.smithery.ai`.

**How to avoid:** Implement pre-flight check as Step 1 in Literature Skill workflow. Refuse immediately if the probe call fails.

**Warning signs:** MCP call returning `tool not found` or timeout errors during result display.

### Pitfall 2: BibTeX Hallucination Without MCP Verification

**What goes wrong:** Skill generates BibTeX from memory or training data rather than MCP-fetched paper records, producing entries with wrong DOIs, wrong author names, or wrong years.

**Why it happens:** AI training data includes paper metadata that may be stale or misremembered.

**How to avoid:** BibTeX must be constructed only from data returned by the MCP call. Never generate BibTeX from prior knowledge. The verification prompt ("Please confirm that the selected paper actually supports the claim") is a second guardrail.

**Warning signs:** BibTeX entries with DOIs that resolve to 404, author name misspellings, wrong publication years.

### Pitfall 3: Cover Letter Using Generic Journal Framing

**What goes wrong:** Contribution statement says "this paper advances the field of urban computing" rather than explicitly referencing CEUS's scope (geography/urban systems perspective, planning/policy relevance).

**Why it happens:** Without loading the journal template, the Skill defaults to generic academic prose.

**How to avoid:** Load `references/journals/[journal].md` before writing the contribution statement. Extract journal's Aims & Scope section and use it to frame the contribution.

**Warning signs:** Contribution statement has no explicit mention of the journal's stated scope or aims.

### Pitfall 4: Visualization Skill Recommending Spatial Charts for Non-Spatial Data

**What goes wrong:** Skill recommends choropleth or spatial scatter when user describes tabular (non-spatial) data.

**Why it happens:** Visualization Skill is geography-aware by design, but spatial chart types only apply when data description mentions spatial coordinates, regions, or admin boundaries.

**How to avoid:** Gate spatial chart recommendations on explicit spatial data signals in the user's data description. If data description mentions "administrative units", "latitude/longitude", "districts", or "regions" → include spatial types. Otherwise → skip them entirely.

**Warning signs:** Recommending `geopandas` for data described as "survey responses from 500 participants".

### Pitfall 5: Exceeding 300-Line Budget

**What goes wrong:** Three Skills with complex workflows push toward 350-400 lines each, especially Literature Skill with its multi-step interaction flow.

**Why it happens:** Result card format, BibTeX template, and verification prompt take significant space if written verbosely.

**How to avoid:** Keep Workflow steps concise (numbered list, not prose paragraphs). Result card format should be shown once as a compact example, not repeated. Budget: ~30 lines frontmatter, ~30 lines Purpose+Trigger+Modes, ~60 lines References+Ask Strategy, ~80 lines Workflow, ~40 lines Output Contract+Edge Cases+Fallbacks, ~30 lines Examples = ~270 lines.

---

## Code Examples

Verified patterns from existing Skills in this project:

### Frontmatter for Literature Skill (MCP tool in tools list)

```yaml
---
name: literature-skill
description: >-
  Search academic literature via Semantic Scholar MCP, select papers interactively,
  and generate verified BibTeX entries. 文献检索与BibTeX生成，通过Semantic Scholar MCP。
triggers:
  primary_intent: search academic literature and generate BibTeX citations
  examples:
    - "Find papers about urban heat island"
    - "帮我找关于城市热岛的文献"
    - "Search literature for spatiotemporal analysis"
    - "生成这篇文章的BibTeX引用"
    - "Find relevant papers on deep learning for land use classification"
    - "帮我检索相关文献并生成引用格式"
tools:
  - Semantic Scholar MCP
  - Structured Interaction
references:
  required: []
  leaf_hints: []
input_modes:
  - pasted_text
output_contract:
  - bibtex_entry
---
```

### Frontmatter for Cover Letter Skill

```yaml
---
name: cover-letter-skill
description: >-
  Generate submission-ready cover letters from paper content and target journal requirements.
  Includes contribution statement, data availability, conflict of interest, and contact block.
  生成投稿信，包含贡献声明、数据可用性、利益冲突声明和联系方式。
triggers:
  primary_intent: generate submission cover letter for academic paper
  examples:
    - "Write a cover letter for my CEUS submission"
    - "帮我写投稿信"
    - "Generate a cover letter for my paper"
    - "写一封投稿到CEUS的cover letter"
    - "Help me draft a submission cover letter"
    - "帮我生成这篇论文的投稿信"
tools:
  - Read
  - Write
  - Structured Interaction
references:
  required: []
  leaf_hints:
    - references/journals/ceus.md
input_modes:
  - file
  - pasted_text
output_contract:
  - cover_letter_md
---
```

### Frontmatter for Visualization Skill

```yaml
---
name: visualization-skill
description: >-
  Recommend appropriate chart types for experimental data with rationale and tool hints.
  Geography-aware: choropleth, spatial scatter, kernel density when spatial data detected.
  为实验数据推荐合适的图表类型，支持地理空间数据可视化建议。
triggers:
  primary_intent: recommend visualization types for experimental data
  examples:
    - "What chart should I use for this data?"
    - "帮我选择合适的可视化方式"
    - "Recommend a visualization for my spatial analysis results"
    - "我应该用什么图展示这个结果"
    - "Which plot type fits my regression results?"
    - "帮我推荐一个展示时空数据的图表"
tools:
  - Structured Interaction
references:
  required: []
  leaf_hints: []
input_modes:
  - pasted_text
output_contract:
  - recommendation_list
---
```

### Cover Letter Output Structure (derived from CONTEXT.md locked decisions)

```markdown
[Date]

The Editor-in-Chief
[Editor Name]
[Journal Name]

Dear [Editor Name],

We submit our manuscript entitled "[Paper Title]" for consideration in [Journal Name].

**Contribution Statement**
[Aligned with journal scope and aims from references/journals/[journal].md]

**Data and Code Availability**
[Links to repositories or "Data are not publicly available due to..."]

**Conflict of Interest**
The authors declare no conflict of interest.

**Corresponding Author**
[Name]
[Email]
[Institution]

Sincerely,
[Authors]
```

### BibTeX Output Format (Literature Skill)

```bibtex
@article{smith2023urban,
  title     = {Urban Heat Island Mitigation through Green Infrastructure},
  author    = {Smith, John and Lee, Kwang-Ho},
  journal   = {Computers, Environment and Urban Systems},
  year      = {2023},
  volume    = {101},
  pages     = {101943},
  doi       = {10.1016/j.compenvurbsys.2023.101943}
}
```

Note: All fields populated from MCP-returned data only — never from prior knowledge.

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Manual BibTeX construction from memory | MCP-fetched structured paper data → BibTeX | Phase 9 design decision | Eliminates hallucination risk for citation entries |
| Cover letter as generic template | Journal-template-aligned contribution statement | Phase 9 design decision | Cover letter explicitly references journal scope, not generic praise |
| Visualization recommendations as prose | Structured 2–3 recommendation cards with tool hints | Phase 9 design decision | Actionable, scannable output |

**Deprecated/outdated in this phase:**
- Manual fallback search mode when MCP unavailable: refused in favor of clear error + setup guidance (consistent with journal-missing → refuse pattern from Phases 3–8)

---

## Open Questions

1. **MCP tool exact response schema for `papers-search-basic`**
   - What we know: Tool is in the allowlist and accessible; returns paper records
   - What's unclear: Exact JSON field names for title, authors, year, citation count, abstract excerpt — need to handle gracefully if abstract field is empty for some papers
   - Recommendation: Skill should note in Edge Cases: "If abstract unavailable: show 'Abstract not available' in result card rather than empty entry"

2. **Cover letter output file: file vs. conversation**
   - What we know: CONTEXT.md implies output is "complete, ready-to-use letter text"
   - What's unclear: Should the Skill always write to `{input_filename_without_ext}_cover_letter.md`, or offer conversation-only output when input is pasted text?
   - Recommendation: Follow the established convention — file input → write to `*_cover_letter.md`; pasted text → output in conversation (same as Reviewer Simulation Skill)

3. **Visualization Skill: how to handle requests for data the Skill cannot evaluate**
   - What we know: Skill receives a data description in natural language; it cannot load or process actual data files
   - What's unclear: Should Skill accept file paths and read data files, or always require text description?
   - Recommendation: Input mode is `pasted_text` only (text description + research question). If user provides a file path, ask them to describe the data instead. Keeps Skill simple and within scope.

---

## Validation Architecture

### Test Framework

| Property | Value |
|----------|-------|
| Framework | None detected — markdown-only Skills, no executable code |
| Config file | None — no test infrastructure in this project |
| Quick run command | Manual: open Claude Code, invoke Skill, verify output structure |
| Full suite command | Manual review of all three SKILL.md files against skill-conventions.md checklist |

### Phase Requirements → Test Map

| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| SUPP-02 | Literature Skill: MCP pre-flight check refuses when unavailable | manual-only | N/A — requires live MCP context | ❌ Wave 0 N/A |
| SUPP-02 | Literature Skill: result cards show Title/Authors/Year/Citations/Excerpt | manual-only | N/A — output validation in session | ❌ Wave 0 N/A |
| SUPP-02 | Literature Skill: BibTeX generated from MCP data, not prior knowledge | manual-only | N/A — requires MCP + human review | ❌ Wave 0 N/A |
| SUPP-01 | Cover Letter Skill: all four content blocks present in output | manual-only | N/A — markdown output, human review | ❌ Wave 0 N/A |
| SUPP-01 | Cover Letter Skill: journal template missing → refuse | manual-only | N/A — requires invocation test | ❌ Wave 0 N/A |
| SUPP-03 | Visualization Skill: 2–3 recommendations with type/reason/tools | manual-only | N/A — output validation in session | ❌ Wave 0 N/A |
| SUPP-03 | Visualization Skill: spatial data triggers choropleth/spatial scatter | manual-only | N/A — requires invocation test | ❌ Wave 0 N/A |

**Note:** All phase 9 tests are manual-only. This project has no automated test infrastructure — all Skills are Claude Code SKILL.md authoring artifacts (markdown), not executable code. Validation is performed through convention compliance checks and manual invocation during the `/gsd:verify-work` gate.

### Sampling Rate

- **Per task commit:** Convention compliance self-check: verify SKILL.md against skill-conventions.md checklist (line count, required sections, frontmatter fields)
- **Per wave merge:** N/A — all three plans are in a single wave
- **Phase gate:** Manual invocation of each Skill before `/gsd:verify-work`

### Wave 0 Gaps

None — no test infrastructure needed for markdown Skill authoring. Validation is structural (convention compliance) and performed during PLAN execution self-check.

---

## Sources

### Primary (HIGH confidence)

- `.claude/settings.json` (user's global Claude Code settings) — Confirmed Semantic Scholar MCP server registration and all `mcp__semantic-scholar__*` tool permissions
- `references/skill-conventions.md` — Frontmatter contract, line budget, section requirements
- `.claude/skills/translation-skill/SKILL.md` — Journal-missing → refuse pattern, file output naming
- `.claude/skills/reviewer-simulation-skill/SKILL.md` — Bilingual output format, file output naming `*_review.md`
- `.claude/skills/abstract-skill/SKILL.md` — Interactive ask-then-act posture, two-path workflow pattern
- `.claude/skills/caption-skill/SKILL.md` — Ask Strategy branching pattern, Read-before-Write output
- `.claude/skills/logic-skill/SKILL.md` — No-reference-load pure analysis pattern, direct mode only

### Secondary (MEDIUM confidence)

- `references/journals/ceus.md` — CEUS Aims & Scope content for Cover Letter contribution statement alignment (verified — file exists and contains "Aims & Scope" equivalent content in Section Guidance)
- `.planning/phases/09-literature-support-skills/09-CONTEXT.md` — All locked decisions; source of truth for interaction designs

### Tertiary (LOW confidence)

- Semantic Scholar MCP tool response schema (field names for paper records) — confirmed tools exist in allowlist but exact JSON schema was not inspected at research time; Skill should handle missing fields gracefully

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — MCP configuration confirmed in settings.json; all tool names verified in permissions allowlist
- Architecture patterns: HIGH — directly derived from locked CONTEXT.md decisions and existing Skill file analysis
- Pitfalls: HIGH — derived from CLAUDE.md principles (anti-hallucination) and established project patterns (refuse-on-missing)
- MCP response schema: LOW — tool availability confirmed but field names not inspected at runtime

**Research date:** 2026-03-12
**Valid until:** 2026-04-12 (stable — MCP server is pre-configured; Skills are markdown authoring, not code)
