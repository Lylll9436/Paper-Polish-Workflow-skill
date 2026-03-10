# Architecture Patterns

**Domain:** Multi-Skill Academic Writing Tool Suite for Claude Code
**Researched:** 2026-03-10

## Recommended Architecture

### High-Level Structure

```
.claude/skills/
  paper-translate/
    SKILL.md
  paper-polish/
    SKILL.md
  paper-deai/
    SKILL.md
  paper-review/
    SKILL.md
  paper-logic/
    SKILL.md
  paper-figures/
    SKILL.md
  paper-experiment/
    SKILL.md
  paper-abstract/
    SKILL.md
  paper-coverletter/
    SKILL.md
  paper-litreview/
    SKILL.md
  paper-visualize/
    SKILL.md

references/
  expression-patterns.md
  academic-phrases.md
  anti-ai-patterns.md
  journals/
    ceus.md
    ijgis.md
    cities.md
```

This is a **flat multi-Skill architecture** where each Skill is an independent directory under `.claude/skills/`, and shared reference material lives in a top-level `references/` directory that all Skills read via relative paths.

### Why This Structure (Not Alternatives)

**Alternative rejected: Single monolithic Skill with routing.**
A single Skill would exceed the 500-line recommended limit, load unnecessary context for every task, and create a brittle routing layer. The official best practices explicitly recommend keeping SKILL.md under 500 lines and splitting concerns into separate Skills.

**Alternative rejected: Nested skills (skills-within-skills).**
Claude Code does not support nesting skills. Each Skill must be a top-level directory with its own SKILL.md. Composition happens via Claude invoking one Skill from within another using the Skill tool, but this is fragile and not recommended for tightly coupled workflows.

**Alternative rejected: All resources bundled inside each Skill directory.**
Duplicating expression-patterns.md and journal templates inside every Skill's directory would create maintenance nightmares. A shared `references/` directory at the repo root is the correct pattern -- every Skill can read these files using `Read` tool with relative paths from the project root.

## Component Boundaries

| Component | Type | Responsibility | Location |
|-----------|------|---------------|----------|
| Translation Skill | Skill | Chinese-to-English academic translation with LaTeX output | `.claude/skills/paper-translate/` |
| Polish Skill | Skill | Structure-to-expression paper polishing workflow | `.claude/skills/paper-polish/` |
| De-AI Skill | Skill | Detect and rewrite AI-generated patterns | `.claude/skills/paper-deai/` |
| Review Skill | Skill | Reviewer-perspective paper critique | `.claude/skills/paper-review/` |
| Logic Skill | Skill | Cross-section logic chain verification | `.claude/skills/paper-logic/` |
| Figures Skill | Skill | Figure/table caption generation and optimization | `.claude/skills/paper-figures/` |
| Experiment Skill | Skill | Experiment result analysis and discussion | `.claude/skills/paper-experiment/` |
| Abstract Skill | Skill | Abstract and cover letter generation | `.claude/skills/paper-abstract/` |
| Cover Letter Skill | Skill | Cover letter generation for journal submission | `.claude/skills/paper-coverletter/` |
| Lit Review Skill | Skill | Literature search via Semantic Scholar MCP | `.claude/skills/paper-litreview/` |
| Visualization Skill | Skill | Experimental result visualization guidance | `.claude/skills/paper-visualize/` |
| Expression Patterns | Reference | Curated academic phrase library | `references/expression-patterns.md` |
| Anti-AI Patterns | Reference | AI-detectable patterns and human alternatives | `references/anti-ai-patterns.md` |
| Journal Templates | Reference | Per-journal requirements, style guides, word budgets | `references/journals/*.md` |

### Component Independence Levels

