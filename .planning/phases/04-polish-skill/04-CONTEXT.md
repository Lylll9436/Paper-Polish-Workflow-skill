# Phase 4: Polish Skill - Context

**Gathered:** 2026-03-11
**Status:** Ready for planning

<domain>
## Phase Boundary

Build an English academic paper polishing Skill that supports two modes: Quick-fix (default, intelligent single-pass) and Guided (fixed three-step: structure → logic → expression). The Skill edits files in-place with LaTeX comment annotations for traceability, loads expression patterns and anti-AI patterns on demand, and adapts to journal-specific style when specified. This phase replaces the existing rigid 6-step legacy SKILL.md entirely.

</domain>

<decisions>
## Implementation Decisions

### Mode design
- Default mode is Quick-fix: user says "polish this" and Skill runs directly
- Quick-fix intelligently judges what to focus on based on input text quality — expression issues get rewritten, structural issues get adjusted, all in one pass
- Guided mode activated only when user explicitly requests it (e.g., "guided polish", "引导模式润色")
- Guided mode follows fixed three steps: structure → logic → expression, each step completes before next begins

### Guided mode confirmation
- Each step (structure/logic/expression) lists discovered problems as a checklist
- User selects which problems to fix from the list
- Selected fixes are applied in batch, then the next step begins
- Logic step includes cross-section coherence check (argument chains, terminology consistency) when input covers multiple sections

### Output and modification presentation
- In-place modification: Skill edits the original file directly using Edit tool
- Before each modification, the original text is preserved as a LaTeX comment annotation (e.g., `% Original: ...` followed by the new text)
- After user confirms acceptance, annotations are automatically cleaned up (original text comments removed)
- A concise summary report is generated after modifications: number of changes, main types of modifications, notes/caveats

### Input granularity
- Smart adaptation: short text is polished entirely, long text is split by section and processed in batches
- Both file path and pasted text are supported as input
- Pasted text results are output directly in the conversation (no file writing needed since there's no file to edit in-place)

### Translation output awareness
- When input appears to come from translation (detected by translation artifacts like translationese, Chinese-English calques), Polish Skill pays special attention to:
  - Translationese patterns (直译腔)
  - Chinese-English calques (中式英语)
  - Overly literal sentence structures
- This is automatic detection, not a flag the user sets

### Anti-AI integration
- Anti-AI patterns library is proactively loaded during polishing (same as Translation Skill)
- Polished output avoids high-frequency AI vocabulary from the blacklist
- Summary report includes a simple prompt: "Recommend running De-AI Skill for further detection check" when applicable

### Claude's Discretion
- Exact threshold for "short text" vs "long text" smart adaptation split
- How to detect translationese vs native English issues
- Internal strategy for Quick-fix intelligent judgment (which dimensions to prioritize)
- Exact LaTeX comment annotation format

</decisions>

<specifics>
## Specific Ideas

- The legacy 6-step SKILL.md is too rigid — the new design should feel adaptive, not mechanical
- Quick-fix should feel like handing your draft to a skilled colleague: they fix what needs fixing without asking 20 questions
- Guided mode problem-list approach avoids the old "sentence-by-sentence confirmation" tedium while keeping user control
- LaTeX comment annotations let users review changes in their editor without needing a separate diff tool
- Journal style should influence the entire polishing process, not just expression-level tweaks

</specifics>

<code_context>
## Existing Code Insights

### Reusable Assets
- `.claude/skills/translation-skill/SKILL.md`: established Skill pattern (210 lines, follows Phase 2 conventions) — Polish Skill should follow same structure
- `references/expression-patterns.md` + leaf modules: organized by writing scenario — Polish Skill loads relevant leaf based on section type
- `references/anti-ai-patterns.md` + category modules: AI vocabulary blacklist and alternatives — loaded proactively during polishing
- `references/journals/ceus.md`: journal style contract — loaded when CEUS is target journal
- `references/skill-conventions.md`: frontmatter contract and body structure rules
- `references/skill-skeleton.md`: copyable starting point

### Established Patterns
- Stable-entrypoint + leaf-module reference loading (Phase 1)
- Frontmatter with `references.required` and `references.leaf_hints` (Phase 2)
- Four interaction modes: interactive, guided, direct, batch (Phase 2)
- Ask-first posture with fallback to plain-text questions (Phase 2)
- Journal template missing → refuse, not fallback (Phase 3)
- Anti-AI patterns loaded proactively during processing (Phase 3)

### Integration Points
- Skill lives at `.claude/skills/polish-skill/SKILL.md`
- Consumes Translation Skill output as potential input (detects translation artifacts)
- Output feeds into De-AI Skill (summary prompts user to run De-AI)
- Uses Edit tool for in-place modifications (not Write for new files)
- References same expression-patterns and journal templates as Translation Skill

</code_context>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 04-polish-skill*
*Context gathered: 2026-03-11*
