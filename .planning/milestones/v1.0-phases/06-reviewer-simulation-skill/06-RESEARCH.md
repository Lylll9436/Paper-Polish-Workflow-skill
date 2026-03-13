# Phase 6: Reviewer Simulation Skill - Research

**Researched:** 2026-03-12
**Domain:** Claude Code Skill authoring / academic peer review simulation
**Confidence:** HIGH

## Summary

Phase 6 creates a Reviewer Simulation Skill that reads a full academic paper and produces a structured peer review report. The Skill follows the established conventions from Phase 2 (frontmatter contract, body structure, ~300 line budget, reference loading) and mirrors the authoring patterns proven in the Translation (Phase 3), Polish (Phase 4), and De-AI (Phase 5) Skills. This is a standalone, read-and-report Skill -- it does not modify the paper, only produces a review report as output.

The core challenge is encoding reviewer judgment into a structured, reproducible format. Real peer reviews follow a well-documented structure: summary, major concerns, minor concerns, questions for authors, and a recommendation. The user has locked specific decisions about scoring dimensions (5 dimensions, 1-10 scale), bilingual output (English + Chinese), concern depth (problem + why + suggestion), and journal-specific adaptation. These decisions are well-supported by academic review conventions and fit comfortably within the Skill pattern.

**Primary recommendation:** Author a single SKILL.md at `.claude/skills/reviewer-simulation-skill/SKILL.md` following the de-ai-skill as the closest structural analog (two-part output: analysis then report, direct mode default, bilingual awareness), adapting the workflow to review rather than rewrite.

<user_constraints>

## User Constraints (from CONTEXT.md)

### Locked Decisions
- Two-part format: dimension scoring overview first, then detailed review body
- Scoring overview uses 1-10 numeric scale across five dimensions: Novelty, Methodology, Writing Quality, Presentation, Significance
- Each dimension score accompanied by a one-line justification
- Review body organized as: Major Concerns > Minor Concerns > Questions for Authors
- Final verdict provided: Accept / Minor Revision / Major Revision / Reject with one-sentence summary
- Each concern includes three parts: problem description, why it's a problem, and a one-sentence directional suggestion
- Concerns are located at section level (e.g., "In the Discussion section..."), not paragraph/sentence level
- Review output is bilingual: English review text with Chinese translation appended for each concern
- No full rewrite examples -- directional guidance only
- When a target journal is specified, journal template is loaded as auxiliary reference to inform review (light adaptation)
- Journal preferences mentioned in review comments where relevant, but core dimensions and weights remain the same
- Missing journal template -> refuse execution (consistent with Translation/Polish/De-AI Skills)
- Full paper input only -- partial sections (just introduction, just methods) are not accepted
- Supports both file path and pasted text as input (consistent with other Skills)
- Output is a unified review report (not per-section mini-reviews)
- Report written directly as a Markdown file (review-report.md or similar)
- For pasted text input: report presented in conversation (no file to write to)

### Claude's Discretion
- Exact weighting or emphasis across the five dimensions
- How to structure the Chinese translation alongside English text (inline vs appended block)
- Internal analysis strategy for identifying weaknesses
- Review report filename convention

### Deferred Ideas (OUT OF SCOPE)
None -- discussion stayed within phase scope.

</user_constraints>

<phase_requirements>

## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| CORE-04 | Reviewer simulation Skill reviews paper from reviewer perspective, identifies weaknesses, and provides actionable improvement suggestions | All research findings directly enable this: peer review structure conventions, scoring dimensions, concern format with actionable suggestions, journal-specific adaptation via existing template system |

</phase_requirements>

## Standard Stack

### Core

| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| Skill conventions | Phase 2 | YAML frontmatter, body structure, line budget | All project Skills follow this contract |
| Skill skeleton | Phase 2 | Copyable starting point | Proven template for 3 previous Skills |
| Expression patterns library | Phase 1 | Writing quality assessment reference | Provides the vocabulary for evaluating expression quality |
| Journal templates | Phase 1 | Journal-specific review criteria | CEUS template provides section guidance and quality checks already structured for review |

