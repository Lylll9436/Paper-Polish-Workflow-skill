# Phase 1: Reference Libraries - Research

**Researched:** 2026-03-11
**Domain:** Markdown reference architecture for Claude Code academic writing Skills
**Confidence:** HIGH

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- Use a hybrid structure: lightweight overview files plus finer-grained subfiles for on-demand loading.
- Optimize for precise AI retrieval over purely manual editing convenience.
- Keep headings stable and hierarchy clear instead of enforcing a rigid file length cap.
- Use different directory contracts for journal templates versus expression and anti-AI references.
- Keep overview files as loading guides/indexes with a small amount of highest-frequency content.
- Treat `references/journals/ceus.md` as a Skill-readable contract with `Submission Requirements`, `Writing Preferences`, and `Quality Checks`.
- Include section purpose, expected content points, and common pitfalls in the CEUS contract.
- Treat the anti-AI library as both a risk-pattern handbook and rewriting-guidance contract.
- Preserve formal academic style while removing obvious AI traces.
- Mark anti-AI entries by `High Risk`, `Medium Risk`, and `Optional`.
- Let downstream Skills default to a lightweight `problem expression -> replacement` retrieval layer.
- Organize the expression library by writing scenario rather than as a flat phrase list.
- Provide English expressions with Chinese explanations.
- Separate general academic patterns from geography/urban-science-specific patterns.
- Systematically include `recommended expressions`, `avoid expressions`, and `usage scenarios`.

### Claude's Discretion
- Exact subdirectory names and file naming conventions under the expression and anti-AI libraries.
- Which highest-frequency items stay in overview files versus move into leaf files.
- The final heading taxonomy used to keep downstream `Read` calls stable.

### Deferred Ideas (OUT OF SCOPE)
- None - discussion stayed within phase scope.
</user_constraints>

<research_summary>
## Summary

Phase 1 is a documentation-and-structure problem, not a library-selection problem. The standard approach for Claude Code Skill references is to keep the runtime contract simple: stable top-level entrypoints that Skills already know how to read, plus narrower leaf files that a Skill can load only when needed. For this repo, that means preserving the existing top-level anchors under `references/` while introducing subdirectories for finer-grained modules.

The key planning implication is that expression patterns and anti-AI references should follow the same hybrid loading philosophy but not the same information model. Expression modules should be organized by writing scenario so downstream Skills can load only the rhetorical function they need. Anti-AI modules should be organized by risk pattern category, with tier markers inside each file, so the later De-AI Skill can both explain risk and fetch lightweight replacements.

For journals, the current `references/journals/ceus.md` path is already documented in README and the legacy Skill shell. That path should remain stable. The content should be upgraded into a contract with invariant sections that later Skills can target reliably instead of treating it as a loose checklist.

**Primary recommendation:** Keep existing top-level reference paths stable, add modular leaf files underneath them, and align public docs to the new hybrid layout in the same phase.
</research_summary>

<standard_stack>
## Standard Stack

The established tools/patterns for this phase:

### Core
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| Markdown reference files | N/A | Store reusable writing guidance | Zero build step, human-readable, directly loadable by Claude Code tools |
| Stable entrypoint files under `references/` | N/A | Provide backwards-compatible load anchors | Existing README and Skill shell already rely on these paths |
| Leaf modules in subdirectories | N/A | Narrow context for on-demand loading | Prevents reference bloat inside downstream Skills |
| Claude Code `Read`/`Grep` retrieval pattern | N/A | Load only relevant sections/files | Matches project architecture and progressive-disclosure goals |

### Supporting
| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| `rg`-friendly heading schema | N/A | Stable section lookup for future Skills | When a Skill needs one section instead of a full file |
| README/CONTRIBUTING path sync | N/A | Keep onboarding docs aligned with actual layout | Whenever reference paths or structure change |
| Journal contract invariants | N/A | Keep all future journal templates structurally consistent | When adding IJGIS, Cities, or other journals later |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Stable overview + leaf modules | One monolithic file per library | Easier to hand-edit, but too expensive for downstream context windows |
| Single CEUS contract file | CEUS subfiles by section | More granular, but adds unnecessary path complexity for the first journal |
| Category modules with risk tiers inside | Pure blacklist-only anti-AI file | Simpler, but too weak for later rewrite guidance and reviewer-facing explanation |

**Installation:**
```bash
# None - this phase is markdown-only.
```
</standard_stack>

<architecture_patterns>
## Architecture Patterns

### Recommended Project Structure
```text
references/
├── expression-patterns.md
├── expression-patterns/
│   ├── introduction-and-gap.md
│   ├── methods-and-data.md
│   ├── results-and-discussion.md
│   ├── conclusions-and-claims.md
│   └── geography-domain.md
├── anti-ai-patterns.md
├── anti-ai-patterns/
│   ├── vocabulary.md
│   ├── sentence-patterns.md
│   └── transitions-and-tone.md
└── journals/
    └── ceus.md
```

