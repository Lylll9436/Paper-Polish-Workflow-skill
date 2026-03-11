---
name: de-ai-skill
description: >-
  Detect and rewrite AI-generated patterns in English academic text.
  Two-phase workflow: scan with risk tagging, then batch rewrite.
  Triggers on "de-AI", "降AI", "reduce AI traces", "AI检测".
triggers:
  primary_intent: Detect and rewrite AI patterns in academic text
  examples:
    - "De-AI this paragraph"
    - "降AI这段论文"
    - "Check for AI patterns in my paper"
    - "Reduce AI traces in my introduction"
    - "AI检测并改写"
    - "Scan this text for AI-generated phrasing"
    - "帮我降低AI检测分数"
tools:
  - Read
  - Edit
  - Structured Interaction
references:
  required:
    - references/anti-ai-patterns.md
    - references/expression-patterns.md
  leaf_hints:
    - references/anti-ai-patterns/vocabulary.md
    - references/anti-ai-patterns/sentence-patterns.md
    - references/anti-ai-patterns/transitions-and-tone.md
    - references/expression-patterns/introduction-and-gap.md
    - references/expression-patterns/methods-and-data.md
    - references/expression-patterns/results-and-discussion.md
    - references/expression-patterns/conclusions-and-claims.md
input_modes:
  - file
  - pasted_text
output_contract:
  - detection_report
  - rewritten_text
  - rewrite_report
---

## Purpose

This Skill detects AI-generated patterns in English academic text and rewrites flagged passages with explainable, risk-tagged results. It scans text against three pattern dimensions (vocabulary inflation, sentence overclaims, transition smoothing) from the anti-AI patterns library, presents detections grouped by risk level (High Risk / Medium Risk / Optional), and lets users batch-select which items to rewrite. Rewrites restructure expressions rather than just swapping synonyms, preserving academic meaning and quality. For file input, edits are made in-place with LaTeX comment annotations for traceability; for pasted text, results appear in conversation.

## Trigger

**Activates when the user asks to:**
- Detect, scan, or check for AI-generated patterns in academic text
- Rewrite or reduce AI traces in English writing
- 降AI、检测AI痕迹、降低AI检测分数

**Example invocations:**
- "De-AI this paragraph" / "降AI这段论文"
- "Check my paper for AI patterns" / "AI检测并改写"
- "Scan only -- just show detections" / "只扫描不改写"

## Modes

| Mode | Default | Behavior |
|------|---------|----------|
| `direct` | Yes | Full detect-then-rewrite two-phase workflow with batch selection |
| `batch` | | Same operation across multiple files with same settings |

**Default mode:** `direct`. User says "de-AI this" and gets detect + rewrite.

**Mode inference:** "scan only", "just check", or "只检测" triggers detect-only (skip rewrite phase). "De-AI all sections" or "batch" switches to `batch`.

## References

### Required (always loaded)

| File | Purpose |
|------|---------|
| `references/anti-ai-patterns.md` | Risk model, category map, retrieval contract |
| `references/expression-patterns.md` | Academic expression patterns for rewrite quality |

### Leaf Hints (loaded proactively for detection)

| File | When to Load |
|------|--------------|
| `references/anti-ai-patterns/vocabulary.md` | Always -- loaded proactively for full-text scan |
| `references/anti-ai-patterns/sentence-patterns.md` | Always -- loaded proactively for full-text scan |
| `references/anti-ai-patterns/transitions-and-tone.md` | Always -- loaded proactively for full-text scan |

### Leaf Hints (loaded during rewrite phase)

| File | When to Load |
|------|--------------|
| `references/expression-patterns/introduction-and-gap.md` | Rewriting introduction or background content |
| `references/expression-patterns/methods-and-data.md` | Rewriting methods or data content |
| `references/expression-patterns/results-and-discussion.md` | Rewriting results or discussion content |
| `references/expression-patterns/conclusions-and-claims.md` | Rewriting conclusion content |

### Loading Rules

- Load ALL three anti-AI pattern leaves proactively at the start for full-text scanning.
- Load expression patterns overview at the start; load section-specific leaves during rewrite phase based on detected section type.
- When a target journal is specified, also load `references/journals/[journal].md`. If template missing, **refuse** with message: "Journal template for [X] not found. Available: CEUS."
- If an anti-AI pattern leaf is missing, warn the user and proceed with reduced detection coverage.

## Ask Strategy

**Before starting, ask about:**
1. Target journal (if not specified) -- determines style consideration during rewrite
2. Scope: full paper or specific section (if ambiguous)

**Rules:**
- In `direct` mode, skip pre-questions when the user provides enough context.
- Never ask more than 2 questions before scanning.
- Use Structured Interaction when available; fall back to plain-text questions otherwise.

## Workflow

### Phase 1: Detect

