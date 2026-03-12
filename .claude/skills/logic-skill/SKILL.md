---
name: logic-skill
description: >-
  Verify logical consistency across paper sections.
  Traces argument chains and identifies gaps, unsupported claims,
  terminology inconsistencies, and number contradictions.
  论文逻辑验证，识别论证链断裂、无支撑声明、术语不一致、数字矛盾。
triggers:
  primary_intent: verify logical consistency of academic paper
  examples:
    - "Check the logic of my paper"
    - "检查我的论文逻辑"
    - "Verify argument chain across sections"
    - "验证论文各章节论证是否自洽"
    - "Find logical inconsistencies in my draft"
    - "帮我找论文里的逻辑问题"
tools:
  - Read
  - Write
  - Structured Interaction
references:
  required: []
  leaf_hints: []
input_modes:
  - file
  - pasted_text
output_contract:
  - logic_report
---

## Purpose

This Skill reads a full academic paper and verifies logical consistency across its sections. It identifies four types of issues — argument chain gaps (AC-), unsupported claims (UC-), terminology inconsistencies (TI-), and number contradictions (NC-) — and produces a two-part bilingual report. Part 1 maps each section's primary claim as an Argument Chain View table, showing how claims connect (or fail to connect) from Introduction through Conclusion. Part 2 categorizes every identified issue with a problem description, an impact statement, and a one-sentence directional suggestion, each followed by an inline Chinese translation. The Skill never rewrites text; it identifies and suggests only.

## Trigger

**Activates when the user asks to:**
- Check, verify, or find logical problems in their paper
- 检查论文逻辑、验证论证链、帮我找逻辑问题

**Example invocations:**
- "Check the logic of my paper" / "检查我的论文逻辑"
- "Verify argument chain across sections" / "验证论文各章节论证是否自洽"
- "Find logical inconsistencies in my draft" / "帮我找论文里的逻辑问题"

## Modes

| Mode | Default | Behavior |
|------|---------|----------|
| `direct` | Yes | Single-pass full-paper analysis, no intermediate confirmation steps |
| `batch` | | Not supported — cross-section logic verification requires full-paper context |

**Default mode:** `direct`. User provides the paper and receives a complete two-part logic report.

## References

### Required (always loaded)

None. Logic verification is a pure analysis task — no expression pattern leaves are needed, and anti-AI patterns are not loaded (consistent with Reviewer Simulation Skill convention). Logic verification is also journal-agnostic; no journal templates are loaded.

### Leaf Hints

None.

## Ask Strategy

**Before starting, ask about:**
1. Input format: file path or pasted text? (skip if obvious from how the user invoked the Skill)
2. Section structure: ask only if section boundaries are unclear from the input (e.g., unnumbered prose without headings)

**Rules:**
- In `direct` mode with sufficient input, proceed without pre-questions.
- Do not ask more than 1 question before starting analysis.
- Use Structured Interaction when available; fall back to a single plain-text question otherwise.

## Workflow

### Step 1 — Input Guard

- Accept input as a file path (Read tool) or pasted text from conversation.
- **Full-paper guard:** If input appears to be partial — single section heading only, fewer than ~500 words, or missing Methods/Results/Discussion markers — refuse with:
  > "Cross-section verification requires the full paper. Please provide the complete manuscript."
- Parse section structure: identify section boundaries and titles exactly as they appear in the paper.

### Step 2 — Analyze Four Issue Types

**Run all four issue-type checks before building the Argument Chain View table.** The table Status column is derived from the issues found in this step.

#### Argument Chain Gaps (AC-)

- Map each section's primary research claim or contribution.
- Check: Does the Introduction claim get supported by the Methods design?
- Check: Do the Results address the measurements stated in Methods?
- Check: Does the Discussion draw conclusions that follow from the Results?
- Check: Does the Conclusion match the findings stated in the Discussion?
- For each chain break, create Issue AC-N with the relevant section references.

#### Unsupported Claims (UC-)

