# Contributing

[English](CONTRIBUTING.md) | [中文](CONTRIBUTING_CN.md)

Thank you for your interest in contributing to Paper Polish Workflow.

## How to Contribute

### Creating a New Skill

New Skills must follow the project conventions:

1. Copy [`references/skill-skeleton.md`](references/skill-skeleton.md) into a new directory as `SKILL.md`
2. Follow the rules in [`references/skill-conventions.md`](references/skill-conventions.md)
3. Fill in all required frontmatter fields (`name`, `description`, `triggers`, `tools`, `references`, `input_modes`, `output_contract`)
4. Fill in all required body sections (`Purpose`, `Trigger`, `Modes`, `References`, `Ask Strategy`, `Workflow`, `Output Contract`, `Edge Cases`, `Fallbacks`)
5. Stay within the ~300 line budget; move reusable content to `references/`
6. Define fallback behavior for when structured interaction tools are unavailable

### Adding Journal Contracts

1. Create a new file in `references/journals/`
2. Name it `[journal-name].md` (lowercase, hyphens for spaces)
3. Keep the stable heading contract:
   - `## Submission Requirements`
   - `## Writing Preferences`
   - `## Quality Checks`
   - `## Section Guidance`
4. Keep `references/journals/[journal].md` directly loadable by downstream Skills

### Improving Expression References

1. Keep `references/expression-patterns.md` as the stable overview entrypoint
2. Add or refine leaf modules in `references/expression-patterns/`
3. Organize content by writing scenario, not by generic grammar buckets
4. Each leaf module should keep:
   - `## Recommended Expressions`
   - `## Avoid Expressions`
   - `## Usage Scenarios`
   - `## Bilingual Example Patterns`

### Improving Anti-AI References

1. Keep `references/anti-ai-patterns.md` as the stable overview entrypoint
2. Add or refine leaf modules in `references/anti-ai-patterns/`
3. Group patterns by category, not by paper section
4. Each leaf module should keep:
   - `## High Risk`
   - `## Medium Risk`
   - `## Optional`
5. Prefer lightweight `Problem expression -> Replacement` rows so future Skills can retrieve them directly

### Modifying the Skill

1. Edit `paper-polish-workflow/SKILL.md`
2. Preserve YAML frontmatter:
   ```yaml
   ---
   name: paper-polish-workflow
   description: ...
   ---
   ```
3. Keep long reference content out of `SKILL.md`; put it in `references/`
4. Maintain workflow structure unless the redesign explicitly changes it

## SKILL.md Requirements

All Skills must follow the [Skill conventions](references/skill-conventions.md). Use the [skill-skeleton.md](references/skill-skeleton.md) as your starting point.

### Frontmatter (Required)

Every `SKILL.md` must have YAML frontmatter with these required fields:

```yaml
---
name: skill-name
description: Brief description with trigger keywords
triggers:
  primary_intent: what the skill does
  examples: ["English phrase", "Chinese phrase"]
tools: [Read, Write]
references:
  required: [references/expression-patterns.md]
input_modes: [file, pasted_text]
output_contract: [polished_english]
---
```

**Rules:**
- `name`: max 64 chars, pattern `^[a-z0-9]+(-[a-z0-9]+)*$`
- `description`: max 1024 chars, include trigger keywords
- `name` must match the parent directory name
- `tools` lists capability categories, not vendor-specific tool names
- See [skill-conventions.md](references/skill-conventions.md) for the full field reference

## Pull Request Process

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with Claude/OpenCode
5. Submit a pull request

## Questions?

Open an issue or start a discussion.