**Step 1 -- Prepare:**
- Load all three anti-AI pattern leaves (vocabulary, sentence-patterns, transitions-and-tone).
- If target journal specified, load journal template; if missing, refuse.
- Read user input (file via Read tool, or pasted text from conversation).

**Step 2 -- Scan:**
- Scan full text against all three pattern dimensions in a single pass.
- For each match, check domain term protection: if the flagged term is standard domain terminology used in context (e.g., "landscape" in a geography paper, "robust" in a statistics paper), mark as SKIPPED with reason.
- For Optional-tier matches: only flag if the pattern appears 3+ times in the text. Single occurrences are suppressed to reduce noise.

**Step 3 -- Present Detection Report:**
- Summary line: "Found N AI patterns (X High Risk / Y Medium Risk / Z Optional)" plus count of skipped domain terms.
- Detailed list grouped by risk level (High first, then Medium, then Optional). Each item shows:

```
[#N] [HIGH RISK] Vocabulary Inflation
  Original: "This groundbreaking approach transforms the analytical framework"
  Pattern: "groundbreaking" -- promotional, exaggerated vocabulary (vocabulary.md)
  Suggestion: "This useful approach improves the analytical framework"
```

**Step 4 -- User Selection:**
- Ask which items to rewrite:
  - "Fix all High Risk"
  - "Fix High + Medium"
  - "Fix all"
  - Specific items by number: "fix 1, 3, 7"

### Phase 2: Rewrite

**Step 1 -- Group and Rewrite:**
- Group selected items by paragraph. When multiple items appear in the same paragraph, rewrite them together in one pass to maintain coherence.
- Load relevant expression pattern leaves based on section type for higher quality alternatives beyond the anti-AI replacement column.
- Restructure expressions -- do not just swap synonyms. Preserve academic meaning and quality.
- If target journal specified, consider journal style preferences during rewrite.

**Step 2 -- Apply Changes:**
- **File input:** Use Edit tool for in-place modifications. Add `% [De-AI] Original: <original text>` LaTeX comment on the line immediately before each rewritten passage.
  - Multi-line originals: each line gets its own `% [De-AI] Original:` prefix.
  - If existing `% [De-AI] Original:` annotations found, clean them up before adding new ones.
- **Pasted text:** Present rewritten version in conversation with before/after comparison for each changed passage.

**Step 3 -- Rewrite Report:**

| Field | Content |
|-------|---------|
| Total rewrites | Applied N of M detected items |
| By category | Vocabulary inflation: count, Sentence overclaim: count, Transition smoothing: count |
| Skipped items | Optional below threshold, domain terms protected |
| Word count delta | +/- N words |

**Step 4 -- Summary:**
- If further polishing is needed, recommend: "Consider running the Polish Skill for additional refinement."

## LaTeX Annotation Format

- Format: `% [De-AI] Original: <original text>` on the line immediately before the replacement.
- Multi-line originals: each line gets its own `% [De-AI] Original:` prefix.
- Annotations are valid LaTeX comments -- the file compiles with them present.
- Cleanup: after user confirms acceptance, remove all lines matching `^% \[De-AI\] Original:`.
- If existing `% [De-AI] Original:` annotations are found, clean them up before adding new ones.

## Output Contract

| Output | Format | Condition |
|--------|--------|-----------|
| Detection report | Structured markdown (summary + detailed list) | Always (Phase 1) |
| Rewritten text | In-place LaTeX with annotations (file) or conversation diff (pasted) | Phase 2 |
| Rewrite report | Structured markdown table | After Phase 2 |
| Word count delta | Integer | After Phase 2 |

## Edge Cases

| Situation | Handling |
|-----------|----------|
| No AI patterns detected | Report "No AI patterns detected" and exit; do not proceed to rewrite |
| Input too short (< 3 sentences) | Warn detection may be unreliable on short text; proceed if user confirms |
| All detections are domain terms (all skipped) | Report "N patterns matched but all identified as domain terminology -- no rewrites needed" |
| Existing `% [De-AI] Original:` annotations | Clean up old annotations before running new scan |
| Input language is not English | Warn and suggest running Translation Skill first |
| Journal template missing when journal specified | Refuse: "Journal template for [X] not found. Available: CEUS." |
| Very long input (10+ pages) | Process in sections; maintain cross-section awareness |

## Fallbacks

| Scenario | Fallback |
|----------|----------|
| Structured Interaction unavailable | Ask 1-2 plain-text questions (journal + scope) |
| Anti-AI pattern leaf missing | Warn user, proceed with available modules (reduced detection coverage) |
| Expression pattern leaf missing | Use overview entrypoint for general rewrite patterns |
| Target journal not specified | Ask once; if declined, use general academic style for rewrites |
| File is read-only or Edit fails | Present changes as a diff in conversation; user applies manually |

---

*Skill: de-ai-skill*
*Conventions: references/skill-conventions.md*