| Skill | Fully Independent | Reads References | May Chain After |
|-------|-------------------|------------------|-----------------|
| Translation | Yes | expression-patterns, journal template | -- |
| Polish | Yes | expression-patterns, journal template | Translation |
| De-AI | Yes | anti-ai-patterns | Polish, Translation |
| Review | Yes | journal template | -- |
| Logic | Yes | -- | Polish |
| Figures | Yes | journal template | -- |
| Experiment | Yes | expression-patterns | -- |
| Abstract | Yes | expression-patterns, journal template | Polish |
| Cover Letter | Yes | journal template | -- |
| Lit Review | Yes (uses Semantic Scholar MCP) | -- | -- |
| Visualization | Yes | -- | Experiment |

**Every Skill must be usable standalone.** Chaining is a user workflow choice, never a technical dependency. A user can invoke `/paper-deai` without having run `/paper-polish` first.

## Data Flow

### How Skills Access Shared Resources

```
User invokes /paper-polish
    |
    v
Claude loads paper-polish/SKILL.md
    |
    v
SKILL.md instructs: "Read references/journals/{journal}.md for journal requirements"
    |
    v
Claude uses Read tool on references/journals/ceus.md
    |
    v
SKILL.md instructs: "Consult references/expression-patterns.md for academic phrases"
    |
    v
Claude uses Read tool on references/expression-patterns.md
    |
    v
Claude executes polishing workflow with loaded context
```

**Key principle: Progressive disclosure.** SKILL.md contains only the workflow logic and pointers to reference files. Claude loads reference files only when needed, conserving context window tokens.

### How Journal Templates Flow

```
references/journals/ceus.md
    |
    +-- Read by: paper-polish (style check step)
    +-- Read by: paper-translate (formatting step)
    +-- Read by: paper-review (journal-specific criteria)
    +-- Read by: paper-abstract (word limits, highlight format)
    +-- Read by: paper-coverletter (journal-specific requirements)
    +-- Read by: paper-figures (caption format requirements)
```

Each journal template is read by multiple Skills but owned by no single Skill. This is the shared resource pattern.

### How Expression Patterns Flow

```
references/expression-patterns.md
    |
    +-- Read by: paper-polish (expression options in Step 3)
    +-- Read by: paper-translate (target expression style)
    +-- Read by: paper-deai (human-sounding alternatives)
    +-- Read by: paper-experiment (results presentation)
    +-- Read by: paper-abstract (abstract phrasing)
```

### User Workflow: Typical Paper Lifecycle

```
1. /paper-translate   (draft Chinese -> English)
2. /paper-polish      (structure -> logic -> expression)
3. /paper-deai        (reduce AI artifacts)
4. /paper-logic       (verify cross-section consistency)
5. /paper-figures     (optimize captions)
6. /paper-experiment  (strengthen discussion)
7. /paper-review      (self-review before submission)
8. /paper-abstract    (finalize abstract)
9. /paper-coverletter (generate cover letter)
```

This is a recommended order, not an enforced one. Users can enter at any step.

## Patterns to Follow

### Pattern 1: Shared Resource Reference via Read Tool

**What:** Each SKILL.md references shared resources using explicit Read instructions, not embedded content.

**When:** Always, for any content shared between 2+ Skills.

**Example in SKILL.md:**
```markdown
## Journal-Specific Requirements

Before polishing, read the target journal's requirements:

1. Ask the user which journal they are targeting
2. Read the journal template: `references/journals/{journal-name}.md`
3. Apply word limits, style rules, and format requirements from the template

Currently supported journals:
- **CEUS**: `references/journals/ceus.md`
- **IJGIS**: `references/journals/ijgis.md`
- **Cities**: `references/journals/cities.md`
```

**Why:** Keeps SKILL.md under 500 lines. Ensures journal templates are maintained in one place. New journals only need a new file in `references/journals/`, no Skill modifications.

### Pattern 2: Bilingual Trigger Descriptions

**What:** Every SKILL.md frontmatter includes both English and Chinese trigger keywords in the description field.

**When:** Always, because the target user works in both languages.