### Pattern 1: Stable entrypoint plus leaf modules
**What:** Keep the existing top-level file as the canonical starting point, then link to narrower modules for detailed loading.
**When to use:** Expression patterns and anti-AI references that will be consumed by multiple downstream Skills.
**Example:**
```markdown
# Academic Expression Patterns

## How to Use This Library
- Read this file first for scope and module selection.
- Load one leaf file when the task is limited to one writing scenario.

## Module Map
- `references/expression-patterns/introduction-and-gap.md`
- `references/expression-patterns/methods-and-data.md`
```

### Pattern 2: Journal contract with invariant headings
**What:** Keep one journal file per journal, but force a stable heading contract so every downstream Skill can read the same section names.
**When to use:** Journal-specific style and submission rules.
**Example:**
```markdown
# CEUS

## Submission Requirements
## Writing Preferences
## Quality Checks
## Section Guidance
```

### Pattern 3: Anti-AI category modules with embedded risk tiers
**What:** Organize anti-AI content by pattern type, then mark entries by `High Risk`, `Medium Risk`, and `Optional` within the file.
**When to use:** References that must support both lightweight substitution and richer rewrite reasoning.
**Example:**
```markdown
## Vocabulary Inflation

### High Risk
| Problem expression | Replacement |
|--------------------|-------------|
```

### Anti-Patterns to Avoid
- **Monolithic reference dumps:** One huge file makes later Skills over-read and increases context waste.
- **Unstable headings or path churn:** Renaming top-level paths breaks README guidance and future Skills.
- **Mixing hard requirements with soft style notes:** Journal contracts become unreliable if mandatory and optional rules are not separated.
- **Copying rules into every Skill:** Shared references lose value if guidance is duplicated and diverges across Skills.
</architecture_patterns>

<dont_hand_roll>
## Don't Hand-Roll

Problems that look simple but have better existing patterns in this repo:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Reference retrieval | Custom parser or indexing script | Stable markdown headings + predictable file paths | The project forbids build steps and Claude can already read markdown directly |
| Journal guidance reuse | Ad hoc per-Skill rule copies | Shared `references/journals/[journal].md` contract | Prevents drift across Translation, Polish, and Review Skills |
| Anti-AI rewrites | One-off prompt snippets in each Skill | Shared anti-AI reference with category modules | Keeps rewrite vocabulary consistent and auditable |

**Key insight:** This phase should establish contracts and structure, not invent new tooling.
</dont_hand_roll>

<common_pitfalls>
## Common Pitfalls

### Pitfall 1: Backwards-incompatible path changes
**What goes wrong:** Existing docs and the legacy Skill shell still point at the old reference paths, so later usage breaks silently.
**Why it happens:** Structure is improved without preserving stable entrypoints.
**How to avoid:** Keep `references/expression-patterns.md` and `references/journals/ceus.md` as canonical entrypoints even after modularization.
**Warning signs:** README examples and real files no longer match; future Skills must special-case old and new paths.

### Pitfall 2: Over-modularization without retrieval guidance
**What goes wrong:** Files become smaller, but nobody knows which leaf file to read for a given writing task.
**Why it happens:** Modularization is treated as a folder exercise rather than a retrieval contract.
**How to avoid:** Put module maps and loading advice in each overview file.
**Warning signs:** Leaf files exist, but overview files do not explain when to use them.

### Pitfall 3: Journal template becomes a dumping ground
**What goes wrong:** CEUS mixes submission rules, style suggestions, and random notes in one flat checklist.
**Why it happens:** Content grows without a stable contract.
**How to avoid:** Separate `Submission Requirements`, `Writing Preferences`, `Quality Checks`, and section-level guidance.
**Warning signs:** Skills cannot reliably target one heading for mandatory requirements versus style advice.

### Pitfall 4: Anti-AI reference collapses into a blacklist only
**What goes wrong:** The De-AI Skill later has words to avoid but no guidance on how to rewrite whole phrases or sentence patterns.
**Why it happens:** Phase 1 optimizes only for list-building and ignores future rewrite workflows.
**How to avoid:** Keep category modules and tier markers, even if downstream Skills default to lightweight replacements.
**Warning signs:** Entries explain what not to say but not what to say instead.
</common_pitfalls>

<code_examples>
## Code Examples

Verified patterns from existing project structure:

### Stable overview file
```markdown
# Academic Expression Patterns

## How to Use This Library
- Load this file when you need module selection guidance.
- Load a leaf file when you only need one rhetorical function.

## Module Map
- `references/expression-patterns/introduction-and-gap.md`
```

### Journal contract heading layout
```markdown
# CEUS (Computers, Environment and Urban Systems)

## Submission Requirements
## Writing Preferences
## Quality Checks
## Section Guidance
```

### Lightweight anti-AI replacement layer
```markdown
## Sentence Overclaim

| Risk | Problem expression | Replacement |
|------|--------------------|-------------|
| High Risk | groundbreaking | practically useful |
```
</code_examples>