### Supporting

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| Anti-AI patterns library | Phase 1 | Detecting AI-sounding patterns in reviewed paper | Optional: when assessing writing quality dimension, detecting AI traces adds value |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| 5 fixed dimensions | Conference-style dimensions (soundness, clarity, contribution, confidence) | User locked 5 dimensions; conference dimensions vary by venue anyway |
| 1-10 scale | Accept/Reject only | 1-10 gives more granularity for improvement guidance; locked decision |
| Markdown report | LaTeX report | Markdown is simpler and more portable; locked decision |

## Architecture Patterns

### Recommended Project Structure

```
.claude/skills/reviewer-simulation-skill/
  SKILL.md            # Single Skill file (~250-280 lines target)
```

No additional files needed. The Skill loads existing references at runtime.

### Pattern 1: Read-Analyze-Report (Single-Phase)

**What:** Unlike the De-AI Skill's two-phase detect-then-rewrite pattern, the Reviewer Skill has a single analytical phase that produces a report. There is no modification phase.

**When to use:** When the Skill produces informational output rather than editing the input.

**Key workflow steps:**
1. Collect context (journal, input)
2. Read full paper
3. Analyze across all five dimensions
4. Generate structured review report
5. Write report file or present in conversation

### Pattern 2: Bilingual Concern Format

**What:** Each concern is presented in English first, then Chinese translation. Based on user decision, the Chinese translation accompanies each concern rather than being a separate block at the end.

**Recommended format (inline bilingual):**

```markdown
### Major Concern 1: Weak Baseline Comparison

**Section:** Results (Section 4)

**Problem:** The paper compares its method only against a single baseline from 2019, ignoring recent state-of-the-art methods published in 2023-2024.

**Why this matters:** Without comparison to current methods, reviewers cannot assess whether the claimed improvements are meaningful or already surpassed by existing work.

**Suggestion:** Consider adding comparisons with [Method X] and [Method Y], which represent current benchmarks in this domain.

> **[中文]** 论文仅与2019年的单一基线方法进行比较，忽略了2023-2024年发表的最新方法。缺少与当前方法的比较，审稿人无法判断所声称的改进是否具有实际意义。建议补充与[方法X]和[方法Y]的比较，这些方法代表了该领域的当前基准。
```

**Rationale:** Inline bilingual keeps problem-suggestion pairs co-located. The Chinese block quote is visually distinct but immediately follows its English counterpart, supporting the user's workflow of quick Chinese comprehension with formal English available.

### Pattern 3: Scoring Overview Table

**What:** A compact dimension scoring table at the top of the report.

**Format:**

```markdown
## Scoring Overview

| Dimension | Score (1-10) | Justification |
|-----------|:---:|---------------|
| Novelty | 6 | Incremental improvement over existing methods; core idea not entirely new |
| Methodology | 7 | Sound experimental design but lacks ablation study |
| Writing Quality | 5 | Several sections contain AI-sounding phrasing and unclear transitions |
| Presentation | 6 | Figures are clear but table formatting inconsistent |
| Significance | 7 | Results have practical implications for urban planning applications |
```

### Pattern 4: Verdict Section

**What:** Final recommendation at the end of the review body.

**Format:**

```markdown
## Verdict

**Recommendation:** Major Revision

The paper addresses an important problem with a reasonable approach, but the weak baseline comparison and missing ablation study must be resolved before the contribution can be properly evaluated.

> **[中文]** 建议：大修。论文针对重要问题提出了合理的方法，但基线比较不充分和缺少消融实验的问题必须解决，才能正确评估其贡献。
```

### Anti-Patterns to Avoid

- **Generating vague concerns:** "The methodology needs improvement" -- every concern must include why it's a problem and a directional suggestion (locked decision).
- **Paragraph-level location:** Concerns are located at section level, not paragraph/sentence level (locked decision). Do not say "In paragraph 3 of the methods section..."
- **Per-section mini-reviews:** The output is a unified review report, not separate reviews for each section (locked decision).
- **Accepting partial input:** Full paper input only. Reject requests to review just an introduction or just a methods section.
- **Over-weighting journal template:** Journal preferences inform review comments where relevant, but core dimensions and weights remain the same across all journals (locked decision).

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Expression quality assessment vocabulary | Custom quality criteria | `references/expression-patterns.md` + leaves | Already curated for academic writing quality |
| Journal-specific quality checks | Hardcoded CEUS criteria | `references/journals/ceus.md` | Journal template already has Section Guidance with common pitfalls per section |
| AI pattern detection in writing quality | Custom detection logic | `references/anti-ai-patterns.md` (optional) | Existing library covers vocabulary inflation, overclaims, transition smoothing |
| Review structure conventions | Ad-hoc format | Standard peer review format (Summary, Major, Minor, Questions) | Well-established academic convention; user locked this structure |

