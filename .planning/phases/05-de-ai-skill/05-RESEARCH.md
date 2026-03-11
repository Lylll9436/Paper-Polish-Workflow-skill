# Phase 5: De-AI Skill - Research

**Researched:** 2026-03-11
**Domain:** Claude Code Skill authoring -- AI pattern detection and rewriting in academic text
**Confidence:** HIGH

## Summary

Phase 5 builds a De-AI Skill that scans English academic text against the existing anti-AI patterns reference library (three leaf modules: vocabulary, sentence-patterns, transitions-and-tone), presents detections with three-tier risk tagging (High Risk / Medium Risk / Optional), and lets users batch-rewrite by risk level. The Skill follows the same conventions as Translation Skill and Polish Skill: YAML frontmatter, dual input modes (file + pasted text), LaTeX comment annotations for file edits, and journal-aware rewriting.

The implementation is entirely markdown-based (SKILL.md authoring), no traditional code. The anti-AI patterns library is already complete with consistent `## High Risk / ## Medium Risk / ## Optional` headings across all three leaf modules, providing a stable detection basis. The main design challenge is the two-phase workflow (detect first, then rewrite on user command) and the domain-term protection mechanism.

**Primary recommendation:** Follow Polish Skill structure closely, but split the workflow into an explicit detect phase and a separate rewrite phase, with risk-level batch selection between them.

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- Detection presentation: sentence-level highlighting with three-tier risk labels (High Risk / Medium Risk / Optional), summary first then detailed list, each item includes original snippet + risk level + pattern category + suggested replacement
- Rewriting strategy: detect first then user chooses to rewrite (not one-step), batch selection by risk level ("fix all High Risk" / "fix High + Medium" / "fix all"), in-place edit with `% [De-AI] Original:` LaTeX comments, post-rewrite report with statistics
- Input/output modes: file path and pasted text dual input (same as Translation/Polish), file input gets in-place edit + annotations, pasted text gets conversation output
- Upstream integration: standalone operation, no upstream history awareness, supports target journal specification with template-missing refusal
- Detection scope: all three dimensions (vocabulary, sentence-patterns, transitions-and-tone), full-text single-pass scan, match only known patterns from anti-AI library (no statistical analysis), domain term protection for false positives

### Claude's Discretion
- How to build domain terminology protection wordlist (hardcoded vs dynamic inference)
- Exact formatting of detection report
- How to maintain context coherence during rewriting
- Internal processing strategy for full-text scan

### Deferred Ideas (OUT OF SCOPE)
None -- discussion stayed within phase scope.
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| CORE-03 | De-AI Skill detects AI-generated patterns and rewrites text to reduce detection scores while maintaining academic quality | Full support: anti-AI patterns library provides detection basis (3 categories x 3 risk tiers); Polish Skill provides rewrite/annotation pattern; Skill conventions provide structure template |
</phase_requirements>

## Standard Stack

### Core
| Asset | Path | Purpose | Why Standard |
|-------|------|---------|--------------|
| Skill Conventions | `references/skill-conventions.md` | SKILL.md authoring rules | Project-mandated for all Skills |
| Skill Skeleton | `references/skill-skeleton.md` | Copyable template | Starting point for new Skills |
| Anti-AI Patterns (overview) | `references/anti-ai-patterns.md` | Risk model, category map, retrieval contract | Stable entrypoint for detection |
| Anti-AI Vocabulary | `references/anti-ai-patterns/vocabulary.md` | Vocabulary inflation detection | 5 High, 5 Medium, 4 Optional entries |
| Anti-AI Sentence Patterns | `references/anti-ai-patterns/sentence-patterns.md` | Sentence template detection | 4 High, 4 Medium, 3 Optional entries |
| Anti-AI Transitions | `references/anti-ai-patterns/transitions-and-tone.md` | Transition/tone detection | 3 High, 4 Medium, 3 Optional entries |

### Supporting
| Asset | Path | Purpose | When to Use |
|-------|------|---------|-------------|
| Polish Skill | `.claude/skills/polish-skill/SKILL.md` | Reference for LaTeX annotation format, summary pattern, De-AI recommendation | Pattern reuse for annotation format |
| Translation Skill | `.claude/skills/translation-skill/SKILL.md` | Reference for dual input mode implementation | Pattern reuse for file/pasted input handling |
| Journal Templates | `references/journals/[journal].md` | Journal-specific style preferences | When user specifies target journal for rewriting |
| Expression Patterns | `references/expression-patterns.md` | Academic expression alternatives | During rewrite phase for quality alternatives |