- Scan for assertive statements that lack evidence, a citation, or a data reference.
- Focus on bold claims in Introduction and Discussion sections.
- For each unsupported claim, create Issue UC-N quoting the exact claim text.

#### Terminology Inconsistencies (TI-)

- Track all technical terms and model/method names introduced in the Introduction.
- Compare usage across all sections for variation: explicit renames, abbreviations introduced mid-paper, mixed use of formal and informal names.
- For each inconsistency, create Issue TI-N quoting ALL variant terms verbatim with their section locations.
- If an abbreviation is defined on first use in the Introduction, do not flag it as an inconsistency.

#### Number Contradictions (NC-)

- Extract quantitative values stated in the Introduction (sample size, accuracy claims, improvement figures).
- Compare against values stated in Results and Conclusion.
- For each discrepancy, create Issue NC-N quoting both values and their locations.
- Ignore numbers in non-quantitative contexts (figure labels, section numbers, footnote markers).

### Step 3 — Build Part 1 Argument Chain View

- After AC- analysis is complete, build the table using analysis evidence.
- Status = "Connected" only when no AC- issue spans that section pair.
- Status = "Gap" when one or more AC- issues involve that section transition.
- Use section titles exactly as they appear in the paper.

### Step 4 — Write Report

- Assemble the two-part report (see Output Contract for locked format).
- Report presents Part 1 table first (readability), then Part 2 categorized issues.
- **File input:** Derive output filename as `{input_filename_without_ext}_logic.md`; write using Write tool.
- **Pasted text input:** Present report in conversation.
- State issue count summary at the end: "Found N issues: X AC-, Y UC-, Z TI-, W NC-."

## Output Contract

| Output | Format | Condition |
|--------|--------|-----------|
| `logic_report` | Markdown file `{input_filename_without_ext}_logic.md` | File input |
| `logic_report` | Conversation output | Pasted text input |

**Locked two-part report format:**

```markdown
# Logic Verification Report

**Paper:** [title or filename]
**Date:** [date]

## Part 1 — Argument Chain View

| Section | Primary Claim | Connects To | Status |
|---------|--------------|-------------|--------|
| Introduction | [claim] | Methods | Connected / Gap |
| Methods | [claim] | Results | Connected / Gap |
| Results | [claim] | Discussion | Connected / Gap |
| Discussion | [claim] | Conclusion | Connected / Gap |
| Conclusion | [claim] | — | Terminal |

## Part 2 — Categorized Issue List

### Argument Chain Gaps

**Issue AC-1: [Descriptive Title]**
**Section:** [Introduction]
**Problem:** [description]
**Why this matters:** [impact on argument coherence]
**Suggestion:** [one-sentence directional suggestion]
> **[中文]** 问题：... 为什么重要：... 建议：...

### Unsupported Claims

**Issue UC-1: [Descriptive Title]**
[same three-part structure + Chinese blockquote]

### Terminology Inconsistencies

**Issue TI-1: [Descriptive Title]**
[same structure — quote exact verbatim terms from the paper]

### Number Contradictions

**Issue NC-1: [Descriptive Title]**
[same structure]
```

**Notes:**
- Part 1 Argument Chain View table is English only (structural, not language-sensitive).
- Part 2 issue entries are bilingual: English problem/why/suggestion followed immediately by `> **[中文]** ...` blockquote.
- Issue numbering is consecutive within each category (AC-1, AC-2, …; UC-1, UC-2, …).

## Edge Cases

| Situation | Handling |
|-----------|----------|
| Input is partial (single section or < 500 words) | Refuse: "Cross-section verification requires the full paper. Please provide the complete manuscript." |
| Paper has no Discussion (e.g., Results → Conclusion directly) | Adapt chain view to actual sections present; note missing sections in the table with "N/A" |
| Paper uses Chinese section titles | Use Chinese titles as-is in the table and all section references |
| No issues found in a category | Include the category heading in Part 2 with "No issues identified in this category." |
| User asks Skill to rewrite identified issues | Decline: "This Skill identifies issues and suggests directions only. Use the Polish Skill for rewriting." |
| Numbers appear in non-quantitative context (figure labels, section numbers) | Ignore; focus on empirical claims and quantitative results only |
| Abbreviation is defined on first use in Introduction | Do not flag as a TI- inconsistency; defined abbreviations are intentional |