**Key insight:** The CEUS journal template already contains section-by-section guidance with "Common pitfalls" for each section (Abstract, Introduction, Methods, Results, Discussion, Conclusion). This is directly usable as a review checklist when the target journal is CEUS.

## Common Pitfalls

### Pitfall 1: Overly Generic Concerns

**What goes wrong:** The review produces concerns like "The introduction could be stronger" without specifying what is weak, why it matters, or what to do about it.

**Why it happens:** LLMs tend toward diplomatic but vague language. Without explicit structural constraints, reviews become commentary rather than actionable feedback.

**How to avoid:** The three-part concern format (problem + why + suggestion) is already locked. The SKILL.md workflow must enforce this structure for every concern. Include an explicit check: "Does each concern have all three parts?"

**Warning signs:** Concerns that lack a "Why this matters" or "Suggestion" component.

### Pitfall 2: Inconsistent Scoring vs. Concern Severity

**What goes wrong:** The scoring overview gives a dimension a 7/10 but the review body lists three major concerns in that dimension, or gives a 3/10 with only one minor concern.

**Why it happens:** Scores and concerns are generated in different parts of the workflow and may not be calibrated against each other.

**How to avoid:** Generate concerns first, then derive scores from the concerns. The workflow should explicitly map concerns to dimensions before assigning scores.

**Warning signs:** Scoring overview and concern list tell contradictory stories.

### Pitfall 3: Reviewing the Topic Rather Than the Paper

**What goes wrong:** The review comments on whether the research topic is important rather than evaluating how well this specific paper addresses it.

**Why it happens:** Without the full paper context, the reviewer falls back to topic-level commentary.

**How to avoid:** The Skill requires full paper input (locked decision). The analysis strategy should focus on the paper's claims, evidence, and execution rather than the topic's worthiness.

**Warning signs:** Concerns that would apply to any paper on the same topic.

### Pitfall 4: Chinese Translation Losing Technical Precision

**What goes wrong:** The Chinese translation uses overly casual or imprecise language for technical terms, losing the specificity of the English concern.

**Why it happens:** Natural Chinese academic writing uses different conventions than English; direct translation can lose nuance.

**How to avoid:** The Chinese translation should preserve domain terminology (e.g., keep "ablation study" as "消融实验" not a generic paraphrase). The SKILL.md should note this.

**Warning signs:** Chinese text that reads as general commentary when the English version is specific.

### Pitfall 5: Line Budget Pressure

**What goes wrong:** The reviewer simulation workflow is inherently complex (five dimensions, bilingual output, journal adaptation, multiple concern types) and the SKILL.md exceeds 300 lines.

**Why it happens:** Trying to encode too much review expertise directly in the Skill file.

**How to avoid:** Keep the SKILL.md focused on structure and workflow. Do not embed review criteria lists -- leverage expression patterns and journal templates loaded at runtime. The concern format template can be shown once as an example rather than repeated.

**Warning signs:** SKILL.md approaching or exceeding 280 lines during authoring.

## Code Examples

### Example 1: Frontmatter Pattern (Based on Existing Skills)

```yaml
---
name: reviewer-simulation-skill
description: >-
  Simulate peer review of academic papers with structured feedback.
  Produces bilingual review report with scoring and actionable suggestions.
  Triggers on "review", "审稿", "peer review", "模拟评审".
triggers:
  primary_intent: simulate peer review of academic paper
  examples:
    - "Review this paper"
    - "审稿这篇论文"
    - "Give me a peer review of my draft"
    - "模拟评审我的论文"
    - "Simulate a reviewer for my CEUS submission"
    - "帮我做一个模拟审稿"
tools:
  - Read
  - Write
  - Structured Interaction
references:
  required:
    - references/expression-patterns.md
  leaf_hints:
    - references/expression-patterns/introduction-and-gap.md
    - references/expression-patterns/methods-and-data.md
    - references/expression-patterns/results-and-discussion.md
    - references/expression-patterns/conclusions-and-claims.md
    - references/expression-patterns/geography-domain.md
input_modes:
  - file
  - pasted_text
output_contract:
  - review_report
---
```

