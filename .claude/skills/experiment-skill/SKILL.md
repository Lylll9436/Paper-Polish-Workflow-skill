---
name: experiment-skill
description: >-
  Analyze experiment results and generate discussion paragraphs for academic papers.
  Two-phase workflow: identify measurable findings (Phase 1), confirm with user,
  then generate grounded discussion paragraphs (Phase 2).
  Accepts tables, statistics, or result descriptions. 实验分析与讨论段落生成。
triggers:
  primary_intent: analyze experiment results and generate discussion paragraphs
  examples:
    - "Analyze my experiment results"
    - "帮我分析实验结果"
    - "Generate discussion for my results table"
    - "把我的实验数据写成讨论段"
    - "Write discussion paragraphs for my findings"
    - "帮我写实验讨论部分"
    - "Identify patterns in my experiment data"
tools:
  - Read
  - Write
  - Structured Interaction
references:
  required:
    - references/expression-patterns.md
  leaf_hints:
    - references/expression-patterns/results-and-discussion.md
    - references/expression-patterns/conclusions-and-claims.md
    - references/expression-patterns/methods-and-data.md
    - references/anti-ai-patterns/vocabulary.md
input_modes:
  - file
  - pasted_text
  - structured_data
output_contract:
  - pattern_analysis
  - discussion_paragraphs
---

## Purpose

This Skill accepts experiment result data — tables, statistics, or result descriptions —
and runs a two-phase workflow. Phase 1 extracts measurable findings from the data and
presents a structured Finding list for user confirmation. Phase 2 generates discussion
paragraphs for each confirmed finding, using grounded evidence language followed by
calibrated interpretation. Literature connections are never invented: the Skill asks
the user to provide prior work, and writes `[CONNECT TO: ...]` placeholders when none
is supplied. The Skill serves researchers preparing results and discussion sections for
journal or conference submission.

## Trigger

**Activates when the user asks to:**
- Analyze experiment results, identify patterns, or extract findings from result data
- Generate discussion paragraphs from confirmed findings
- 分析实验结果、识别规律、生成讨论段落

**Example invocations:**
- "Analyze my results table and write discussion"
- "帮我分析实验结果并写讨论段"
- "Generate discussion paragraphs for my findings"
- "What patterns do my experiment results show?"

## Modes

| Mode | Default | Behavior |
|------|---------|----------|
| `direct` | Yes | Full two-phase workflow: Phase 1 finding list → user confirm → Phase 2 discussion |
| `batch` | | Not supported — experiment analysis requires full context of the complete results set |

**Default mode:** `direct`. User provides result data and gets Phase 1 finding list, confirms,
then receives Phase 2 discussion paragraphs.

**Mode inference:** "Just identify findings" or "只分析不写讨论" runs Phase 1 only.

## References

### Required (always loaded)

| File | Purpose |
|------|---------|
| `references/expression-patterns.md` | Expression patterns overview; loaded at Phase 1 start |

### Leaf Hints (loaded in Phase 2)

| File | When to Load |
|------|--------------|
| `references/expression-patterns/results-and-discussion.md` | Always in Phase 2 — result reporting and pattern interpretation language |
| `references/expression-patterns/conclusions-and-claims.md` | Always in Phase 2 — calibrated claim language (suggests, indicates, scope) |
| `references/expression-patterns/methods-and-data.md` | In Phase 2 if user's result description includes method details needing clarification |
| `references/anti-ai-patterns/vocabulary.md` | In Phase 2 — screen generated output for AI-sounding vocabulary |

### Conditional

| File | When to Load |
|------|--------------|
| `references/journals/[journal].md` | When user specifies a target journal. If missing, **refuse**: "Journal template for [X] not found. Available: CEUS." |

## Ask Strategy

**Before starting, ask about:**
1. Research questions: "What are the main research questions this experiment addresses?"
   (Required — Phase 2 uses these to connect findings to purpose)
2. Prior work to connect to: "Which papers or findings should the discussion reference?"
   (Optional — ask once; if declined, use `[CONNECT TO: ...]` placeholders in Phase 2)
3. Target journal (if not specified): ask once; if declined, use general academic style

**Rules:**
- Never ask more than 3 questions before starting Phase 1
- Research questions are mandatory; the Skill cannot produce grounded Phase 2 output without them
- If the user declines to provide research questions, write `[RESEARCH QUESTION: describe your RQ here]`
  placeholders rather than blocking the workflow entirely

## Workflow

### Phase 1: Analyze Results

**Step 1 — Prepare:**
- Load `references/expression-patterns.md` overview
- If a journal was specified, load its template; if template is missing, refuse with message above
- Read input: file via Read tool, pasted results block (table, statistics, narrative), or structured_data
- **Guard — measurable data required:** if input is vague (e.g., "my results show improvement"
  without values, comparisons, or metrics), refuse: "Please provide specific values, comparisons,
  or metrics before I can identify findings."
- LaTeX table input: read data values and captions; ignore typesetting commands

