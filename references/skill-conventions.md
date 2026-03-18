# Skill Conventions

Canonical authoring rules for all Claude Code Skills in this project.

Every new Skill must follow this document. The companion example skeleton at [`references/skill-skeleton.md`](skill-skeleton.md) provides a copyable starting point that encodes these rules.

---

## Frontmatter Contract

Every `SKILL.md` must begin with a YAML frontmatter block containing these fields:

### Required Fields

| Field | Type | Purpose | Example |
|-------|------|---------|---------|
| `name` | string | Unique Skill identifier. Must match parent directory name. Max 64 chars, pattern `^[a-z0-9]+(-[a-z0-9]+)*$` | `translation-skill` |
| `description` | string | Brief description including trigger keywords. Max 1024 chars | `Translate Chinese academic text into polished English for journal submission` |
| `triggers` | object | Discovery metadata with `primary_intent` (string) and `examples` (list of phrases) | See skeleton |
| `tools` | list | Tools the Skill is allowed to depend on by default. Use capability names, not vendor-specific identifiers | `[Read, Write]` |
| `references` | object | `required` (list of stable entrypoint paths) and optional `leaf_hints` (list of narrower modules) | See skeleton |
| `input_modes` | list | Accepted input shapes | `[file, pasted_text]` |
| `output_contract` | list | Output types the Skill produces | `[polished_english, latex]` |

### Rules

- Keep frontmatter concise. Operational detail belongs in the body sections, not in repeated prose fields.
- `triggers.examples` should include both English and Chinese phrases when the Skill supports bilingual invocation.
- `tools` lists capability categories (Read, Write, Structured Interaction, PDF Analysis) rather than hard-coding one vendor tool name. If a Skill needs structured user interaction, list `Structured Interaction` and define the fallback in the body.
- `references.required` lists stable entrypoint files. `references.leaf_hints` lists narrower modules the Skill is likely to need, but the Skill may still load other leaf files at runtime.

---

## Body Structure

The Skill body uses a semi-templated backbone. All sections below are required unless marked optional.

| Section | Required | Purpose |
|---------|----------|---------|
| `## Purpose` | Yes | One-paragraph summary of what the Skill does and who it serves |
| `## Trigger` | Yes | When and how the Skill activates, including example invocations |
| `## Modes` | Yes | Supported interaction modes and how behavior varies across them |
| `## References` | Yes | Which reference files the Skill loads and under what conditions |
| `## Ask Strategy` | Yes | What to ask the user before acting and how to handle missing information |
| `## Workflow` | Yes | Step-by-step operational flow; depth varies by Skill type |
| `## Output Contract` | Yes | Exact shape of what the Skill produces |
| `## Edge Cases` | Yes | Known tricky inputs and how to handle them |
| `## Fallbacks` | Yes | Degradation behavior when tools or references are unavailable |
| `## Examples` | Recommended | At least one minimal invocation-and-output example |

### Section Depth

- **Direct Skills** (e.g., caption generation): Keep Workflow short. A numbered list of 3-5 steps is enough.
- **Guided Skills** (e.g., paper polishing): Workflow may use sub-headings for each phase, but must still respect the line budget.

### Bilingual Output Eligibility

Skills are classified as bilingual-eligible based on their `output_contract` type. Skills that produce academic text support bilingual paragraph-by-paragraph comparison; pure analysis, recommendation, and citation Skills are exempt.

| Eligibility | Skills | Reason |
|-------------|--------|--------|
| Eligible (default ON) | translation, polish, de-ai, reviewer-simulation, abstract, experiment, logic | Produces academic text where Chinese comparison aids the user |
| Exempt | caption, cover-letter, visualization, literature | English-only captions, journal-specific format, recommendation-only, BibTeX output |

Eligible Skills support bilingual paragraph-by-paragraph comparison output. Bilingual mode is ON by default; users may opt out. The bilingual format specification is defined in `references/bilingual-output.md` (created in Phase 13).

---

## Reference Loading Rules

Skills must follow the project's stable-entrypoint-plus-leaf-module architecture.

### Loading Strategy