**Example:**
```yaml
---
name: paper-polish
description: Systematic workflow for polishing academic paper sections. Top-down approach from structure to logic to expression. Triggers: polish paper, revise paper, improve writing, 润色论文, 精修论文, 改善论文表达
---
```

**Why:** Claude uses the description to decide when to activate a Skill. Without Chinese triggers, the Skill won't activate for Chinese-language requests. The official docs confirm that description quality directly impacts activation rates (20% to 90% improvement with good descriptions).

### Pattern 3: Interactive Selection via AskUserQuestion

**What:** Use `AskUserQuestion` (Claude Code's interactive question tool) for multi-option expression selection instead of text-based menus.

**When:** Whenever presenting 2-4 alternatives for user selection (expression options, transition words, structure choices).

**Example in SKILL.md:**
```markdown
### Expression Selection

For each sentence, provide 2-4 expression options using the AskUserQuestion tool:
- Present the sentence's logical function as the question
- Each option is a complete rewritten sentence
- Always include a "Keep original" option
- Allow custom text input

Process 3-5 sentences per batch for efficiency.
```

**Why:** Reduces conversation turns, provides cleaner UX than text-based selection, and the user can always type a custom answer.

### Pattern 4: Journal Template Structure (Extensibility Pattern)

**What:** Every journal template follows an identical structure so Skills can parse them predictably.

**When:** Creating any new journal template in `references/journals/`.

**Example template structure:**
```markdown
# [Journal Name] ([Abbreviation])

## Journal Requirements

| Requirement | Specification |
|-------------|---------------|
| Total word limit | [number] words |
| Abstract | [limit] words |
| Highlights | [count] items, each [char limit] characters |
| Review type | [blind type] |

## Word Budget

| Section | Suggested Words |
|---------|-----------------|
| Abstract | [range] |
| Introduction | [range] |
| [etc.] | [etc.] |

## Style Checklist

- [ ] [Journal-specific requirement 1]
- [ ] [Journal-specific requirement 2]

## Highlights Format (if applicable)

### Rules
- [Rule 1]
- [Rule 2]

### Examples
| # | Highlight | Characters | Status |
|---|-----------|------------|--------|
| 1 | [example] | [count] | [ok/over] |
```

**Why:** Predictable structure means Skills can reference "the Word Budget section of the journal template" and reliably find it. New journals are trivially added by copying this template.

## Anti-Patterns to Avoid

### Anti-Pattern 1: Embedding Reference Content in SKILL.md

**What:** Putting expression patterns, journal requirements, or other shared content directly in SKILL.md.

**Why bad:** Exceeds the 500-line limit. Creates duplication across Skills. Updates require editing multiple files. Wastes context window tokens when the content isn't needed for the current step.

**Instead:** Keep SKILL.md focused on workflow logic. Reference shared files with explicit Read instructions.

### Anti-Pattern 2: Skills That Depend on Other Skills' Output

**What:** Building Skills that require specific output format from a previous Skill (e.g., De-AI Skill expects a `*_polished.md` file from Polish Skill).

**Why bad:** Creates hidden coupling. Users can't use De-AI on text they wrote themselves or polished with a different tool. Breaks the "every Skill is standalone" principle.

**Instead:** Each Skill should accept any input text. Ask the user what file or text to work on. Don't assume a prior Skill has run.

### Anti-Pattern 3: Overly Complex Routing in a Single Skill

**What:** A "paper-assistant" master Skill that tries to detect intent and route to sub-workflows.

**Why bad:** Claude Code already has Skill discovery via descriptions. Adding a routing layer duplicates this and adds a failure mode. The master Skill would need to load all sub-workflows into context, defeating progressive disclosure.

**Instead:** Trust Claude's built-in Skill activation. Write specific descriptions for each Skill so Claude selects the right one.

### Anti-Pattern 4: Deeply Nested Reference Files

**What:** SKILL.md references `docs/advanced.md`, which references `docs/details/specifics.md`.

**Why bad:** Official best practices explicitly warn against this. Claude may partially read nested files (using `head -100`), resulting in incomplete information.

**Instead:** Keep all references one level deep from SKILL.md. SKILL.md points directly to every file it might need.

## Scalability Considerations

| Concern | At 5 Skills | At 10+ Skills | At 20+ Skills |
|---------|-------------|---------------|---------------|
| Context budget for descriptions | No concern (~2.5K chars) | Approaching budget (~5K chars) | May hit 16K char budget; optimize descriptions |
| Reference file size | Small, fast to load | Consider splitting expression-patterns by section type | Split into domain-specific files |
| Journal templates | 1-2 journals | 3-5 journals, still manageable | Index file listing available journals may help |
| User discoverability | Users remember Skill names | Need clear naming convention | Consider a "paper-help" meta-Skill listing all available Skills |

### Context Budget Note

Claude Code's Skill description budget defaults to 2% of context window (fallback: 16,000 characters). With 11 Skills at ~150 chars per description, total is ~1,650 characters -- well within budget. This architecture supports up to ~100 Skills before hitting the default limit.

## Suggested Build Order

Based on component dependencies and the shared resource pattern:

```
Phase 1: Foundation (must come first)
  references/expression-patterns.md    -- shared by 5+ Skills
  references/journals/ceus.md          -- shared by 6+ Skills
  references/anti-ai-patterns.md       -- shared by 2+ Skills
  Journal template structure spec      -- defines the contract

Phase 2: Core Skills (highest value, most commonly used)
  paper-polish/SKILL.md                -- redesign from scratch per PROJECT.md
  paper-translate/SKILL.md             -- translation is the entry point for Chinese researchers

Phase 3: Quality Skills (build on polishing foundation)
  paper-deai/SKILL.md                  -- typically runs after polish/translate
  paper-review/SKILL.md                -- reviewer simulation for self-check

Phase 4: Section-Specific Skills
  paper-abstract/SKILL.md
  paper-figures/SKILL.md
  paper-experiment/SKILL.md
  paper-logic/SKILL.md

Phase 5: Support Skills
  paper-coverletter/SKILL.md
  paper-litreview/SKILL.md
  paper-visualize/SKILL.md

Phase 6: Expansion
  Additional journal templates (ijgis.md, cities.md)
  Additional expression pattern categories
  README and installation guide
```

**Build order rationale:**
1. **References first** because multiple Skills depend on them. Building Skills before references means you'd need to revisit each Skill to add reference links.
2. **Polish and Translate first** because they are the most-used Skills and the polishing Skill is explicitly flagged for redesign in PROJECT.md.
3. **De-AI and Review next** because they represent the quality assurance layer that validates polish/translate output.
4. **Section-specific Skills in parallel** because they are independent of each other.
5. **Support Skills last** because they add value but aren't core to the polishing workflow.
6. **Expansion last** because the architecture should be validated with CEUS before adding more journals.

## Sources

- [Extend Claude with skills - Claude Code Docs](https://code.claude.com/docs/en/skills) (HIGH confidence -- official documentation)
- [Skill authoring best practices - Claude API Docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) (HIGH confidence -- official documentation)
- [Anthropic Skills Repository](https://github.com/anthropics/skills) (HIGH confidence -- official reference implementations)
- [Inside Claude Code Skills: Structure, prompts, invocation](https://mikhail.io/2025/10/claude-code-skills/) (MEDIUM confidence -- third-party deep dive, verified against official docs)
- [awesome-ai-research-writing](https://github.com/Leey21/awesome-ai-research-writing) (MEDIUM confidence -- reference project for prompt design, not architecture)
- [Claude Skills and CLAUDE.md: a practical 2026 guide for teams](https://www.gend.co/blog/claude-skills-claude-md-guide) (MEDIUM confidence -- community guide)