<validation_architecture>
## Validation Architecture

Phase 1 does not need a unit-test framework. It needs repeatable structural validation that catches missing files, missing heading contracts, and broken doc references.

### Validation approach
- Use shell-based artifact checks (`Test-Path`, `rg`) instead of a language test runner.
- Validate overview files and leaf modules separately.
- Validate path references in README/CONTRIBUTING in the same phase, because docs are part of the retrieval contract.

### Recommended commands
- Quick feedback: `rg -n "^## " references\expression-patterns.md references\expression-patterns\*.md references\journals\ceus.md references\anti-ai-patterns.md references\anti-ai-patterns\*.md`
- Full feedback: `rg -n "Submission Requirements|Writing Preferences|Quality Checks|High Risk|Medium Risk|Optional|Recommended Expressions|Avoid Expressions|Usage Scenarios" references\*.md references\expression-patterns\*.md references\anti-ai-patterns\*.md references\journals\*.md README.md README_CN.md CONTRIBUTING.md CONTRIBUTING_CN.md`
- Link integrity: `rg -n "expression-patterns/|anti-ai-patterns|journals/ceus.md" README.md README_CN.md CONTRIBUTING.md CONTRIBUTING_CN.md references\*.md`

### What must be validated during execution
- Overview files remain usable on their own.
- Each leaf file is narrowly scoped and independently loadable.
- CEUS contract headings are invariant and separable.
- Anti-AI entries expose category grouping and risk tiers.
- Public docs point to real paths after restructuring.
</validation_architecture>

<sota_updates>
## State of the Art (2024-2025)

What's changed recently for this project domain:

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Large all-in-one Skill prompts | Lean Skills with on-demand reference loading | Reflected in project initialization research (2026-03-10) | Shared references become first-class infrastructure |
| Journal notes as loose checklists | Journal files as stable contracts | Current roadmap and Phase 1 context | Future Skills can target fixed headings reliably |
| Simple AI-sounding word blacklists | Risk-tiered rewrite guidance | Current roadmap + Phase 1 decisions | Later De-AI Skill can explain and rewrite, not only flag |

**New patterns to consider:**
- Keep top-level entrypoints stable while leaf modules evolve underneath.
- Treat docs (`README`, `CONTRIBUTING`) as part of the runtime reference contract, not just project marketing.

**Deprecated/outdated:**
- Embedding long reference tables directly inside SKILL.md files.
- Letting each Skill invent its own journal or anti-AI rule format.
</sota_updates>

<open_questions>
## Open Questions

1. **How much example density should each leaf file carry?**
   - What we know: Overview files should stay light, and leaf files should be independently useful.
   - What's unclear: The exact threshold where a leaf file becomes too long for comfortable on-demand loading.
   - Recommendation: Keep modules scenario-focused and let execution trim or split again if one file grows too broad.

2. **How explicit should README be about leaf paths versus overview paths?**
   - What we know: Top-level entrypoints must remain stable for current docs and future Skills.
   - What's unclear: Whether public docs should highlight only the entrypoint files or also teach advanced leaf-file loading.
   - Recommendation: Keep README simple and overview-oriented; reserve deeper path detail for CONTRIBUTING or the overview files themselves.
</open_questions>

<sources>
## Sources

### Primary (HIGH confidence)
- `.planning/phases/01-reference-libraries/01-CONTEXT.md` - locked decisions for Phase 1
- `.planning/ROADMAP.md` - phase goal, success criteria, and planned plan count
- `.planning/REQUIREMENTS.md` - requirement-level contract for FNDN-01 to FNDN-03
- `references/expression-patterns.md` - current expression library seed
- `references/journals/ceus.md` - current CEUS template seed
- `README.md` - public path contract already exposed to users

### Secondary (MEDIUM confidence)
- `.planning/research/SUMMARY.md` - prior project-level research on Claude Code Skill architecture and shared references
- `.planning/PROJECT.md` - project architecture and product constraints

### Tertiary (LOW confidence - needs validation)
- None - this phase research is primarily constrained by local project architecture rather than external APIs.
</sources>

<metadata>
## Metadata

**Research scope:**
- Core technology: Markdown reference architecture for Claude Code Skills
- Ecosystem: Shared references, journal templates, anti-AI guidance, repo docs
- Patterns: Hybrid loading, stable heading contracts, modular leaf files
- Pitfalls: Context bloat, unstable paths, mixed-purpose files, blacklist-only anti-AI design

**Confidence breakdown:**
- Standard stack: HIGH - markdown-only repo with existing reference paths already established
- Architecture: HIGH - directly supported by roadmap, context, and prior project research
- Pitfalls: HIGH - visible from current seed files and downstream dependency chain
- Code examples: HIGH - based on real repo paths and planned file contract

**Research date:** 2026-03-11
**Valid until:** 2026-04-10
</metadata>

---

*Phase: 01-reference-libraries*
*Research completed: 2026-03-11*
*Ready for planning: yes*