1. If the Skill knows exactly which leaf module it needs, it may load that leaf file directly.
2. If the Skill is unsure, it must start from the stable overview entrypoint and select the appropriate leaf from there.
3. Never duplicate reference content into `SKILL.md`. Load it at runtime.

### Declaring References

- List all stable entrypoints in frontmatter `references.required`.
- List likely leaf files in `references.leaf_hints` so maintainers know which narrow modules the Skill expects to use.
- If a loaded reference does not match the current task, stop and ask the user for clarification rather than silently guessing a fallback.
- **`required: []` is acceptable** only for self-contained Skills that produce no written academic text and load no local reference files by design. Qualifying cases: pure analysis Skills (e.g., `logic-skill` — logic checking), pure recommendation Skills (e.g., `visualization-skill` — chart type suggestions), and external-MCP-only Skills (e.g., `literature-skill` — literature search). The `## References` body section must document why no reference loading is needed.

### Reference Paths

- Expression patterns: `references/expression-patterns.md` (overview), `references/expression-patterns/*.md` (leaves)
- Anti-AI patterns: `references/anti-ai-patterns.md` (overview), `references/anti-ai-patterns/*.md` (leaves)
- Journal contracts: `references/journals/[journal].md`

---

## Interaction Modes

Every Skill must declare which modes it supports and how behavior changes across them.

### Mode Taxonomy

| Mode | Behavior | When to Use |
|------|----------|-------------|
| `interactive` | Full step-by-step flow with user confirmation at each decision point | Default for guided Skills when the user wants maximum control |
| `guided` | Multi-pass flow with user confirmation at key checkpoints, not every micro-step | When the user wants structure but not per-sentence confirmation |
| `direct` | Single-pass output with minimal interaction | Quick-fix requests or batch processing |
| `batch` | Process multiple inputs with the same settings, minimal per-item interaction | When applying the same operation across sections or files |

### Mode Selection

- The default mode should be stated in the `## Modes` section.
- If the user does not specify a mode, use the default.
- Mode can be inferred from trigger phrasing (e.g., "quickly polish" suggests `direct`; "help me revise step by step" suggests `interactive`).

---

## Fallback Rules

Every Skill must define graceful degradation for at least these scenarios.

### Structured Interaction Unavailable

If the runtime environment does not provide a structured interaction tool (e.g., no `AskUserQuestion`, no `mcp_question`):

1. Fall back to 1-3 plain-text questions covering only the highest-impact gaps.
2. Do not block the entire workflow. Proceed with sensible defaults and explain what was assumed.
3. Document the fallback in the `## Fallbacks` section.

### Reference File Missing

If a declared reference file cannot be loaded:

1. Log which file was expected and could not be found.
2. If the Skill can still produce useful output without the reference, proceed with reduced capability and warn the user.
3. If the reference is critical to correctness, stop and tell the user what is missing.

### Journal Not Specified

If the Skill supports journal-aware behavior but the user does not specify a journal:

1. Ask once which journal the paper targets.
2. If the user declines or does not know, proceed with general academic style.
3. Do not guess a journal.

---

## Line Budget

`SKILL.md` files must stay lean. Target approximately 300 lines as a hard budget.

### Why

- Large Skills consume too much context before the real task begins.
- Reference content belongs in `references/`, not inline.
- Shorter Skills are easier to maintain, review, and evolve.

### Rules

- Target: 300 lines or fewer.
- If a Skill exceeds 300 lines, the author must justify why the extra length is necessary and cannot be moved to a reference file.
- Frontmatter counts toward the budget.
- Comments and blank lines count toward the budget.

### Strategies to Stay Under Budget

- Move reusable tables, pattern lists, and examples to `references/`.
- Keep Workflow steps concise; use sub-headings only when the Skill is genuinely multi-phase.
- Avoid duplicating information that already exists in a reference file.

---

## Ask Strategy Conventions

The default interaction posture for all Skills is **ask first, then act**.

### What to Ask

- Any input that materially changes the output direction (target journal, section scope, interaction mode).
- Do not ask about things the Skill can safely infer or default.

### How to Ask

