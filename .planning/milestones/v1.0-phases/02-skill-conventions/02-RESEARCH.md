# Phase 2: Skill Conventions - Research

**Researched:** 2026-03-11
**Domain:** Markdown authoring conventions for Claude Code academic writing Skills
**Confidence:** HIGH

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- Use a strict frontmatter contract for future Skills.
- Every Skill should include at least `name`, `description`, `triggers`, `tools`, `references`, `input_modes`, and `output_contract`.
- `triggers` should be expressed as trigger intent plus example phrases.
- `tools` should list the tools a Skill is allowed to depend on by default; detailed tool usage belongs in the body.
- `input_modes` and `output_contract` should appear as short enums or summaries in frontmatter, with operational detail in the body.
- Skills may directly load a leaf reference file when the correct leaf is obvious.
- When the correct leaf file is not obvious, Skills should start from the stable overview file.
- The `references` field should list stable entrypoint files plus optional leaf-file hints.
- `SKILL.md` may retain a moderate amount of inline guidance, but long reusable reference content should stay under `references/`.
- If a reference does not match the current task, the default behavior is to stop and ask the user for clarification.
- The default interaction posture should be ask first, then act.
- Any branch that materially changes output direction should be clarified before the main workflow proceeds.
- If structured interaction tools are unavailable, fallback should be 1 to 3 plain-text questions covering only the highest-impact gaps.
- Skill conventions should explicitly support `interactive`, `guided`, `direct`, and `batch` modes.
- Use a semi-templated Skill body with a required backbone plus room for skill-type-specific additions.
- Required section types should include `Purpose`, `Trigger`, `Modes`, `References`, `Ask Strategy`, `Workflow`, `Output Contract`, `Edge Cases`, and `Fallbacks`.
- Workflow detail may vary by Skill type: direct Skills can stay shorter, guided Skills can use fuller step-by-step flow.
- Keep at least one minimal example strongly recommended in each Skill.

### Claude's Discretion
- Exact YAML field ordering and naming style within the strict frontmatter contract.
- Exact wording and enforcement of the line-budget rule.
- Whether conventions and the example skeleton are separate but linked files or a single combined artifact.

### Deferred Ideas (OUT OF SCOPE)
- None - discussion stayed within phase scope.
</user_constraints>

<research_summary>
## Summary

Phase 2 is a contract-definition problem rather than an implementation-framework problem. The repo already has one legacy Skill shell, a completed modular reference system, and public documentation that assumes markdown-only authoring. The standard approach for this phase is therefore to create one normative conventions document plus one copyable example skeleton, both stored at stable repo paths and written so later feature phases can follow them directly.

The most important research result is that conventions should be capability-oriented, not tool-name-fragile. The legacy shell is only 259 lines, which proves a disciplined line budget is realistic, but it also hard-codes `mcp_question` and related tool names. Given the explicit project blocker around `AskUserQuestion` availability, conventions should define what a Skill needs to accomplish (structured interaction, fallback text questions, reference loading) and only secondarily mention tool choices.

The second key result is that the conventions artifact and the example skeleton should be separate but linked. The conventions document needs to be normative and concise enough for maintenance, while the example skeleton needs to be directly copyable by future phases without forcing maintainers to extract code blocks from a longer prose file.

**Primary recommendation:** Create a canonical conventions document plus a linked example skeleton, and make README / CONTRIBUTING point maintainers to those files as the default starting point for all new Skills.
</research_summary>

<standard_stack>
## Standard Stack

The established tools/patterns for this phase:

### Core
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| Markdown conventions docs | N/A | Define project-wide authoring rules | Matches markdown-only repo and future Skill authoring workflow |
| YAML frontmatter | N/A | Encode Skill-level contract metadata | Already used by existing Skill shell and required by roadmap success criteria |
| Stable `references/` entrypoints | N/A | Share reusable constraints and authoring guidance | Aligns with Phase 1 reference architecture |
| Example skeleton file | N/A | Provide a copyable starting point for later phases | Prevents every future Skill from inventing its own body structure |

### Supporting
| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| Capability-based tool policy | N/A | Decouple conventions from one specific interaction tool name | When tool availability differs between environments |
| Mode taxonomy (`interactive/guided/direct/batch`) | N/A | Keep workflows consistent across future Skills | When a Skill supports multiple invocation styles |
| Line-budget rule | N/A | Keep `SKILL.md` lean and reference-driven | When preventing reference bloat or monolithic prompts |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Separate conventions doc + skeleton | Single giant conventions file with embedded skeleton | Easier to keep in one place, but worse for copyability and maintenance |
| Strict frontmatter contract | Minimal frontmatter with body-only details | Shorter, but too weak for consistency and discovery across many Skills |
| Capability-oriented tool rules | Hard-coded tool names in each template | Faster to draft, but brittle when tools differ or become unavailable |

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
├── skill-conventions.md
├── skill-skeleton.md
├── expression-patterns.md
├── anti-ai-patterns.md
└── journals/
    └── ceus.md
