# Paper Polish Workflow

[English](README.md) | [中文](README_CN.md)

A systematic, AI-assisted workflow for polishing academic papers. Designed for use with Claude/OpenCode AI assistants.

## What is this?

An AI skill that guides you through paper polishing **step by step**:

```
Structure → Sentence Logic → Expression
```

Instead of making arbitrary changes, the AI helps you:
1. Confirm the overall structure of each section
2. Confirm the logic of each sentence
3. Select from multiple expression options
4. Check consistency and coherence

## Features

- **Top-down approach**: Never jumps to wording before confirming logic
- **User-led**: Every step requires your confirmation
- **Option-based**: Uses interactive selection for efficient choices
- **Reference-driven**: Consults reusable reference libraries instead of duplicating long prompt tables
- **Journal-aware**: Includes a stable CEUS journal contract (extensible to other journals)
- **Modular references**: Keeps stable overview files plus narrower leaf modules for on-demand loading

## Installation

### For OpenCode Users

```bash
# Copy the skill directory to your project
cp -r paper-polish-workflow/ .opencode/skills/
```

### For Claude Code Users

```bash
# Copy the skill directory
cp -r paper-polish-workflow/ .claude/skills/
```

## Quick Start

Simply ask the AI to polish your paper:

```
User: Help me polish the abstract

AI: [Phase 0]
    - What is the target journal?
    - Where are the example papers?

User: CEUS, examples in docs/example

AI: [Step 1] Presents structure for confirmation...
AI: [Step 2] Presents per-sentence logic...
AI: [Step 3] Presents expression options...
AI: [Step 5] Checks coherence, suggests transitions...
AI: Writes polished version to file
```

## Workflow Steps

| Step | What Happens |
|------|--------------|
| **Phase 0** | AI asks about target journal, word limits, example papers |
| **Step 1** | AI presents section structure for confirmation |
| **Step 2** | AI presents per-sentence logic for confirmation |
| **Step 3** | AI presents expression options (multiple choice) |
| **Step 4** | AI consults example papers if needed |
| **Step 5** | AI checks repetition and coherence |
| **Write** | AI presents final version and writes to file |

## Authoring New Skills

All new Skills must follow the project's Skill conventions. Start here:

- [`references/skill-conventions.md`](references/skill-conventions.md): canonical authoring rules (frontmatter contract, modes, fallbacks, line budget)
- [`references/skill-skeleton.md`](references/skill-skeleton.md): copyable example skeleton to use as a starting point

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed authoring guidance.

## Shared References

The project uses stable reference entrypoints plus narrower leaf modules.

### Stable Entry Points

- [`references/expression-patterns.md`](references/expression-patterns.md): overview and module map for academic expression patterns
- [`references/anti-ai-patterns.md`](references/anti-ai-patterns.md): overview and risk model for anti-AI rewriting
- [`references/journals/ceus.md`](references/journals/ceus.md): CEUS journal contract
- [`references/skill-conventions.md`](references/skill-conventions.md): Skill authoring conventions
- [`references/skill-skeleton.md`](references/skill-skeleton.md): copyable Skill template

### Why this structure?

- Skills can keep using stable top-level paths.
- Longer workflows can load only the relevant leaf module under `references/expression-patterns/` or `references/anti-ai-patterns/`.
- Future journal templates can follow the same stable `references/journals/[journal].md` contract.
- New Skills start from the skeleton and follow the conventions, ensuring consistency across the suite.

## Supported Journals

### CEUS (Computers, Environment and Urban Systems)

Built-in support for:
- Word limits (<=8,000 total, <=250 abstract)
- Highlights generation (3-5 items, <=85 chars each)
- Writing preferences and quality checks
- Section-by-section guidance for downstream Skills

See [`references/journals/ceus.md`](references/journals/ceus.md) for the current contract.

### Adding Other Journals

Create a new file in `references/journals/` following the CEUS heading contract:
- `## Submission Requirements`
- `## Writing Preferences`
- `## Quality Checks`
- `## Section Guidance`

## Project Structure

```text
paper-polish-workflow/
├── paper-polish-workflow/
│   └── SKILL.md
├── references/
│   ├── skill-conventions.md            # Skill authoring conventions
│   ├── skill-skeleton.md               # Copyable Skill template
│   ├── expression-patterns.md          # Stable overview entrypoint
│   ├── expression-patterns/            # Scenario-based expression modules
│   ├── anti-ai-patterns.md             # Stable anti-AI entrypoint
│   ├── anti-ai-patterns/               # Risk-tiered anti-AI modules
│   └── journals/
│       └── ceus.md                     # Stable CEUS journal contract
├── examples/
│   └── abstract-polishing-session.md
├── README.md
├── README_CN.md
├── CONTRIBUTING.md
└── CONTRIBUTING_CN.md
```

## Requirements

- OpenCode or Claude Code with tool access
- Tools needed:
  - `mcp_question` - for option selection
  - `mcp_read`, `mcp_write` - for file operations
  - `mcp_look_at` - for PDF analysis (optional)

## Example Session

See [examples/abstract-polishing-session.md](examples/abstract-polishing-session.md) for a complete walkthrough.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines. Contributions are especially useful for:
- New journal contracts under `references/journals/`
- New expression modules under `references/expression-patterns/`
- New anti-AI modules under `references/anti-ai-patterns/`

## License

MIT License - See [LICENSE](LICENSE) for details.

## Acknowledgments

Developed through iterative refinement while polishing a dual-layer urban perception paper for CEUS journal submission.