- When structured interaction is available, use it for multi-option questions.
- When it is not available, ask 1-3 plain-text questions covering only the highest-impact gaps.
- Never ask more than 3 questions before producing initial output.

### When to Skip Asking

- In `direct` and `batch` modes, reduce or eliminate pre-questions when the user has provided enough context in the trigger.
- If all required information is already present in the input, proceed without asking.

### AskUserQuestion Enforcement

Each Skill's `## Ask Strategy` section declares which questions use `AskUserQuestion` and which use plain text. There is no blanket mandate — authors decide per-Skill based on interaction needs.

**Rules:**

- When `AskUserQuestion` is available and the user must choose between 2-4 discrete options, use it.
- In `direct` mode, Skills skip all `AskUserQuestion` calls entirely. The user chose direct = they want speed. Proceed with defaults and inferred context.
- In `batch` mode, `AskUserQuestion` calls are also skipped; settings from the first item apply to all subsequent items.
- When `AskUserQuestion` is unavailable, fall back per the existing rule in `## Fallback Rules > Structured Interaction Unavailable`.

**Good — structured question for multi-option selection:**

```
AskUserQuestion({
  question: "Which section should I polish first?",
  options: [
    { label: "Abstract", description: "Full abstract rewrite with word count check" },
    { label: "Introduction", description: "Logic flow and contribution statement" },
    { label: "Methods", description: "Technical accuracy and reproducibility" }
  ]
})
```

**Bad — plain dialogue for multi-option questions:**

```
"Which section should I polish first?
1. Abstract
2. Introduction
3. Methods
Please type the number."
```

Do not present options as numbered plain text when `AskUserQuestion` is available. The structured tool provides a timed multiple-choice interface that is faster and less error-prone.

---

## Workflow Memory

All Skills must participate in the project-wide workflow memory system. This system records Skill invocations and detects frequent sequences to offer workflow recommendations.

### Recording

- After the Skill's first-step validation completes (i.e., when the Skill begins producing output, not on invocation), append one entry to `.planning/workflow-memory.json`.
- Entry format: `{"skill": "<skill-name>", "ts": "<ISO timestamp>"}`
- Maximum 50 entries retained. If log length >= 50, drop the oldest entry before appending.
- If the file does not exist, create it as `[]` before appending.
- If the Skill refuses or exits before completing first-step validation, do NOT write a record.

### Pattern Detection (Step 0)

- At the very start of the Workflow, before any existing Step 1 logic, read `.planning/workflow-memory.json`.
- Check if the last 1-2 entries form a consecutive pattern with the current Skill that appears >= threshold times (configured in `.planning/config.json` under `workflow_memory.threshold`, default: 5).
- Pattern types: 2-item (A->B) or 3-item (A->B->C) consecutive chains. Check 3-item patterns first (more specific wins).
- Count occurrences by scanning the entire log left-to-right: for each position i where log[i..i+N-1] matches the pattern, count +1. Occurrences may share no entries but positions can be non-overlapping.
- If a matching pattern is found, present a bilingual recommendation via AskUserQuestion:
  - Question: "检测到常用流程：[pattern]（已出现 N 次）。是否直接以 direct 模式运行 [current skill]？"
  - Options: "Yes, proceed" (activates direct mode) / "No, continue normally"
- If the user accepts: skip all Ask Strategy questions and run in `direct` mode.
- If the user declines or AskUserQuestion is unavailable: proceed in normal mode.
- Pattern detection must not block the workflow. If the history file is missing, empty, or unparseable, skip silently to Step 1.

---

## Naming and Discovery

- Skill directory name must match the `name` field in frontmatter.
- `description` must contain keywords that help discovery (both English and Chinese when applicable).
- `triggers.examples` should cover common invocation phrases so the Skill is findable via natural language.

---

## Maintenance Rules

- When updating a Skill, preserve the frontmatter contract. Do not remove required fields.
- When adding a new reference dependency, add it to `references.required` or `references.leaf_hints`.
- When conventions change, update this document first, then propagate to existing Skills.
- Keep the example skeleton in sync with this document.

---

*Conventions: references/skill-conventions.md*
*Companion skeleton: [references/skill-skeleton.md](skill-skeleton.md)*
