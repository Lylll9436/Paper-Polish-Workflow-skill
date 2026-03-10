# Paper Polish Workflow

## What This Is

A complete academic writing AI tool suite for Claude Code, covering the full paper lifecycle: translation, polishing, de-AI detection, reviewer simulation, literature search, logic verification, figure/table captions, experiment analysis, abstract/cover letter generation, and visualization guidance. Each capability is an independent Skill (SKILL.md) under `.claude/skills/`. Primarily for personal use in geography/urban science research, open-sourced for the community.

## Core Value

Every Skill must produce output that is directly usable in a real paper submission — no toy demos, no generic advice. Quality of the prompt engineering and execution flow determines whether this tool suite is actually useful or just another prompt collection.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Chinese-to-English academic translation with LaTeX output
- [ ] English paper polishing (structure → logic → expression, redesigned flow)
- [ ] De-AI detection and rewriting (reduce AI-generated traces)
- [ ] Reviewer-perspective paper review with actionable feedback
- [ ] Literature search and review via Semantic Scholar MCP
- [ ] Logic chain verification across sections
- [ ] Figure and table caption generation/optimization
- [ ] Experiment result analysis and discussion generation
- [ ] Abstract and cover letter generation
- [ ] Visualization recommendation for experimental results
- [ ] Journal template system (CEUS as first, extensible)
- [ ] Shared reference library (expression patterns, academic phrases)
- [ ] Project-level README and installation guide

### Out of Scope

- Web UI or standalone application — this is a Claude Code Skill project, not a SaaS
- Real-time collaboration — single-user workflow
- Non-academic writing (blog posts, marketing copy) — focused on research papers
- Automated paper generation from scratch — this assists human writers, not replaces them

## Context

- **Current state**: Existing repo has a basic polishing SKILL.md, one CEUS journal template, one expression patterns reference, one example session. The user considers the entire current implementation as a "shell" that needs to be redone.
- **Reference project**: [awesome-ai-research-writing](https://github.com/Leey21/awesome-ai-research-writing) — a collection of 17 prompt templates covering translation, polishing, de-AI, logic checking, figure/table captions, experiment analysis, reviewer simulation, etc. These prompts are from top research labs (MSRA, Seed, SH AI Lab) and universities (PKU, USTC, SJTU). This project will adapt and improve upon these prompt designs as Claude Code Skills.
- **Target journals**: Geography and urban science journals — CEUS, IJGIS, Cities, etc.
- **Platform**: Claude Code (`.claude/skills/` directory structure)
- **Architecture decision**: Multiple independent Skill files. Each capability (polish, translate, review, etc.) is its own SKILL.md with its own trigger conditions. Shared resources (expression patterns, journal templates) live in `references/`.

## Constraints

- **Platform**: Claude Code only — Skills must follow `.claude/skills/[name]/SKILL.md` format
- **No external dependencies**: Skills are pure markdown prompts, no npm packages or build steps
- **Journal specificity**: First-class support for geography/urban journals; other fields are secondary
- **Language**: Skills should work in both Chinese and English interaction (bilingual triggers and instructions)
- **Tool access**: Skills may use `AskUserQuestion` for interactive selection, `Read`/`Write`/`Edit` for file operations, `Grep`/`Glob` for search, `Bash` for shell commands, and `mcp__semantic-scholar__*` for literature search

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Multi-Skill architecture (not single Skill with routing) | Each capability is independent and self-contained; users invoke what they need directly | -- Pending |
| Adapt prompts from awesome-ai-research-writing as base | High-quality prompts from top researchers; adapt for Claude Code Skill format with interactivity | -- Pending |
| CEUS as first journal template | User's primary target journal; establishes template pattern for others | -- Pending |
| Redesign polishing flow from scratch | Current 6-step flow is too rigid; new design should be more flexible and context-aware | -- Pending |

---
*Last updated: 2026-03-10 after initialization*