### No Alternatives Needed
This is a markdown Skill authoring project. There are no library choices -- the "stack" is the existing reference files and conventions.

## Architecture Patterns

### Skill File Structure
```
.claude/skills/de-ai-skill/
└── SKILL.md          # ~300 lines, follows skill-conventions.md
```

### Pattern 1: Two-Phase Workflow (Detect then Rewrite)
**What:** Unlike Polish Skill's single-pass approach, De-AI separates detection and rewriting into two explicit phases with user decision between them.
**When to use:** Always -- this is a locked decision.
**Structure:**
```
Phase 1: Detect
  - Load all three anti-AI leaf modules
  - Scan full text against known patterns
  - Present summary: "Found 12 AI patterns (5 High / 4 Medium / 3 Optional)"
  - Present detailed list: each item with snippet, risk, category, suggestion

[User decides]: "Fix all High Risk" / "Fix High + Medium" / "Fix all" / specific items

Phase 2: Rewrite
  - Apply rewrites for selected items
  - Use Edit tool for file input, conversation output for pasted text
  - Add % [De-AI] Original: annotations
  - Generate rewrite report
```

### Pattern 2: Risk-Level Batch Selection
**What:** User selects which risk levels to rewrite rather than confirming each item individually.
**When to use:** Between detect and rewrite phases.
**Options:**
- "Fix all High Risk" -- rewrites only High Risk items
- "Fix High + Medium" -- rewrites High and Medium Risk items
- "Fix all" -- rewrites everything including Optional
- Specific items by number -- "fix 1, 3, 7"

### Pattern 3: LaTeX Annotation Format (De-AI variant)
**What:** Same annotation pattern as Polish Skill but with `[De-AI]` prefix.
**Format:**
```latex
% [De-AI] Original: This proves that urban sprawl is groundbreaking.
The evidence suggests that urban sprawl is substantial.
```
**Rules:**
- Multi-line originals: each line gets its own `% [De-AI] Original:` prefix
- Cleanup: remove all lines matching `^% \[De-AI\] Original:` after user acceptance
- If existing `% [De-AI] Original:` annotations found, clean up before adding new ones

### Pattern 4: Detection Item Structure
**What:** Each detected item must include four fields for explainability.
**Structure per item:**
```
[#N] [HIGH RISK] Vocabulary Inflation
  Original: "This groundbreaking approach transforms urban analysis"
  Pattern: "groundbreaking" -- promotional, exaggerated vocabulary
  Suggestion: "This useful approach improves urban analysis"
```

