# Project Retrospective

*A living document updated after each milestone. Lessons feed forward into future planning.*

## Milestone: v1.0 — Paper Polish Workflow

**Shipped:** 2026-03-13
**Phases:** 10 | **Plans:** 15 | **Requirements:** 16/16

### What Was Built
- 11 production Claude Code Skills covering the full academic paper writing lifecycle
- Modular reference library (expression patterns, anti-AI patterns, CEUS journal template) with on-demand loading architecture
- Two-phase detect-then-rewrite pattern (De-AI, Experiment Skills) enabling user confirmation before generation
- Bilingual output architecture: Skills trigger in Chinese/English; Reviewer + Logic Skills provide inline Chinese translations
- Complete bilingual README with Skill inventory table and end-to-end quick-start workflows

### What Worked
- **Phase velocity was high after Phase 1:** Phases 2-10 executed quickly (~3-5 min each) because Skill authoring is markdown-only with no code build steps. Phase 1 (reference design) dominated time investment at ~90 min.
- **Convention-first approach paid off:** Defining Skill conventions (Phase 2) before authoring any Skills ensured all 11 Skills are consistent. No refactoring needed across Skills.
- **Anti-hallucination pattern locked early:** Explicitly designing the literature-skill to use MCP data exclusively (from CLAUDE.md principle) prevented the most common failure mode of AI-generated BibTeX.
- **Modular reference design enables narrow context:** Each Skill loads only the 1-3 leaf files it needs. This keeps Skill prompt context lean and avoids loading a large monolithic reference every time.
- **Milestone audit caught real issues:** The pre-archive audit found 4 tech debt items before archiving, preventing them from being lost. All 4 resolved before archive.

### What Was Inefficient
- **ROADMAP.md plan status not updated during execution:** Phases 3, 4, and 6 were marked `[ ]` (not started) in the roadmap's progress table even after completion. Status tracking was only in STATE.md. Next milestone: update roadmap plan status at each plan completion.
- **Phase 1 research took 90+ minutes:** Expression patterns required significant design work (what categories, how to structure for retrieval). This was appropriate investment but not anticipated upfront. Future reference-heavy phases should be time-budgeted explicitly.
- **Accomplishments not extractable from SUMMARY.md by CLI:** The `gsd-tools milestone complete` command returned empty accomplishments because SUMMARY.md files don't have a standardized `one_liner` field. Accomplishments had to be manually authored. Add `one_liner` field to future SUMMARY.md files.

### Patterns Established
- **Two-phase analysis workflow** (Phase 1 confirm → Phase 2 generate): Used in De-AI Skill and Experiment Skill; applicable to any Skill where user should approve intermediate output before expensive generation
- **Geography-conditional Ask Strategy**: Spatial figure type question gates entire spatial metadata branch (CRS, study area, legend); non-spatial figures skip branch entirely. Avoids irrelevant questions.
- **Analysis-before-table ordering in Logic Skill**: Status column in Argument Chain table is *derived* from identified issues rather than filled optimistically. Prevents false confidence.
- **Journal-missing → refuse pattern**: Translation, Cover Letter Skills refuse with clear instructions when journal template is absent rather than falling back to generic style. Maintains quality guarantee.
- **Domain term protection via context inference**: De-AI Skill infers domain terms from surrounding context rather than hardcoded wordlist. More robust and generalizable.

### Key Lessons
1. **Reference architecture design is the highest-leverage phase.** Phase 1 decisions (modular vs. monolithic, file naming, category structure) affect every downstream Skill. Invest time here; it compounds.
2. **Define interaction mode taxonomy before first Skill.** Having 4 named modes (direct/guided/interactive/batch) in conventions prevents ad-hoc interaction design in each Skill.
3. **Anti-hallucination must be designed in, not added later.** The literature-skill's MCP-only BibTeX rule works because it was specified in the plan, not retrofitted. Retrofit is harder because Skills don't have tests.
4. **Skill budget forces good design.** The ~300-line limit forces progressive disclosure — lean SKILL.md with on-demand reference loading. Without the budget, Skills would balloon with embedded examples.
5. **Two-phase workflow pattern is reusable.** De-AI and Experiment Skills both use Phase 1 (analysis/detection) → user confirms → Phase 2 (generation). This pattern should be documented in conventions for future analysis Skills.

### Cost Observations
- Model mix: ~100% sonnet (claude-sonnet-4-6); quality profile used throughout
- Sessions: ~5-6 sessions across 3 days (2026-03-10 to 2026-03-13)
- Notable: Skill authoring phases (2-10) were extremely fast (~3-5 min/plan); all time concentrated in Phase 1 reference design and audit/tech-debt resolution

---

## Cross-Milestone Trends

### Process Evolution

| Milestone | Sessions | Phases | Key Change |
|-----------|----------|--------|------------|
| v1.0 | ~6 | 10 | First milestone — established reference architecture, Skill conventions, and two-phase workflow pattern |

### Cumulative Quality

| Milestone | Requirements | Coverage | Skills Shipped |
|-----------|-------------|----------|----------------|
| v1.0 | 16/16 | 100% | 11 |

### Top Lessons (Verified Across Milestones)

1. Reference architecture is the highest-leverage design phase — invest time here before any Skill authoring
2. Convention-first approach (define standards before implementing) prevents inconsistency across Skills
