# Phase 1: Reference Libraries - Context

**Gathered:** 2026-03-11
**Status:** Ready for planning

<domain>
## Phase Boundary

Build the shared reference materials that all downstream Skills can load on demand: an academic expression library, a CEUS journal contract, and an anti-AI patterns library. This phase defines how those references are organized and what information they must contain. It does not add new writing capabilities or implement downstream Skills.

</domain>

<decisions>
## Implementation Decisions

### Reference library organization
- Use a hybrid structure: keep lightweight overview files plus finer-grained subfiles for on-demand loading.
- Optimize for precise AI retrieval over purely manual editing convenience.
- Do not enforce a rigid file length limit; instead require stable headings, clear hierarchy, and context-friendly topic boundaries.
- Use different directory contracts for journal templates versus expression and anti-AI references.
- Overview files should primarily act as loading guides and indexes, while allowing a small amount of highest-frequency content when it improves quick reads.

### CEUS journal contract
- `ceus.md` should be a Skill-readable contract, not only a submission checklist.
- Structure the CEUS contract into `Submission Requirements`, `Writing Preferences`, and `Quality Checks`.
- Section guidance should be detailed enough to include section purpose, expected content points, and common pitfalls.
- The contract should be rich enough to support translation, polish, and review Skills consistently.
- Later research may enrich the pitfall and style guidance using CEUS author guidance and representative published-paper patterns, but the contract structure should stay stable.

### Anti-AI reference strategy
- The anti-AI reference should function as both a risk-pattern handbook and a rewriting-guidance contract.
- Its goal is to preserve formal academic style while removing obvious AI traces, not to make writing casual.
- Mark entries by risk tier: `High Risk`, `Medium Risk`, and `Optional`.
- Internal entries may include rationale and rewrite direction, but downstream Skills should be able to read a lightweight `problem expression -> replacement` layer by default.

### Expression library content model
- Organize the expression library by writing scenario rather than as a flat phrase sheet.
- Provide English expressions with Chinese explanations; full bilingual long-form examples are not required by default.
- Maintain separate layers for general academic expressions and geography/urban-science-specific expressions.
- Systematically include `recommended expressions`, `avoid expressions`, and `usage scenarios` rather than only positive phrase lists.

### Claude's Discretion
- Exact subdirectory names and file naming conventions under the expression and anti-AI libraries.
- Which highest-frequency items remain in overview files versus move into dedicated subfiles.
- The exact heading taxonomy used to keep downstream `Read` calls stable across reference documents.

</decisions>

<specifics>
## Specific Ideas

- The existing single-file references are seeds, not final structure.
- Future Skills should be able to read only the section they need instead of loading one large reference file.
- Journal references should behave like reusable contracts; expression and anti-AI references should behave like modular knowledge libraries.
- No external product or UX reference was required in this discussion; the priority is downstream Skill reuse for real paper submission workflows.

</specifics>

<code_context>
## Existing Code Insights

### Reusable Assets
- `references/expression-patterns.md`: existing seed content already covers sentence starters, transitions, hedging, results, and conclusions; it can be split and upgraded instead of rewritten from zero.
- `references/journals/ceus.md`: existing seed content already captures hard requirements, word budgets, highlights, and basic style notes; it is a strong starting point for the richer CEUS contract.
- `paper-polish-workflow/SKILL.md`: the current polish Skill already reads reference files by stable path, which validates the need for predictable file locations and headings.

### Established Patterns
- `references/` is already the shared knowledge root for reusable writing guidance.
- `references/journals/[journal].md` is already documented in README and CONTRIBUTING, so journal paths should remain stable to avoid breaking existing docs and future Skills.
- The project is markdown-only with no build step, so reference design must stay human-readable and directly loadable by Claude Code tools.

### Integration Points
- Phase 2 should codify how Skills declare and load these references under a consistent convention.
- Phase 3, Phase 4, and Phase 6 will directly depend on the expression library and CEUS contract.
- Phase 5 will depend on the anti-AI library structure defined here, including tiering and lightweight retrieval format.

</code_context>

<deferred>
## Deferred Ideas

None - discussion stayed within the Phase 1 boundary.

</deferred>

---

*Phase: 01-reference-libraries*
*Context gathered: 2026-03-11*
