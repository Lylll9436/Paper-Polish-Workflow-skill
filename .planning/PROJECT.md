# Paper Polish Workflow

## What This Is

A complete Claude Code Skill suite for academic paper writing, covering the full lifecycle: Chinese-to-English translation, English polishing, de-AI detection and rewriting, peer reviewer simulation, abstract generation, experiment analysis and discussion generation, figure/table caption optimization, logic chain verification, literature search and BibTeX generation, cover letter generation, and visualization recommendation. 11 independent Skills share a modular reference library (expression patterns, anti-AI patterns, journal templates). Primarily for geography/urban science research papers; open-sourced for the community.

## Core Value

Every Skill must produce output that is directly usable in a real paper submission — no toy demos, no generic advice. Quality of prompt engineering and execution flow determines whether this tool suite is actually useful or just another prompt collection.

## Requirements

### Validated

- ✓ Shared expression patterns library (sentence starters, transitions, hedging, results, conclusions) with bilingual examples — v1.0
- ✓ Journal template system with CEUS as first template, strict contract with invariant headings — v1.0
- ✓ Anti-AI patterns library with risk-tiered vocabulary blacklist and human-sounding alternatives — v1.0
- ✓ Skill template conventions: YAML frontmatter format, ~300-line budget, progressive disclosure, 4 interaction modes — v1.0
- ✓ Chinese-to-English translation Skill with LaTeX output, CEUS style, anti-AI patterns loaded proactively — v1.0
- ✓ English polishing Skill with quick-fix mode (direct) and guided mode (structure → logic → expression), LaTeX annotation — v1.0
- ✓ De-AI Skill: two-phase detect-then-rewrite, risk-tagged across vocabulary/sentence-patterns/transitions, domain term protection — v1.0
- ✓ Reviewer simulation Skill: 5-dimension scoring, three-part concern format (problem + why + suggestion), bilingual output — v1.0
- ✓ Abstract generation/optimization Skill: 5-sentence Farquhar formula, labeled output, direct and restructure paths — v1.0
- ✓ Experiment analysis Skill: two-phase findings → discussion, evidence-before-interpretation rule, anti-hallucination placeholders — v1.0
- ✓ Figure/table caption Skill: geography-conditional Ask Strategy, CRS/legend/study area awareness, .tex file output — v1.0
- ✓ Logic verification Skill: argument chain table, four-category typed issues (AC/UC/TI/NC), bilingual output — v1.0
- ✓ Literature search Skill: Semantic Scholar MCP, BibTeX from MCP data only, mandatory anti-hallucination verification — v1.0
- ✓ Cover letter Skill: journal-aware contribution statement, CEUS scope alignment, file/paste dual input — v1.0
- ✓ Visualization recommendation Skill: geography-conditional chart types (choropleth/spatial scatter/KDE), direct mode only — v1.0
- ✓ Bilingual README with installation guide, 11-Skill inventory table, and end-to-end quick-start workflows — v1.0

### Active

## Current Milestone: v2.0 Repo-to-Paper & Bilingual Enhancement

**Goal:** Enable automatic paper draft generation from experiment repositories with top-down structure-first workflow, fix interactive question patterns, and add bilingual paragraph-by-paragraph comparison across Skills.

**Target features:**
- Repo-to-Paper draft generator: full repo scan → top-down generation (H1→H2→H3→body) with user checkpoints at each level
- Integrated literature search: Semantic Scholar references collected by H2 stage, saved as ref files with metadata + abstracts
- Bilingual paragraph-by-paragraph comparison output (English + Chinese)
- Fix AskUserQuestion usage in paper-polish-workflow (Claude using plain dialogue instead of structured questions)

### Out of Scope

- Web UI or standalone application — this is a Claude Code Skill project, not a SaaS
- Real-time collaboration — single-user workflow
- Non-academic writing (blog posts, marketing copy) — focused on research papers
- Automated paper generation from scratch — assists human writers, not replaces them
- OpenCode/Cursor support — Claude Code only; other platforms are v2+ consideration
- Image/figure generation — requires image generation models; out of scope for text Skills
- Plagiarism detection — requires specialized databases (Turnitin, iThenticate)