### Pattern 5: Domain Term Protection
**What:** When a flagged vocabulary item is actually standard domain terminology, mark it as skip rather than false positive.
**Example:** "landscape" flagged by vocabulary patterns but is standard geography terminology.
**Recommendation (Claude's Discretion):** Use dynamic inference rather than hardcoded wordlist. The Skill should check whether a flagged term appears in a domain-specific context (e.g., geography paper using "landscape" literally). Instruction in SKILL.md: "Before flagging a vocabulary match, check if the term is used as domain-standard terminology in context. If so, mark as SKIPPED with reason."

### Anti-Patterns to Avoid
- **One-step detect-and-rewrite:** User must see detections and choose before any rewriting happens
- **Per-item confirmation:** Use batch risk-level selection, not "fix this one? yes/no" for each item
- **Statistical AI detection:** Only match known patterns from the anti-AI library, never attempt perplexity analysis or statistical detection
- **Synonym-only substitution:** Rewrites must restructure expressions, not just swap words
- **Duplicating anti-AI content into SKILL.md:** Load at runtime via references, never inline

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Detection patterns | Custom pattern lists in SKILL.md | `references/anti-ai-patterns/*.md` | Already organized by category and risk tier |
| Annotation format | New annotation syntax | `% [De-AI] Original:` (same pattern as Polish) | Consistency across Skills, shared cleanup regex |
| Input mode handling | Custom input logic | Same dual-mode pattern from Polish/Translation Skills | Proven pattern, user expectation consistency |
| Journal style awareness | Inline journal rules | `references/journals/[journal].md` | Centralized, maintainable, shared across Skills |
| Expression alternatives | Custom replacement lists | `references/expression-patterns/*.md` for rewrite quality | Richer alternatives than anti-AI replacement column alone |

**Key insight:** The anti-AI patterns library IS the detection engine. The Skill's job is workflow orchestration (scan, present, select, rewrite), not pattern authoring.

## Common Pitfalls

### Pitfall 1: Exceeding 300-line budget
**What goes wrong:** The two-phase workflow and detailed detection presentation rules tempt verbose SKILL.md.
**Why it happens:** Detect + Rewrite is more complex than single-pass Polish.
**How to avoid:** Keep detection item format as a brief template, not exhaustive examples. Reference the anti-AI library for pattern details rather than repeating them. Use the shared guided-mode table pattern from Polish Skill.
**Warning signs:** SKILL.md approaching 250 lines before Edge Cases section.

### Pitfall 2: Rewrites that break context coherence
**What goes wrong:** Replacing individual phrases without considering surrounding sentences creates disjointed text.
**Why it happens:** Pattern matching is local but good rewriting requires paragraph context.
**How to avoid:** Instruct the Skill to read the full surrounding paragraph before rewriting any item within it. When multiple items appear in the same paragraph, rewrite them together in one pass.
**Warning signs:** Rewritten sentences that don't grammatically connect to adjacent sentences.

### Pitfall 3: Over-detection causing alert fatigue
**What goes wrong:** Too many Optional-tier detections overwhelm the user with noise.
**Why it happens:** Optional patterns like "notably" or "significantly" are common in legitimate academic writing.
**How to avoid:** Count repetitions -- flag Optional items only when they appear 3+ times in the text (repetition is the real signal). Single occurrences of Optional items should be suppressed or grouped as a footnote.
**Warning signs:** Detection report showing 30+ items for a short paper, mostly Optional.

### Pitfall 4: Domain term false positives
**What goes wrong:** Flagging "landscape" in a geography paper or "robust" in a statistics paper as AI vocabulary.
**Why it happens:** The vocabulary list includes words that are legitimate in certain domains.
**How to avoid:** Domain term protection: check if the flagged word appears in a domain-appropriate context before including it in results. Mark as SKIPPED with reason.
**Warning signs:** User repeatedly dismissing detections for legitimate technical terms.

### Pitfall 5: Inconsistent annotation prefix
**What goes wrong:** Using `% [Polish] Original:` instead of `% [De-AI] Original:` or mixing them.
**Why it happens:** Copy-pasting from Polish Skill template.
**How to avoid:** Explicitly state the prefix as `% [De-AI] Original:` in the Workflow section. Include cleanup regex: `^% \[De-AI\] Original:`.
**Warning signs:** Mixed annotation prefixes in output files.

## Code Examples

### Detection Summary Format
```
## De-AI Scan Results

**Total:** 12 AI patterns detected (5 High Risk / 4 Medium Risk / 3 Optional)

### High Risk (5 items)

[#1] Vocabulary Inflation
  "This groundbreaking approach transforms the analytical framework"
  -> "groundbreaking" is promotional vocabulary (anti-ai-patterns/vocabulary.md)
  Suggested: "This useful approach improves the analytical framework"

[#2] Sentence Overclaim
  "This proves that urban density directly causes heat islands"
  -> "This proves that" overstates certainty (anti-ai-patterns/sentence-patterns.md)
  Suggested: "This suggests that urban density contributes to heat islands"

...

### Medium Risk (4 items)
...

### Optional (3 items)
...

---
**Action:** Which items to rewrite?
- "Fix all High Risk" / "Fix High + Medium" / "Fix all"
- Or specify by number: "fix 1, 2, 5"
```

### Rewrite Report Format
```
## De-AI Rewrite Report

**Rewrites applied:** 9 of 12 detected items (High + Medium)

| Category | Count | Examples |
|----------|-------|---------|
| Vocabulary inflation | 4 | groundbreaking -> useful, comprehensive -> broad |
| Sentence overclaim | 3 | "proves that" -> "suggests that" |
| Transition smoothing | 2 | "Moreover, it is worth noting" -> "Additionally" |

**Skipped:** 3 Optional items (below threshold)
**Domain terms protected:** 1 ("landscape" -- geography standard term)
```

### LaTeX Annotation Example
```latex
% [De-AI] Original: This groundbreaking study leverages comprehensive data to demonstrate the transformative impact.
This useful study draws on broad data to show the meaningful impact.
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Polish Skill handles anti-AI as side effect | Dedicated De-AI Skill with focused workflow | Phase 5 | Users get explainable, targeted anti-AI rewriting instead of opaque polish |
| One-pass detect-and-fix | Two-phase detect-then-rewrite with user selection | Phase 5 decision | User maintains control over which patterns to address |

**Key pattern evolution:**
- Polish Skill "recommends running De-AI Skill" in its summary (established in Phase 4) -- this is the handoff point
- De-AI Skill is the dedicated, deep tool; Polish Skill is the quick preventive layer

## Open Questions

1. **Optional item repetition threshold**
   - What we know: Optional patterns should only be flagged when overused, not on single occurrence
   - What's unclear: Exact threshold (2+? 3+? percentage of paragraphs?)
   - Recommendation: Start with 3+ occurrences as the threshold; document in SKILL.md as configurable guidance

2. **Cross-paragraph pattern repetition**
   - What we know: Full-text scan should detect repetition across paragraphs (locked decision)
   - What's unclear: How to present cross-paragraph repetition differently from single-occurrence flags
   - Recommendation: Add a "Repetition" sub-section in detection report showing patterns that appear in 3+ distinct paragraphs

3. **Expression patterns loading during rewrite**
   - What we know: Anti-AI replacement column provides basic alternatives; expression patterns provide richer options
   - What's unclear: Whether to load expression pattern leaves during rewrite for higher quality alternatives
   - Recommendation: Load expression pattern leaves based on detected section type during rewrite phase for quality alternatives beyond the anti-AI replacement column

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | Manual review (markdown Skill, no executable code) |
| Config file | None |
| Quick run command | Read SKILL.md and verify against skill-conventions.md checklist |
| Full suite command | End-to-end test: invoke De-AI Skill on sample academic text |

### Phase Requirements -> Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| CORE-03a | Skill identifies AI-generated patterns with risk tagging | manual | Invoke on sample text containing known anti-AI patterns | N/A |
| CORE-03b | Skill rewrites flagged passages using anti-AI alternatives | manual | Select "fix all High Risk" and verify rewrites | N/A |
| CORE-03c | Rewritten text maintains academic quality | manual | Read rewritten text for coherence and meaning preservation | N/A |
| CORE-03d | User can review before/after comparison | manual | Check LaTeX annotations or conversation diff output | N/A |

### Sampling Rate
- **Per task commit:** Read SKILL.md, verify frontmatter fields, section presence, line count
- **Per wave merge:** Invoke Skill on a test paragraph with known High/Medium/Optional patterns
- **Phase gate:** Full invocation test covering both input modes (file + pasted text)

### Wave 0 Gaps
- [ ] `.claude/skills/de-ai-skill/SKILL.md` -- the deliverable itself (does not exist yet)
- No test infrastructure needed -- this is a markdown Skill file, validation is manual invocation

## Sources

### Primary (HIGH confidence)
- `references/anti-ai-patterns.md` + three leaf modules -- read directly, verified structure and content
- `references/skill-conventions.md` -- read directly, verified all required sections
- `references/skill-skeleton.md` -- read directly, verified template structure
- `.claude/skills/polish-skill/SKILL.md` -- read directly, verified annotation format and workflow patterns
- `.claude/skills/translation-skill/SKILL.md` -- read directly, verified dual input mode pattern

### Secondary (MEDIUM confidence)
- Phase 4 decisions in STATE.md -- verified LaTeX annotation pattern and anti-AI proactive loading

### Tertiary (LOW confidence)
- None -- all findings based on direct file reads from the project

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH -- all reference files read and verified directly
- Architecture: HIGH -- patterns derived from existing Polish/Translation Skills with locked user decisions
- Pitfalls: HIGH -- derived from analyzing the anti-AI library content and skill conventions constraints

**Research date:** 2026-03-11
**Valid until:** 2026-04-11 (stable -- markdown skill project, no external dependencies)
