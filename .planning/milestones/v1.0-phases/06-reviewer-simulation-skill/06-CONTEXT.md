# Phase 6: Reviewer Simulation Skill - Context

**Gathered:** 2026-03-12
**Status:** Ready for planning

<domain>
## Phase Boundary

Build a Reviewer Simulation Skill that reads a full paper and produces a structured peer review report. The review identifies weaknesses across novelty, methodology, writing quality, presentation, and significance, providing actionable improvement suggestions for each concern. The output mimics real journal review conventions (major concerns, minor concerns, questions for authors) with a dimension scoring overview. This phase does not implement automated paper revision — it produces a review report only.

</domain>

<decisions>
## Implementation Decisions

### Review report structure
- Two-part format: dimension scoring overview first, then detailed review body
- Scoring overview uses 1-10 numeric scale across five dimensions: Novelty, Methodology, Writing Quality, Presentation, Significance
- Each dimension score accompanied by a one-line justification
- Review body organized as: Major Concerns → Minor Concerns → Questions for Authors
- Final verdict provided: Accept / Minor Revision / Major Revision / Reject with one-sentence summary

### Weakness-to-suggestion depth
- Each concern includes three parts: problem description, why it's a problem, and a one-sentence directional suggestion (e.g., "Consider adding a comparison with baseline method X to strengthen validity")
- Concerns are located at section level (e.g., "In the Discussion section..."), not paragraph/sentence level
- Review output is bilingual: English review text with Chinese translation appended for each concern
- No full rewrite examples — directional guidance only

### Journal-specific review criteria
- When a target journal is specified, journal template is loaded as auxiliary reference to inform review (light adaptation)
- Journal preferences mentioned in review comments where relevant, but core dimensions and weights remain the same
- Missing journal template → refuse execution (consistent with Translation/Polish/De-AI Skills)

### Input and output
- Full paper input only — partial sections (just introduction, just methods) are not accepted
- Supports both file path and pasted text as input (consistent with other Skills)
- Output is a unified review report (not per-section mini-reviews)
- Report written directly as a Markdown file (review-report.md or similar)
- For pasted text input: report presented in conversation (no file to write to)

### Claude's Discretion
- Exact weighting or emphasis across the five dimensions
- How to structure the Chinese translation alongside English text (inline vs appended block)
- Internal analysis strategy for identifying weaknesses
- Review report filename convention

</decisions>

<specifics>
## Specific Ideas

- The review should feel like receiving a real peer review — structured, professional, actionable
- Each weakness should be specific enough that the author knows exactly what section to revisit
- The five scoring dimensions (Novelty, Methodology, Writing Quality, Presentation, Significance) cover both technical and communication aspects
- Bilingual output (English + Chinese) serves the user's workflow of understanding review feedback quickly in Chinese while having the formal English version

</specifics>

<code_context>
## Existing Code Insights

### Reusable Assets
- `.claude/skills/polish-skill/SKILL.md`: established Skill pattern (follows Phase 2 conventions) — Reviewer Skill should follow same frontmatter and body structure
- `.claude/skills/de-ai-skill/SKILL.md`: another recent Skill with similar structure — reference for consistency
- `references/expression-patterns.md` + leaf modules: could inform writing quality assessment dimension
- `references/journals/ceus.md`: journal template for CEUS-specific review criteria adaptation
- `references/skill-conventions.md` + `references/skill-skeleton.md`: Skill authoring rules and copyable starting point

### Established Patterns
- Frontmatter with `references.required` and `references.leaf_hints` (Phase 2)
- Bilingual triggers in `triggers.examples` (Phase 2 convention)
- Journal template missing → refuse execution (Phase 3/4/5 decision)
- Ask-first posture with fallback to plain-text questions (Phase 2)
- ~300 line hard budget for SKILL.md (Phase 2)

### Integration Points
- Skill lives at `.claude/skills/reviewer-simulation-skill/SKILL.md`
- Loads journal templates from `references/journals/` when target journal specified
- May reference expression-patterns for writing quality assessment
- Uses Read tool for paper input, Write tool for report output
- No upstream/downstream Skill dependencies (standalone review capability)

</code_context>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 06-reviewer-simulation-skill*
*Context gathered: 2026-03-12*