## Fallbacks

| Scenario | Fallback |
|----------|----------|
| Structured Interaction unavailable | Proceed in direct mode without pre-questions; ask file-vs-pasted via plain text only if genuinely unclear |
| Read tool fails (file not found or unreadable) | Ask user to paste the paper text instead |
| Write tool fails | Present the logic report in conversation; advise user to save manually as `_logic.md` |
| Paper is extremely long (> 20,000 words) | Warn: "Analysis may be approximate for very long manuscripts." Offer to focus on specific section pairs if needed |

## Examples

**Minimal invocation (file input):**

User: "Check the logic of my draft: /papers/urban_model.pdf"

*Ask Strategy (direct mode — file path provided, proceeding without pre-questions)*

*Step 2 analysis runs: AC-1 found (Introduction→Methods chain break), TI-1 found (terminology variant)*
*Step 3 derives Status column from AC-1*

**Truncated output (`urban_model_logic.md`):**

```markdown
# Logic Verification Report

**Paper:** urban_model.pdf
**Date:** 2026-03-12

## Part 1 — Argument Chain View

| Section | Primary Claim | Connects To | Status |
|---------|--------------|-------------|--------|
| Introduction | Urban sprawl prediction framework proposed | Methods | Gap |
| Methods | Gradient boosting model trained on parcel data | Results | Connected |
| Results | 82% accuracy on hold-out set | Discussion | Connected |
| Discussion | Model generalizes to mid-size cities | Conclusion | Connected |
| Conclusion | Framework advances urban planning practice | — | Terminal |

## Part 2 — Categorized Issue List

### Argument Chain Gaps

**Issue AC-1: Introduction Framework Not Reflected in Methods Design**
**Section:** [Introduction], [Methods]
**Problem:** The Introduction proposes a "multi-scale prediction framework," but Methods describes a single-scale gradient boosting model with no multi-scale architecture.
**Why this matters:** Reviewers will note the mismatch between the stated contribution and the actual implementation, undermining the novelty claim.
**Suggestion:** Either update the Introduction to describe a single-scale model, or add a multi-scale component to Methods with justification.
> **[中文]** 问题：引言提出"多尺度预测框架"，但方法章节仅描述单尺度梯度提升模型，无多尺度架构。为什么重要：审稿人会注意到声明贡献与实际实现之间的不一致，削弱新颖性声明。建议：修改引言以描述单尺度模型，或在方法中补充多尺度组件并加以说明。

### Unsupported Claims

No issues identified in this category.

### Terminology Inconsistencies

**Issue TI-1: "Urban Sprawl Prediction Framework" vs. "Urban Growth Model"**
**Section:** [Introduction, 第2段], [Methods, 第1段]
**Problem:** The Introduction uses "urban sprawl prediction framework" while Methods refers to the same system as "urban growth model" without an explicit rename or definition.
**Why this matters:** Readers and reviewers may interpret these as different systems, reducing clarity.
**Suggestion:** Choose one term and use it consistently throughout, or introduce the alternative as a defined synonym on first use.
> **[中文]** 问题：引言使用"城市蔓延预测框架"，方法章节将同一系统称为"城市增长模型"，未作明确说明。为什么重要：读者和审稿人可能将其理解为不同系统，降低论文清晰度。建议：统一使用一个术语，或在首次出现时将替代名称定义为同义词。

### Number Contradictions

No issues identified in this category.
```

Found 2 issues: 1 AC-, 0 UC-, 1 TI-, 0 NC-.

---

*Skill: logic-skill*
*Conventions: references/skill-conventions.md*