## Context

**v1.0 shipped:** 2026-03-13 — 11 Skills, 15 plans across 10 phases, 16/16 requirements satisfied.

**Current codebase:**
- 11 SKILL.md files in `.claude/skills/*/` (~2,400 lines total)
- 8 reference files in `references/` (expression patterns, anti-AI patterns, CEUS journal template)
- Bilingual README.md at repository root
- Platform: Claude Code only (`.claude/skills/` format)

**Tech stack:**
- Pure markdown Skills (no build steps, no npm packages)
- Shared reference library loaded on-demand via Read tool
- Semantic Scholar MCP for literature search (SUPP-02)
- AskUserQuestion for interactive mode Skills

**Known issues from v1.0 audit (tech debt):**
- `skill-conventions.md` lacks documented escape hatch for Skills with `required: []`; logic-skill and visualization-skill correctly use it but convention doesn't formalize this
- `literature-skill` uses vendor-specific tool name "Semantic Scholar MCP" in frontmatter instead of capability category "External MCP" per conventions
- `cover-letter-skill` has `required: []` but journal template is functionally mandatory; should move CEUS from leaf_hints to required

**Target journals:** Geography and urban science — CEUS (v1.0 template complete), IJGIS, Cities (v2+ candidates)

## Constraints

- **Platform**: Claude Code only — Skills must follow `.claude/skills/[name]/SKILL.md` format
- **No external dependencies**: Skills are pure markdown prompts, no npm packages or build steps
- **Journal specificity**: First-class support for geography/urban journals; other fields secondary
- **Language**: Skills work in both Chinese and English (bilingual triggers and instructions)
- **Tool access**: Skills may use `AskUserQuestion`, `Read`/`Write`/`Edit`, `Grep`/`Glob`, `Bash`, and `mcp__semantic-scholar__*`

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Multi-Skill architecture (not single Skill with routing) | Each capability is independent and self-contained; users invoke what they need directly | ✓ Good — 11 Skills are independently invokable with no shared state issues |
| Adapt prompts from awesome-ai-research-writing as base | High-quality prompts from top researchers; adapt for Claude Code Skill format with interactivity | ✓ Good — all 11 Skills functional and convention-compliant |
| CEUS as first journal template | User's primary target journal; establishes template pattern for others | ✓ Good — CEUS contract used by translation, polish, cover letter, reviewer Skills |
| Redesign polishing flow from scratch | Current 6-step flow was too rigid; new design should be more flexible | ✓ Good — quick-fix + guided modes serve different user needs |
| Modular reference library (5 expression modules, 3 anti-AI modules) | On-demand loading keeps Skill context narrow; each Skill loads only what it needs | ✓ Good — avoids context bloat; each Skill loads 1-3 leaf files max |
| Strict ~300-line Skill budget | Keeps Skills readable and context-efficient; forces progressive disclosure | ✓ Good — all 11 Skills within budget |
| Anti-hallucination citation rule (MCP data only) | Prior incident pattern: AI-generated citations have ~40% error rate | ✓ Good — literature-skill refuses to infer BibTeX fields from prior knowledge |
| Two-phase workflow for De-AI and Experiment Skills | User must confirm findings before generation begins; reduces wasted generation | ✓ Good — adopted as pattern for analysis Skills |
| Bilingual output pattern (inline blockquotes for Chinese) | Reviewer simulation and logic Skills serve Chinese-primary users | ✓ Good — inline format more readable than appended section |
| Single-file bilingual README (symmetric halves) | One file is easier to maintain than README.md + README_CN.md | ✓ Good — v1.0 README.md replaces legacy README_CN.md |

---
*Last updated: 2026-03-17 after v2.0 milestone started*