### Example 2: Review Report Output Structure

```markdown
# Peer Review Report

**Paper:** [title or filename]
**Target Journal:** [journal or "General"]
**Date:** [date]

## Scoring Overview

| Dimension | Score (1-10) | Justification |
|-----------|:---:|---------------|
| Novelty | X | [one line] |
| Methodology | X | [one line] |
| Writing Quality | X | [one line] |
| Presentation | X | [one line] |
| Significance | X | [one line] |

## Major Concerns

### Major Concern 1: [Title]

**Section:** [section name]

**Problem:** [description]

**Why this matters:** [impact explanation]

**Suggestion:** [directional guidance]

> **[中文]** [Chinese translation of all three parts]

## Minor Concerns

### Minor Concern 1: [Title]

**Section:** [section name]

**Problem:** [description]

**Why this matters:** [impact explanation]

**Suggestion:** [directional guidance]

> **[中文]** [Chinese translation]

## Questions for Authors

1. [Question with context]
   > **[中文]** [Chinese translation]

## Verdict

**Recommendation:** [Accept / Minor Revision / Major Revision / Reject]

[One-sentence summary]

> **[中文]** [Chinese translation]
```

### Example 3: Journal-Specific Adaptation Pattern

When a journal is specified, the Skill loads the journal template and weaves journal-specific observations into concerns where relevant:

