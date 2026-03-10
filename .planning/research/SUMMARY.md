# Research Summary: Paper Polish Workflow

**Domain:** Multi-Skill Academic Writing AI Tool Suite for Claude Code
**Researched:** 2026-03-10
**Overall confidence:** HIGH

## Executive Summary

This project builds a suite of 10-11 independent Claude Code Skills covering the full academic paper lifecycle: translation, polishing, de-AI rewriting, reviewer simulation, literature search, logic verification, figure/table captions, experiment analysis, abstract/cover letter generation, and visualization guidance. The target users are Chinese researchers publishing in English-language geography and urban science journals, starting with CEUS (Computers, Environment and Urban Systems).

The architecture follows Claude Code's official recommendations for multi-Skill projects: each capability is a self-contained directory with its own SKILL.md (under 300 lines), and shared resources (expression patterns, anti-AI patterns, journal templates) live in a top-level `references/` directory accessible to all Skills via the Read tool. This flat structure avoids the monolithic single-Skill approach (which would exceed context window budgets) and avoids fragmentation (by sharing conventions and reference files).

The critical architectural decision is that Skills must be fully independent -- any Skill can be invoked without prior Skills having run. Chaining (translate -> polish -> de-AI -> review) is a user workflow choice, not a technical requirement. This independence is achieved by having every Skill accept arbitrary input text and reference shared journal templates for style rules.

The reference project (awesome-ai-research-writing) provides 17 prompt templates from top Chinese research institutions, which serve as design inspiration but NOT as architectural templates. Their single-file prompt approach does not map to Claude Code's progressive disclosure model. The key adaptation is converting flat prompts into Skills with lean SKILL.md files and on-demand reference loading.

## Key Findings

**Stack:** Pure markdown Skills (no npm/build), Claude Code built-in tools (Read/Write/Edit/AskUserQuestion), Semantic Scholar MCP for citations, YAML frontmatter for discovery.

**Architecture:** Flat multi-Skill layout under `.claude/skills/`, shared `references/` directory at project root, journal template contract enforcing consistent structure across all journal files, progressive disclosure (SKILL.md points to references, Claude loads on-demand).

**Critical pitfall:** Context window bloat. Academic papers are long (8000+ words). Skills must be aggressively lean (under 300 lines) and load reference material on-demand, never inline. The existing 347-line polishing SKILL.md is already borderline.

## Implications for Roadmap

Based on research, suggested phase structure:

1. **Foundation: Shared References and Skill Template** - Build the reference files and establish the Skill template pattern before any individual Skill.
   - Addresses: Expression patterns, journal templates, anti-AI patterns, Skill conventions
   - Avoids: Pitfall 4 (context bloat), Pitfall 7 (fragmentation), Pitfall 11 (template proliferation)

2. **Core Skills: Polish and Translate** - The two most commonly used capabilities, and the Polish Skill explicitly needs redesign.
   - Addresses: Paper polishing (redesigned), Chinese-to-English translation
   - Avoids: Pitfall 2 (voice erasure), Pitfall 5 (rigid workflow), Pitfall 6 (expression crutch)

3. **Quality Skills: De-AI and Review** - The quality assurance layer that validates output from Phase 2.
   - Addresses: De-AI detection/rewriting, reviewer simulation
   - Avoids: Pitfall 8 (arms race), Pitfall 3 (useless in practice)

4. **Section-Specific Skills** - Independent Skills for specific paper sections, buildable in parallel.
   - Addresses: Abstract, figures/tables, experiment analysis, logic verification
   - Avoids: Pitfall 3 (useless in practice) by testing with real papers

5. **Support Skills** - Lower-priority capabilities that add value but aren't core.
   - Addresses: Cover letter, literature search, visualization
   - Avoids: Pitfall 9 (Semantic Scholar dependency) with graceful degradation

6. **Expansion** - Additional journals and documentation.
   - Addresses: IJGIS, Cities journal templates; README and installation guide
   - Avoids: Pitfall 11 (template proliferation) by validating the pattern with CEUS first

**Phase ordering rationale:**
- References must come first because 5+ Skills depend on expression patterns and 6+ Skills depend on journal templates. Building Skills before references means revisiting each Skill later.
- Polish and Translate are the highest-value Skills and the most complex. Polish is explicitly flagged for redesign. Building them early validates the Skill template pattern.
- De-AI and Review are quality assurance tools that logically follow polishing. They also exercise the anti-AI patterns reference.
- Section-specific Skills are independent and can be built in parallel. They have no inter-dependencies.
- Support Skills are deferred because they have external dependencies (Semantic Scholar MCP) or lower urgency (cover letters, visualization).
- Journal expansion is last because the template pattern must be validated with CEUS before replication.

**Research flags for phases:**
- Phase 2 (Polish redesign): Needs deeper research into flexible workflow patterns. The current 6-step flow is acknowledged as too rigid, and the redesign approach is not yet defined.
- Phase 3 (De-AI Skill): Needs research into current AI detection patterns and what constitutes "humanization" vs. quality improvement.
- Phase 5 (Lit Review Skill): Needs investigation of Semantic Scholar MCP server availability, configuration requirements, and rate limits in Claude Code.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | Claude Code Skills are fully documented; no ambiguity in technology choices |
| Features | HIGH | Feature list comes directly from PROJECT.md and the reference project |
| Architecture | HIGH | Multi-Skill with shared references is the officially recommended pattern; verified against official docs and best practices |
| Pitfalls | HIGH | Triangulated from official docs, academic research on AI writing, and analysis of the existing codebase |
| Build Order | MEDIUM | Logical dependency analysis is sound, but actual timing depends on user priorities and effort estimates |

## Gaps to Address

- **AskUserQuestion tool availability**: The existing SKILL.md references `mcp_question` which may not exist in current Claude Code. Need to verify what interactive selection tools are actually available and design fallback patterns.
- **Polish Skill redesign approach**: The current flow is acknowledged as too rigid, but the specific redesign (how to support both guided and quick-fix modes) needs prototyping.
- **Anti-AI patterns content**: The reference file needs to be created from scratch. Research into current AI detection patterns and effective human-sounding alternatives is needed during Phase 1.
- **Semantic Scholar MCP configuration**: How to configure and verify the MCP server in Claude Code needs documentation. May vary by user setup.
- **Cross-platform compatibility**: The project README mentions both Claude Code and OpenCode. Need to verify whether Skills work identically in both platforms, or if platform-specific adaptations are needed.

## Sources

- [Extend Claude with skills - Claude Code Docs](https://code.claude.com/docs/en/skills) (HIGH confidence)
- [Skill authoring best practices - Claude API Docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) (HIGH confidence)
- [Anthropic Skills Repository](https://github.com/anthropics/skills) (HIGH confidence)
- [awesome-ai-research-writing](https://github.com/Leey21/awesome-ai-research-writing) (MEDIUM confidence)
- [Inside Claude Code Skills](https://mikhail.io/2025/10/claude-code-skills/) (MEDIUM confidence)
- [Claude Agent Skills Deep Dive](https://leehanchung.github.io/blogs/2025/10/26/claude-skills-deep-dive/) (MEDIUM confidence)