```

### Pattern 1: Normative conventions doc plus copyable skeleton
**What:** Keep one document for rules and one separate file for the example template.
**When to use:** Multi-Skill repos where several later phases will copy the same pattern.
**Example:**
```markdown
# Skill Conventions

## Frontmatter Contract
## Reference Loading Rules
## Interaction Modes
## Fallback Rules
```

```markdown
---
name: example-skill
triggers:
  primary_intent: ...
---
```

### Pattern 2: Capability-first interaction policy
**What:** Define what the Skill must accomplish before naming exact tools.
**When to use:** Environments where structured UI tools may or may not be available.
**Example:**
```markdown
## Ask Strategy
- Use structured selection when available.
- Fallback to 1-3 plain-text questions when it is not.
```

### Pattern 3: Overview-first references with direct-leaf allowance
**What:** Let Skills either load the stable overview or jump directly to the correct leaf file when they already know what they need.
**When to use:** Reusable reference systems that now have stable entrypoints plus leaf modules.
**Example:**
```yaml
references:
  required:
    - references/expression-patterns.md
  optional_leaf_hints:
    - references/expression-patterns/results-and-discussion.md
```

### Anti-Patterns to Avoid
- **Thin frontmatter:** `name` + `description` only is too weak for later Skill consistency.
- **Tool-name lock-in:** conventions that assume `mcp_question` or one exact UI tool will age poorly.
- **Reference duplication inside SKILL.md:** long reference tables should not migrate back out of `references/`.
- **One-size-fits-all workflow detail:** direct Skills and guided Skills should not be forced into the same verbosity level.
</architecture_patterns>

<dont_hand_roll>
## Don't Hand-Roll

Problems that look simple but have better project-wide solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Skill metadata | Per-skill ad hoc YAML | Strict frontmatter contract | Prevents drift across 8+ future Skills |
| Tool policy | One-off tool paragraphs in each Skill | Shared conventions section with fallback rules | Keeps behavior consistent when tools differ |
| Skill body shape | Custom layout per Skill | Semi-templated backbone + mode-specific variation | Makes later planning and maintenance faster |
| Reference loading | Inline prompt tables | `references/` entrypoints + optional leaf hints | Preserves the Phase 1 modular reference architecture |

**Key insight:** This phase should remove authoring ambiguity before the project starts multiplying Skills.
</dont_hand_roll>

<common_pitfalls>
## Common Pitfalls

### Pitfall 1: Conventions mirror the legacy shell too closely
**What goes wrong:** The conventions freeze current quirks instead of defining the better reusable standard.
**Why it happens:** The only existing shell feels like the obvious template.
**How to avoid:** Treat `paper-polish-workflow/SKILL.md` as migration evidence, not as the canonical end state.
**Warning signs:** Conventions repeat `mcp_question` assumptions or old step numbering rather than describing transferable rules.

### Pitfall 2: Frontmatter becomes heavy but not useful
**What goes wrong:** Many fields are added, but they do not actually help future Skill authors or later planners.
**Why it happens:** Metadata is added for completeness rather than contract value.
**How to avoid:** Keep fields limited to information that changes how the Skill is discovered, invoked, or evaluated.
**Warning signs:** Frontmatter repeats long prose that already belongs in the body.

### Pitfall 3: Tool rules ignore degradation paths
**What goes wrong:** A Skill looks valid on paper but fails when structured interaction is unavailable.
**Why it happens:** The conventions assume the best-case tool environment.
**How to avoid:** Make fallback behavior mandatory wherever interaction changes output direction.
**Warning signs:** A Skill cannot proceed unless one exact interactive tool exists.

### Pitfall 4: Example skeleton is not actually copyable
**What goes wrong:** The skeleton demonstrates ideas, but future phases still need to rewrite major sections from scratch.
**Why it happens:** The example is explanatory instead of operational.
**How to avoid:** Keep the skeleton concrete, minimal, and aligned with the required sections and frontmatter contract.
**Warning signs:** A future phase would need to extract pieces from several docs before it can start.
</common_pitfalls>

<code_examples>
## Code Examples

Verified patterns from current repo direction:

### Strict frontmatter shape
```yaml
---
name: translation-skill
description: Translate Chinese academic text into polished English for journal submission
triggers:
  primary_intent: translation
  examples: ["translate paper", "论文翻译"]
tools: [Read, Write]
references:
  required: [references/expression-patterns.md, references/journals/ceus.md]