```markdown
### Major Concern 2: Missing Urban Systems Perspective

**Section:** Introduction (Section 1)

**Problem:** The introduction frames the work purely as a machine learning contribution without connecting to urban planning or spatial decision-making contexts.

**Why this matters:** CEUS requires a visible geography/urban-systems perspective throughout the paper. Without this framing, the paper may be considered a poor fit for the journal.

**Suggestion:** Strengthen the introduction by explaining how the proposed method addresses a specific urban systems challenge, following CEUS's preference for concrete contribution statements over generic AI framing.

> **[中文]** 引言部分将工作纯粹定位为机器学习贡献，未与城市规划或空间决策情境关联。CEUS要求论文全文保持明确的地理/城市系统视角。建议在引言中阐明所提方法如何解决特定的城市系统挑战，遵循CEUS偏好具体贡献声明而非泛泛的AI框架。
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Unstructured review text | Structured review with predefined criteria | Ongoing trend, accelerated 2023-2025 | Structured reviews produce more consistent, actionable feedback |
| Score-only reviews | Text-primary reviews (scores secondary) | ICML 2025, ICLR 2026 | Conference guidelines now state "review text matters more than the score" |
| Generic review criteria | Journal-specific structured review forms | Ongoing (Elsevier structured peer review) | Different journals weight different criteria |

**Relevant to this Skill:**
- The five locked dimensions (Novelty, Methodology, Writing Quality, Presentation, Significance) align well with the standard peer review dimensions used across major ML/AI conferences (which typically use Novelty/Soundness, Contribution/Significance, Presentation/Clarity).
- For journal-specific reviews (like CEUS), the existing journal template already provides section-by-section common pitfalls that serve as a natural review checklist.

## Open Questions

1. **Anti-AI patterns as a writing quality signal**
   - What we know: The anti-AI patterns library detects vocabulary inflation, sentence overclaims, and transition smoothing. These are directly relevant to "Writing Quality" dimension scoring.
   - What's unclear: Should the Reviewer Skill explicitly load anti-AI pattern leaves to assess writing quality, or is this overkill for a review (vs. the De-AI Skill which is purpose-built for this)?
   - Recommendation: List anti-AI patterns in `leaf_hints` as optional, not `required`. The Skill can note AI-sounding patterns as a Writing Quality concern when detected, but need not systematically scan like the De-AI Skill does. Keep it lightweight.

2. **Report filename convention**
   - What we know: User marked this as Claude's Discretion.
   - What's unclear: Whether to use a fixed name or derive from the input filename.
   - Recommendation: Use `{input_name}_review.md` for file input (e.g., `paper_review.md`). If the input file has no clear name, fall back to `review-report.md`. This follows the translation skill's `{name}_en.tex` / `{name}_bilingual.tex` naming pattern.

3. **Chinese translation structure**
   - What we know: User marked this as Claude's Discretion. Review is bilingual.
   - What's unclear: Inline per-concern (blockquote after each concern) vs. appended section at the end.
   - Recommendation: Inline blockquote after each concern (as shown in the examples above). This keeps the translation co-located with the English text, supporting quick comprehension. A single appended block would require scrolling back and forth.

## Validation Architecture

### Test Framework

| Property | Value |
|----------|-------|
| Framework | Manual validation (Skill is a markdown file, not executable code) |
| Config file | none |
| Quick run command | Invoke the Skill with a test paper and verify report structure |
| Full suite command | Test with file input, pasted text, CEUS journal, and no journal |

### Phase Requirements to Test Map

| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| CORE-04a | Skill reads full paper and produces structured reviewer feedback covering novelty, methodology, writing quality, presentation, significance | manual | Invoke Skill with sample paper, verify all 5 dimensions scored | N/A -- manual |
| CORE-04b | Each weakness comes with specific, actionable improvement suggestion | manual | Check each concern has problem + why + suggestion (3 parts) | N/A -- manual |
| CORE-04c | Review follows real journal review conventions (major, minor, questions) | manual | Verify report structure has Major Concerns, Minor Concerns, Questions for Authors sections | N/A -- manual |
| CORE-04d | Skill can target review criteria to a specific journal (e.g., CEUS) | manual | Invoke with `--journal CEUS`, verify CEUS-specific concerns appear | N/A -- manual |

### Sampling Rate

- **Per task commit:** Manual review of SKILL.md structure against conventions checklist
- **Per wave merge:** Full invocation test with sample paper
- **Phase gate:** Verify all four CORE-04 sub-behaviors with actual invocation

### Wave 0 Gaps

None -- Skill authoring phases produce markdown files only, with no test infrastructure needed. Validation is structural (conventions compliance) and behavioral (invocation testing).

## Sources

### Primary (HIGH confidence)

- **Existing project Skills** (translation-skill, polish-skill, de-ai-skill) -- direct structural analogs providing proven patterns for frontmatter, body sections, reference loading, mode selection, ask strategy, edge cases, and fallbacks
- **references/skill-conventions.md** -- authoritative Skill authoring rules
- **references/skill-skeleton.md** -- copyable starting template
- **references/journals/ceus.md** -- CEUS journal contract with section guidance and common pitfalls (directly usable as review criteria)
- **references/expression-patterns.md** -- expression quality reference library

### Secondary (MEDIUM confidence)

- [PLOS - How to Write a Peer Review](https://plos.org/resource/how-to-write-a-peer-review/) -- standard peer review structure (summary, major, minor, questions)
- [Wiley - Step by Step Guide to Reviewing a Manuscript](https://authors.wiley.com/Reviewers/journal-reviewers/how-to-perform-a-peer-review/step-by-step-guide-to-reviewing-a-manuscript.html) -- major vs minor concern distinction
- [ICLR 2026 Reviewer Guide](https://iclr.cc/Conferences/2026/ReviewerGuide) -- four key questions framework for review judgment
- [ICML 2025 Peer Review FAQ](https://icml.cc/Conferences/2025/PeerReviewFAQ) -- emphasis on review text over scores
- [Gates Open Research - How to write a constructive peer review report](https://blog.gatesopenresearch.org/2025/10/08/how-to-write-a-constructive-peer-review-report/) -- structured feedback format
- [Elsevier Reviewer Guidelines](https://www.elsevier.com/reviewer/how-to-review) -- general Elsevier reviewer framework (applicable to CEUS)

### Tertiary (LOW confidence)

- None -- all findings verified against multiple sources or project artifacts.

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH -- entirely reusing existing project infrastructure (conventions, skeleton, references)
- Architecture: HIGH -- following proven Skill authoring patterns from 3 existing Skills, adapted for review output
- Pitfalls: HIGH -- pitfalls derived from structural analysis of the locked decisions and peer review conventions

**Research date:** 2026-03-12
**Valid until:** 2026-04-12 (stable -- Skill conventions and reference libraries are project-internal and unlikely to change)