**Step 2 — Extract Findings:**
- Identify measurable comparisons: method A vs. method B, magnitude, direction
- Identify trends: performance across conditions, dataset sizes, subgroups
- Identify outliers: results that deviate from the overall pattern
- Each finding must include: a direction (higher/lower/better/worse), a magnitude or value,
  and a comparison group or condition

**Step 3 — Present Finding List:**
- Use locked format per item:
  ```
  Finding 1: [subject] [comparison/trend] [value] on [metric/condition]
  Finding 2: Performance degrades in [condition] ([N] vs. [M])
  Finding 3: [Subgroup] shows the largest effect ([value])
  ```
- Summary line: "Identified N findings. Please confirm, correct, or add before I write discussion."
- Wait for user approval before proceeding to Phase 2

---

### Phase 2: Generate Discussion

**Step 1 — Prepare:**
- Load `references/expression-patterns/results-and-discussion.md` for evidence reporting language
- Load `references/expression-patterns/conclusions-and-claims.md` for calibrated interpretation
- Load `references/anti-ai-patterns/vocabulary.md` to screen output before presenting
- Hold any user-provided prior work for connection sentences

**Step 2 — Write Discussion Paragraphs:**
- One paragraph per confirmed finding
- Each paragraph follows this structure:
  1. **Evidence sentence:** state the finding with full quantification
     (use results-and-discussion.md patterns for comparative and trend language)
  2. **Interpretation sentence:** claim using calibrated language from conclusions-and-claims.md
     ("suggests", "indicates" — never lead with interpretation before evidence)
  3. **Connection sentence:** if user provided prior work, connect the finding to it;
     otherwise write `[CONNECT TO: describe the prior finding here]`
- **CRITICAL rule:** Interpretation sentence must follow the evidence sentence. Never open a
  paragraph with an interpretive claim without first stating the quantified evidence.
- After generating all paragraphs, check output against vocabulary.md; revise any flagged patterns

**Step 3 — Output:**
- Present all discussion paragraphs in sequence
- If file input was used, offer to append discussion to file using Write tool
- Recommend Polish Skill for further expression refinement if higher-register prose is desired

## Output Contract

| Output | Format | Condition |
|--------|--------|-----------|
| `pattern_analysis` | Structured Finding list (Finding N: format) | Always — Phase 1 |
| `discussion_paragraphs` | One paragraph per confirmed finding | Phase 2 only, after Phase 1 confirmation |

**Note:** Phase 2 output cannot be produced without Phase 1 confirmation. If user skips Phase 1
and requests discussion directly, require Phase 1 completion first.

## Edge Cases

| Situation | Handling |
|-----------|----------|
| Input is vague (no measurable values) | Refuse Phase 1 with: "Please provide specific values, comparisons, or metrics before I can identify findings." |
| User skips Phase 1 and asks for discussion | Require Phase 1 completion first; do not generate paragraphs without confirmed findings |
| User provides no research questions | Ask once; if declined, write `[RESEARCH QUESTION: describe your RQ here]` placeholders |
| User provides no prior literature | Use `[CONNECT TO: ...]` placeholders; do not attempt to name papers or authors |
| Only one finding identified | Produce a single discussion paragraph; do not pad or invent additional findings |
| Finding conflicts with user-stated hypothesis | Flag the discrepancy explicitly; do not suppress the conflicting result |
| Journal specified but template missing | Refuse: "Journal template for [X] not found. Available: CEUS." |
| Input is LaTeX table markup | Read data values and captions; ignore typesetting commands |
| Phase 1 produces no findings | Report "No measurable findings identified from input" and stop |

## Fallbacks

| Scenario | Fallback |
|----------|----------|
| Structured Interaction unavailable | Ask 1-3 plain-text questions: research questions, prior work, target journal |
| Expression pattern leaf missing | Proceed with general academic register; warn user of reduced quality |
| Write tool fails | Present discussion paragraphs in conversation; user saves manually |
| Phase 1 produces no findings | Report clearly and stop; do not proceed to Phase 2 |

## Examples

**Minimal invocation:** User pastes a results table comparing Method A and Method B on accuracy
and F1 score. User states RQ: "Does our approach outperform the baseline on both metrics?"

**Phase 1 output:**
```
Finding 1: Method A outperforms Method B by 3.2 percentage points on accuracy (87.4% vs. 84.2%)
Finding 2: Method A outperforms Method B by 4.1 points on F1 score (82.6 vs. 78.5)

Identified 2 findings. Please confirm, correct, or add before I write discussion.
```

**User confirms.** No prior work provided.

**Phase 2 output (Finding 1):**
```
Method A achieves 87.4% accuracy, outperforming Method B by 3.2 percentage points (84.2%).
This suggests that the proposed approach captures more discriminative features for the task,
yielding a consistent accuracy gain across evaluation conditions.
[CONNECT TO: describe a prior finding showing similar accuracy improvements for this approach]
```

---

*Skill: experiment-skill*
*Conventions: references/skill-conventions.md*