input_modes: [file, pasted_text]
output_contract: [latex, polished_english]
---
```

### Capability-first fallback block
```markdown
## Ask Strategy
- Ask before acting when the journal, mode, or scope changes the output direction.
- If structured interaction is unavailable, ask 1-3 plain-text questions only.
```

### Semi-templated body skeleton
```markdown
## Purpose
## Trigger
## Modes
## References
## Ask Strategy
## Workflow
## Output Contract
## Edge Cases
## Fallbacks
```
</code_examples>

<validation_architecture>
## Validation Architecture

Phase 2 needs structural verification, not a runtime test framework. The output is correct when the conventions file, example skeleton, and maintainer docs all encode the same contract.

### Validation approach
- Validate the conventions doc for required rule sections.
- Validate the skeleton for strict frontmatter fields, required body headings, and line budget.
- Validate repo docs for discoverability of the new conventions artifacts.

### Recommended commands
- Quick feedback: `rg -n "Frontmatter Contract|Reference Loading Rules|Interaction Modes|Fallback Rules|Line Budget" references\skill-conventions.md references\skill-skeleton.md`
- Full feedback: `rg -n "^name:|^description:|^triggers:|^tools:|^references:|^input_modes:|^output_contract:|## Purpose|## Trigger|## Modes|## References|## Ask Strategy|## Workflow|## Output Contract|## Edge Cases|## Fallbacks|interactive|guided|direct|batch" references\skill-skeleton.md references\skill-conventions.md`
- Line-budget check: `powershell -NoLogo -NoProfile -Command "(Get-Content 'references\skill-skeleton.md' | Measure-Object -Line).Lines"`
- Doc alignment: `rg -n "skill-conventions|skill-skeleton" README.md README_CN.md CONTRIBUTING.md CONTRIBUTING_CN.md`

### What must be validated during execution
- Required frontmatter fields are explicit and stable.
- Progressive disclosure rules are documented clearly enough for future phases to follow.
- Interaction fallback rules do not depend on one exact tool name.
- The example skeleton is concrete and remains under the line-budget target.
- Public docs point maintainers to the conventions artifacts.
</validation_architecture>

<sota_updates>
## State of the Art (2024-2025)

What's changed recently for this project domain:

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Long self-contained skill prompts | Lean skills with external references and explicit mode contracts | Reinforced by Phase 1 + Phase 2 discussion | Requires stronger authoring conventions |
| Tool-specific prompts | Capability-oriented interaction contracts with fallback | Current project blocker on AskUserQuestion availability | Makes Skills resilient across environments |
| Single-file reference blobs | Stable entrypoint + leaf-module loading | Completed in Phase 1 | Skill conventions must explicitly support direct-leaf loading |

**New patterns to consider:**
- Keep example skeletons operational and copyable, not just illustrative.
- Treat maintainer docs as part of the authoring contract, not optional afterthoughts.

**Deprecated/outdated:**
- Thin frontmatter that hides how a Skill is invoked and what it returns.
- Embedding reusable reference tables back into each individual Skill.
</sota_updates>

<open_questions>
## Open Questions

1. **How strongly should the line budget be enforced for future Skills?**
   - What we know: The legacy shell is already 259 lines, and the project prefers lean Skills.
   - What's unclear: Whether the conventions doc should set a hard `<= 300` limit or a softer recommendation.
   - Recommendation: Treat `~300` as a hard target with explicit exceptions requiring justification.

2. **Should README highlight both conventions files or only the normative one?**
   - What we know: Public docs need to surface the authoring starting point for future contributors.
   - What's unclear: Whether the skeleton should be highlighted equally or framed as an implementation aid.
   - Recommendation: Mention both, but make the conventions doc the primary anchor and the skeleton the copyable companion.
</open_questions>

<sources>
## Sources

### Primary (HIGH confidence)
- `.planning/phases/02-skill-conventions/02-CONTEXT.md` - locked decisions for Phase 2
- `.planning/ROADMAP.md` - phase goal and FNDN-04 success criteria
- `.planning/REQUIREMENTS.md` - requirement contract for FNDN-04
- `.planning/STATE.md` - current project status and blockers
- `paper-polish-workflow/SKILL.md` - legacy shell showing current anti-patterns and coupling
- `README.md`, `CONTRIBUTING.md`, `README_CN.md`, `CONTRIBUTING_CN.md` - maintainer-facing docs that must reflect the conventions output

### Secondary (MEDIUM confidence)
- `.planning/phases/01-reference-libraries/01-CONTEXT.md` - prior phase decisions on reference structure and stable paths
- `.planning/research/SUMMARY.md` - earlier project-level architecture research supporting lean Skills and shared references

### Tertiary (LOW confidence - needs validation)
- None - Phase 2 is governed by local project contracts rather than fast-moving external dependencies.
</sources>

<metadata>
## Metadata

**Research scope:**
- Core technology: Markdown conventions for Claude Code Skills
- Ecosystem: frontmatter, references, modes, fallbacks, maintainer docs
- Patterns: strict frontmatter, semi-templated body, capability-first tool policy, progressive disclosure
- Pitfalls: legacy-shell coupling, frontmatter drift, tool-name fragility, non-copyable skeletons

**Confidence breakdown:**
- Standard stack: HIGH - markdown-only repo and existing reference architecture are already established
- Architecture: HIGH - directly constrained by Phase 2 context and roadmap success criteria
- Pitfalls: HIGH - visible from the current legacy shell and recorded blockers
- Code examples: HIGH - derived from repo-local patterns and planned contract shape

**Research date:** 2026-03-11
**Valid until:** 2026-04-10
</metadata>

---

*Phase: 02-skill-conventions*
*Research completed: 2026-03-11*
*Ready for planning: yes*
