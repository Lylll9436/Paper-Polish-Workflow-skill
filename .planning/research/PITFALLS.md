# Domain Pitfalls: Academic Writing AI Tool Suite (Claude Code Skills)

**Domain:** Academic paper writing assistance as Claude Code Skills
**Researched:** 2026-03-10
**Overall confidence:** HIGH (triangulated from official docs, research papers, practitioner experience, and existing codebase analysis)

---

## Critical Pitfalls

Mistakes that cause rewrites, make Skills useless in practice, or undermine the entire project's value proposition.

---

### Pitfall 1: Citation Hallucination Treated as a Secondary Concern

**What goes wrong:** Skills that generate or suggest citations produce fabricated references. Studies show 40-65% of AI-generated citations contain errors or are entirely fabricated. Even GPT-5-class models fabricate citations 39% of the time without retrieval augmentation. NeurIPS 2025 accepted papers with 100+ hallucinated citations that passed peer review.

**Why it happens:** LLMs predict plausible-sounding text, not verified bibliographic records. They generate realistic author names, journal titles, and DOIs that look correct but point to nonexistent papers. The hallucinations are particularly insidious because they blend elements of real papers -- a real author name combined with a fabricated title, or a real title with wrong authors.

**Consequences:**
- Users submit papers with fake citations, risking desk rejection and reputational damage
- For less-researched topics (common in geography/urban science niche journals), hallucination rates are even higher (28-29% vs 6% for well-studied topics)
- A single fabricated citation discovered by reviewers can discredit an entire submission

**Prevention:**
1. Every Skill that touches citations MUST route through Semantic Scholar MCP for verification -- never generate citations from LLM memory alone
2. The Literature Search Skill must be the ONLY path for citation generation; other Skills should insert `[CITATION NEEDED]` placeholders instead of guessing
3. Implement a verification output format: for every citation suggested, include the Semantic Scholar paper ID, actual title, and actual authors so the user can cross-check
4. In the polishing and translation Skills, when encountering `\cite{}` commands, explicitly state "I have NOT verified this citation" rather than silently passing through or modifying them

**Detection (warning signs):**
- Skill output contains `\cite{AuthorYear}` references the user did not provide
- BibTeX entries appear in output without Semantic Scholar verification
- Skill descriptions mention "suggest relevant references" without specifying the verification mechanism

**Phase mapping:** Address in the Literature Search Skill (Phase 1 if building core Skills first). All other Skills should have citation-handling rules from day one.

**Confidence:** HIGH -- backed by multiple 2025 studies ([Enago Academy](https://www.enago.com/academy/ai-hallucinations-research-citations/), [PsyPost](https://www.psypost.org/study-finds-nearly-two-thirds-of-ai-generated-citations-are-fabricated-or-contain-errors/), [NeurIPS incident](https://www.yahoo.com/news/articles/neurips-one-world-top-academic-140000003.html))

---

### Pitfall 2: Author Voice Erasure (The "AI Homogenization" Problem)

**What goes wrong:** Polishing and translation Skills produce output that sounds like every other AI-polished paper. The text becomes grammatically perfect but loses the author's distinctive voice, rhetorical choices, and cultural perspective. Non-native English speakers are particularly affected -- their natural writing patterns get replaced with generic Western academic English.

**Why it happens:**
- LLMs converge on statistically common academic patterns, producing "average academic prose"
- Polishing prompts that say "improve clarity and professionalism" without constraints will rewrite everything into the same homogeneous style
- The expression-patterns reference file (currently 171 lines of templates) encourages formulaic writing when used as direct substitution rather than inspiration
- Hedging language gets smoothed away ("suggests" becomes "proves"), subtly changing meaning
- Sentence-level polishing without preserving the author's original sentence rhythm creates style discontinuity

**Consequences:**
- Reviewers and editors increasingly notice AI-polished papers (uniform sentence length, predictable transitions, overly parallel structures)
- The paper loses the researcher's authentic analytical voice, making it harder to defend during reviewer responses
- AI detectors flag the polished text, creating integrity concerns even when the research is entirely original
- Cultural and disciplinary rhetorical traditions get erased (e.g., Chinese academic writing conventions differ from Western ones)

**Prevention:**
1. Polishing Skills must include a "voice preservation" constraint: "Maintain the author's sentence structure preferences where they do not impede clarity. Only modify for grammatical errors, ambiguity, or journal-specific requirements."
2. Provide a "minimal polish" vs "deep restructure" mode. Default to minimal.
3. Before polishing, the Skill should analyze the author's writing patterns (average sentence length, preferred transitions, hedging frequency) and maintain these in output
4. The De-AI Skill should NOT be a "make it undetectable" tool. Instead, it should identify specific AI-telltale patterns and let the author choose which to address while preserving their natural voice
5. Translation Skills must preserve the source language's rhetorical structure where possible rather than imposing target-language conventions

**Detection (warning signs):**
- All polished sentences become roughly the same length
- Transition phrases are all drawn from a small set ("Furthermore," "Moreover," "Additionally,")
- Original hedging language ("our results suggest") gets replaced with stronger claims ("our results demonstrate")
- The user says "this doesn't sound like me"

**Phase mapping:** Must be built into the Polishing Skill, Translation Skill, and De-AI Skill from their initial implementation. Retrofitting voice preservation is very difficult.

**Confidence:** HIGH -- confirmed by [AWEJ literature review on authorial voice](https://awej.org/preserving-authorial-voice-in-academic-texts-in-the-age-of-generative-ai-a-thematic-literature-review/), [PMC study on excessive standardization](https://pmc.ncbi.nlm.nih.gov/articles/PMC10508892/)

---

### Pitfall 3: The "Impressive Demo, Useless in Practice" Skill

**What goes wrong:** Skills that produce impressive-looking output in demos but fail when used on real papers. Common examples:
- A Reviewer Simulation Skill that gives generic feedback ("consider strengthening the motivation") instead of pointing out specific logical gaps
- An Experiment Analysis Skill that summarizes data tables without actual analytical insight
- A Visualization Recommendation Skill that suggests chart types without considering journal conventions or data characteristics
- A Logic Verification Skill that checks superficial consistency but misses deep reasoning errors

**Why it happens:**
- Skills designed around what sounds useful rather than what actual paper-writing workflows need
- Prompts written to demonstrate capability rather than to be reliably useful
- Lack of real paper testing -- the current example session uses a fictional paper, which masks the Skill's limitations with real content
- AI reviewers achieve ~82% binary accept/reject accuracy but provide superficial feedback that lacks evidence-based justifications and fails to assess methodological novelty

**Consequences:**
- Users waste time running Skills that produce output they then discard
- Trust in the tool suite erodes after a few unhelpful experiences
- The project becomes a "prompt collection" rather than a production tool

**Prevention:**
1. Every Skill must be tested against at least one real paper before being considered complete. The fictional example session pattern should be replaced or supplemented with anonymized real-paper sessions
2. Define a "usefulness test" for each Skill: "Would the author copy this output into their paper with less than 2 minutes of editing?"
3. For the Reviewer Simulation Skill: instead of trying to replicate the full review process, focus on what AI is actually good at -- checking consistency, verifying literature coverage, spotting formatting issues, and flagging missing elements. Do NOT promise to evaluate novelty or theoretical contribution
4. For the Experiment Analysis Skill: require the user to provide their hypothesis and expected outcome, then have the Skill identify discrepancies and suggest discussion points rather than generating generic analysis paragraphs
5. Each Skill should have a clearly stated "this Skill does NOT do X" section to set expectations

**Detection (warning signs):**
- Skill produces output longer than 500 words without specific references to the user's content
- Skill output could apply to any paper in the field without modification
- The user has to significantly rewrite the output to make it usable
- Skill description promises "comprehensive review" or "thorough analysis"

**Phase mapping:** Apply to every Skill during its development phase. Testing with real papers should be a mandatory milestone gate before marking any Skill as "validated."

**Confidence:** HIGH -- supported by [Stanford Agentic Reviewer analysis](https://paperreview.ai/tech-overview), [ReviewAgents paper](https://arxiv.org/html/2503.08506), current example session analysis

---

### Pitfall 4: SKILL.md Context Window Bloat

**What goes wrong:** A single SKILL.md file tries to contain all instructions, examples, templates, and reference material. This exhausts the context window when combined with the user's paper content, leading to degraded performance, forgotten instructions, or hard "Prompt is too long" errors.

**Why it happens:**
- The current polishing SKILL.md is 347 lines with inline examples, mcp_question code blocks, and journal-specific content
- When Claude loads the Skill and then reads the user's paper section (often 2000+ words), plus conversation history, the context fills rapidly
- "Context rot" research shows that even within the context window limit, irrelevant information causes reasoning performance to drop 13-85%
- Multiple Skills loaded simultaneously (e.g., polishing + journal template + expression patterns) compound the problem
- Skills that include supporting files inline rather than referencing them for lazy loading

**Consequences:**
- Claude "forgets" earlier Skill instructions mid-session, especially during the later steps of multi-step workflows
- Quality degrades as the session progresses -- Step 5 and Step 6 of the polishing workflow get worse output than Step 1
- Sessions crash with "Prompt is too long" errors, losing all progress
- Users forced to restart sessions repeatedly, breaking the workflow

**Prevention:**
1. Keep each SKILL.md under 500 lines (Claude Code official recommendation). Aim for 200-300 lines of core instructions
2. Move reference material (expression patterns, journal templates) to separate files. Reference them with "For details, see [reference.md](reference.md)" -- Claude loads these only when needed
3. Use `context: fork` for Skills that process large amounts of text (full-paper Logic Verification, cross-section consistency) so they run in an isolated subagent context
4. Do NOT embed multiple full code examples of `mcp_question` calls in the Skill. One concise example is sufficient; the rest is pattern repetition that wastes context
5. Design multi-step workflows to be resumable: each step should read the current state from a file rather than depending on conversation history
6. Expression patterns reference should be consulted via file Read during Step 3, not loaded into the Skill prompt

**Detection (warning signs):**
- SKILL.md exceeds 500 lines
- Skill contains more than one full `mcp_question` code block example
- Skill embeds journal-specific rules inline instead of referencing external files
- Users report that Claude "forgets" instructions from the beginning of the session

**Phase mapping:** Address in the very first phase when establishing the Skill template pattern. Every subsequent Skill should follow the lean template. The existing polishing SKILL.md needs restructuring before building additional Skills.

**Confidence:** HIGH -- supported by [Claude Code official docs](https://code.claude.com/docs/en/skills), [context rot research](https://www.ramanean.com/troubleshooting-claude-code-fixing-the-context-prompt-too-long-error/), [Anthropic context engineering](https://01.me/en/2025/12/context-engineering-from-claude/)

---

## Moderate Pitfalls

Mistakes that cause significant rework or reduced effectiveness but are recoverable.

---

### Pitfall 5: Rigid Step-by-Step Workflow That Ignores Real Usage

**What goes wrong:** The polishing workflow enforces a strict linear sequence (Structure -> Logic -> Expression -> Reference Check -> Coherence -> Final) that doesn't match how authors actually work. Users want to polish a single paragraph, fix one awkward sentence, or jump directly to expression options without going through structural analysis.

**Why it happens:**
- The current 6-step design was created as an idealized workflow, not observed from actual usage
- The user already identified this: "Current 6-step flow is too rigid"
- Academic writing is inherently iterative and non-linear -- authors don't polish a paper in a single pass through a fixed sequence

**Prevention:**
1. Design Skills as independent capabilities, not steps in a sequence. A user should be able to invoke "polish this paragraph" without first confirming macro structure
2. The polishing Skill should detect scope from context: single sentence vs paragraph vs section vs full paper, and adapt its workflow accordingly
3. Provide a "full workflow" mode AND a "quick fix" mode. The full workflow is the current 6-step process; the quick fix skips to expression options for a specific passage
4. Each step should be independently invocable and produce useful output even without preceding steps

**Detection (warning signs):**
- Users frequently skip steps or express frustration at the sequential requirement
- The Skill description includes mandatory checkpoints that block progress
- "Checkpoint: User confirms X before proceeding to Step Y" appears as a hard gate rather than a suggestion

**Phase mapping:** Address when redesigning the Polishing Skill. The Skill architecture should support both guided and quick-access modes from the initial design.

**Confidence:** HIGH -- directly stated by the user in PROJECT.md

---

### Pitfall 6: The Expression Patterns Reference as a Crutch, Not a Guide

**What goes wrong:** The expression patterns reference file becomes the primary source for polished text, leading to formulaic output. Every paper polished with this tool starts with "has gained increasing importance in..." or transitions with "To address these limitations, this study introduces..."

**Why it happens:**
- The current `expression-patterns.md` contains 171 lines of templates organized by function (Background, Gap, Method, Transition, etc.)
- If the Skill instructs Claude to "consult expression-patterns.md for professional phrasing," Claude will pattern-match from this file rather than generating contextually appropriate expressions
- The patterns are drawn from one disciplinary style (urban/geography), making them inappropriate for cross-domain use
- The awesome-ai-research-writing reference project similarly provides template patterns that encourage direct substitution

**Prevention:**
1. Frame expression patterns as "patterns to be aware of" not "patterns to copy." The Skill instruction should say: "Use expression-patterns.md to understand the register and formality level expected, then generate original expressions that match the author's voice"
2. Include anti-patterns in the reference: "Overused phrases to avoid: 'has gained increasing importance,' 'plays a crucial role in'"
3. Regularly update the patterns file to remove any phrase that appears in more than 5% of papers in the target field (a sign it has become cliched)
4. The polishing Skill should generate multiple expression options with variety in structure, not just vocabulary swaps of the same template

**Detection (warning signs):**
- Multiple papers polished with this tool share the same opening phrases
- The expression patterns file grows monotonically without pruning
- Claude's polishing suggestions are almost always drawn from the patterns file rather than generated fresh

**Phase mapping:** Address when building the Polishing Skill and when maintaining the expression patterns reference. Include "anti-patterns" section from the start.

**Confidence:** MEDIUM -- inferred from current codebase analysis and general prompt engineering best practices

---

### Pitfall 7: Monolithic Single-Skill vs. Uncoordinated Multi-Skill

**What goes wrong:** Two failure modes in the architecture:
- **Monolithic:** Everything in one giant SKILL.md (the awesome-ai-research-writing approach of putting 17 prompts in one README). Impossible to maintain, test, or improve individual capabilities
- **Fragmented:** Each capability is an isolated Skill with no awareness of others, leading to contradictory advice (polishing Skill suggests one phrasing, de-AI Skill rewrites it back, reviewer Skill flags the de-AI output as awkward)

**Why it happens:**
- The project has already decided on multi-Skill architecture (correct decision), but coordination between Skills is not yet designed
- The awesome-ai-research-writing reference project puts everything in one file because there is no coordination mechanism between separate prompts
- Claude Code does not natively coordinate between Skills -- each Skill runs independently

**Prevention:**
1. Define a shared `references/conventions.md` file that ALL Skills must follow. This includes terminology decisions, preferred tense conventions, and formatting rules. Each Skill reads this as foundational context
2. Design explicit handoff protocols: the output format of one Skill should be directly consumable as input by the next logical Skill (e.g., polishing output goes into de-AI input)
3. Journal template files (like `ceus.md`) serve as the single source of truth for style rules. No Skill should hardcode journal-specific rules inline
4. Use a consistent file-based state management pattern: Skills write intermediate results to files (e.g., `sections/01_abstract_polished.md`), and downstream Skills read from these files rather than depending on conversation context

**Detection (warning signs):**
- Two Skills give contradictory style advice
- Users have to manually copy output from one Skill to provide as input to another
- Journal-specific rules are duplicated across multiple SKILL.md files
- The total character budget for Skill descriptions is exceeded (16,000 character default)

**Phase mapping:** Establish the shared conventions file and inter-Skill communication pattern in the first phase, before building individual Skills. This is architectural foundation work.

**Confidence:** HIGH -- based on [Claude Code Skills documentation](https://code.claude.com/docs/en/skills), prompt debt research ([PromptDebt paper](https://arxiv.org/html/2509.20497v1))

---

### Pitfall 8: De-AI Detection Skill Creates an Arms Race, Not Better Writing

**What goes wrong:** The De-AI Skill becomes a tool for evading AI detection rather than improving writing quality. It introduces unnatural stylistic variation, obscure synonyms, and deliberate "imperfections" to fool detectors, making the text worse rather than better.

**Why it happens:**
- AI detection is fundamentally unreliable -- false positive rates are significant, and non-native English speakers are disproportionately flagged
- "Humanizer" tools use rewriting strategies that prioritize surface-level unpredictability over rhetorical clarity
- Detection-oriented rewriting may adjust hedging language that is academically important ("suggests" vs "proves") or introduce meaning drift
- The arms race between generation and detection is escalating: 2026 detection systems look for measurable patterns beyond surface wording

**Prevention:**
1. Reframe the De-AI Skill's purpose: "Identify and address patterns that make your writing sound generic or formulaic" rather than "make your text undetectable by AI detectors"
2. Focus on genuinely improving writing quality: varying sentence structure, using field-specific terminology, adding author-specific analytical depth. These happen to also reduce AI detection scores, but the goal is better writing
3. Never claim or imply that the Skill can guarantee undetectability -- this is technically impossible and ethically questionable
4. Include a warning: "The best way to avoid AI detection concerns is to use AI as an editing assistant while maintaining your own voice and ideas"
5. Provide specific diagnostic output: "These 3 sentences have AI-typical patterns: uniform length, predictable parallel structure, and generic transition words" with targeted fixes rather than wholesale rewriting

**Detection (warning signs):**
- The Skill description mentions "bypass," "undetectable," or "fool detectors"
- The Skill rewrites entire paragraphs at once instead of suggesting targeted edits
- Output includes deliberately awkward phrasing or uncommon synonyms introduced solely to reduce detection scores

**Phase mapping:** Address when building the De-AI Skill. The framing and goal-setting in the Skill description and instructions are critical from the start.

**Confidence:** HIGH -- supported by [HumanizeAI analysis](https://humanizeai.com/blog/how-to-humanize-ai-text/), [Paperpal detection research](https://paperpal.com/blog/press-release/why-paperpal-written-text-flagged-by-ai-detectors)

---

### Pitfall 9: Semantic Scholar MCP as Single Point of Failure for Literature

**What goes wrong:** The Literature Search Skill depends entirely on Semantic Scholar MCP, which has rate limits, coverage gaps, and API instability. When it fails or returns incomplete results, the Skill becomes useless.

**Why it happens:**
- Semantic Scholar API has rate limits: 1 RPS for new API keys, shared public pool for unauthenticated access
- Coverage gaps: Springer abstracts are not shown due to publisher agreements. Newer papers may have incomplete metadata
- API requires exponential backoff, which means batch searches (needed for literature reviews) can be slow
- The MCP server implementations are community-maintained, not officially supported by Semantic Scholar

**Prevention:**
1. Design the Literature Search Skill to degrade gracefully: if Semantic Scholar is unavailable, output should clearly state "unable to verify" rather than falling back to LLM-generated citations
2. Cache and reuse search results within a session to minimize API calls
3. Recommend users obtain a Semantic Scholar API key for reliable access. Include setup instructions in the Skill or project README
4. For citation verification in other Skills (polishing, translation), use a lightweight check (paper ID lookup) rather than full search queries
5. Consider supporting multiple search backends in future phases, but do NOT design for this in Phase 1 -- premature abstraction

**Detection (warning signs):**
- Users report "no results found" for papers they know exist
- The Skill silently falls back to generating citations from LLM memory when the API fails
- Batch literature review requests timeout or hit rate limits

**Phase mapping:** Address when building the Literature Search Skill. API key setup should be part of project installation documentation.

**Confidence:** MEDIUM -- based on [Semantic Scholar API documentation](https://www.semanticscholar.org/product/api), MCP server implementations

---

## Minor Pitfalls

Mistakes that cause inconvenience or suboptimal results but are easily corrected.

---

### Pitfall 10: Tool Name Confusion in Skill Instructions

**What goes wrong:** The current SKILL.md references tools with incorrect names: `mcp_question`, `mcp_read`, `mcp_write`, `mcp_edit`, `mcp_look_at`. These are not the actual Claude Code tool names. Claude Code uses `AskUserQuestion` (for the question tool, which may not even exist in the current version), `Read`, `Write`, `Edit`, `Bash`, `Grep`, `Glob`, etc.

**Why it happens:**
- The SKILL.md was likely written based on an earlier version of Claude Code or a different platform's tool naming convention
- The awesome-ai-research-writing reference project may use different tool names (OpenCode, Cursor)
- Tool names evolve across platform versions

**Prevention:**
1. Verify all tool names against the current Claude Code documentation before writing any Skill
2. Use only standard Claude Code tool names in SKILL.md: `Read`, `Write`, `Edit`, `Bash`, `Grep`, `Glob`, `WebSearch`, `WebFetch`
3. For interactive selection, verify whether `AskUserQuestion` or a similar tool exists and is available. If not, design the interaction pattern using standard conversation (presenting options in markdown and asking the user to type their choice)
4. Document the available tool names in the shared conventions file so all Skills are consistent

**Detection (warning signs):**
- Tool names in SKILL.md that start with `mcp_` prefix (outdated convention)
- Skills referencing tools that do not appear in `allowed-tools` documentation
- Users report errors when the Skill tries to use a nonexistent tool

**Phase mapping:** Fix immediately in Phase 1 when establishing the Skill template pattern. Audit all tool references in the current SKILL.md.

**Confidence:** HIGH -- verified against [Claude Code Skills documentation](https://code.claude.com/docs/en/skills)

---

### Pitfall 11: Journal Template Proliferation Without Abstraction

**What goes wrong:** As journal templates are added (CEUS, IJGIS, Cities, etc.), each template duplicates common academic formatting rules with minor variations. Updating a shared convention (e.g., APA citation style) requires editing every template file.

**Why it happens:**
- The current design has one journal template file (`ceus.md`) with all requirements inline
- No abstraction layer separates "common academic rules" from "journal-specific rules"
- Copy-paste between journal templates is the path of least resistance

**Prevention:**
1. Separate journal templates into two layers:
   - `references/academic-conventions.md` -- common rules (verb tense conventions, citation formatting, hedging guidelines)
   - `references/journals/[journal].md` -- journal-specific overrides only (word limits, required sections, special formatting)
2. Journal templates should be structured data (tables with specific values) not prose instructions. This makes them easier to parse and less likely to have inconsistencies
3. Design the first template (CEUS) with this separation in mind. Adding IJGIS should require only the delta from common conventions

**Detection (warning signs):**
- Multiple journal templates contain identical paragraphs about verb tense or hedging
- Updating a common rule requires touching more than one file
- New journal templates are created by copying CEUS and modifying it

**Phase mapping:** Address when establishing the journal template structure in Phase 1. The CEUS template already exists and should be refactored to demonstrate the pattern before adding new journals.

**Confidence:** MEDIUM -- inferred from software engineering principles applied to this specific context

---

### Pitfall 12: Prompt Debt from Unversioned Skill Evolution

**What goes wrong:** Skills get iteratively tweaked (adding a sentence here, changing a word there) without tracking why changes were made. After several iterations, the Skill contains contradictory instructions, obsolete examples, and instructions that were added to fix one edge case but cause problems in other cases.

**Why it happens:**
- "Prompt debt" -- the accumulation of mismatched instructions and forgotten context in prompts
- Skills are markdown files that don't have test suites or type checking
- An engineer fixes one user's complaint by adding an instruction, not realizing it conflicts with an earlier one
- No evaluation framework to measure whether a change improved or degraded Skill quality

**Prevention:**
1. Treat each SKILL.md change as a deliberate design decision. Add comments (or a changelog section within the Skill) explaining WHY an instruction exists, not just WHAT it says
2. Use git commit messages to document the reasoning behind Skill changes
3. Establish a small set of "test inputs" for each Skill: known paper excerpts with expected output characteristics. Run these after any Skill modification to check for regressions
4. Periodically review each Skill for contradictory or obsolete instructions (every 3-6 months or when the underlying Claude model changes)
5. When Claude Code updates its model, re-test all Skills. Prompt engineering techniques that work on Claude Opus 4 may need adjustment for future models

**Detection (warning signs):**
- Skill contains instructions that use "MUST" and "NEVER" for contradictory behaviors
- Skill has grown beyond 500 lines through accumulated additions
- Users report inconsistent behavior from the same Skill across different sessions
- Multiple instructions address the same concern with different approaches

**Phase mapping:** Establish the prompt hygiene practices in Phase 1 (template pattern and conventions). Apply continuously throughout all subsequent phases.

**Confidence:** MEDIUM -- supported by [PromptDebt research](https://arxiv.org/html/2509.20497v1), [prompt debt practitioner analysis](https://medium.com/@johnmunn/prompt-debt-6e6e05c7958a)

---

## Phase-Specific Warnings

| Phase Topic | Likely Pitfall | Mitigation |
|---|---|---|
| Skill Template & Architecture Setup | Pitfall 4 (context bloat), Pitfall 7 (fragmentation), Pitfall 10 (tool names) | Establish lean Skill template, shared conventions file, verify tool names against current docs |
| Polishing Skill Redesign | Pitfall 2 (voice erasure), Pitfall 5 (rigid workflow), Pitfall 6 (expression crutch) | Build voice preservation into prompt design, support both guided and quick-fix modes, frame patterns as inspiration not templates |
| Translation Skill | Pitfall 2 (voice erasure), Pitfall 1 (citation hallucination in translated citations) | Preserve source rhetorical structure, never translate citation content (only format) |
| De-AI Detection Skill | Pitfall 8 (arms race), Pitfall 2 (voice erasure) | Frame as writing quality improvement, not detection evasion; provide targeted diagnostic fixes |
| Reviewer Simulation Skill | Pitfall 3 (useless in practice) | Limit to what AI does well (consistency, completeness, formatting checks); do NOT promise novelty assessment |
| Literature Search Skill | Pitfall 1 (citation hallucination), Pitfall 9 (API dependency) | Route all citations through Semantic Scholar, design graceful degradation, include API key setup docs |
| Logic Verification Skill | Pitfall 3 (useless in practice), Pitfall 4 (context bloat) | Use `context: fork` for subagent isolation; focus on cross-reference consistency rather than deep reasoning |
| Figure/Table Caption Skill | Pitfall 3 (useless in practice) | Require the user to provide data context; generate captions that match journal conventions, not generic descriptions |
| Experiment Analysis Skill | Pitfall 3 (useless in practice), Pitfall 1 (fabricated statistical claims) | Require user-provided hypothesis; analyze discrepancies, don't generate generic discussion |
| Journal Template Expansion | Pitfall 11 (template proliferation) | Maintain two-layer separation (common conventions + journal-specific overrides) |
| Ongoing Maintenance | Pitfall 12 (prompt debt) | Version-control Skill changes with rationale; periodic regression testing |

---

## Sources

### Citation Hallucination
- [Enago Academy: AI Hallucinations in Research](https://www.enago.com/academy/ai-hallucinations-research-citations/)
- [PsyPost: Nearly two-thirds of AI-generated citations fabricated](https://www.psypost.org/study-finds-nearly-two-thirds-of-ai-generated-citations-are-fabricated-or-contain-errors/)
- [NeurIPS hallucinated citations incident](https://www.yahoo.com/news/articles/neurips-one-world-top-academic-140000003.html)
- [CheckIfExist: Detecting Citation Hallucinations](https://arxiv.org/html/2602.15871v1)

### Author Voice and AI Writing
- [AWEJ: Preserving Authorial Voice in the Age of Generative AI](https://awej.org/preserving-authorial-voice-in-academic-texts-in-the-age-of-generative-ai-a-thematic-literature-review/)
- [PMC: AI to improve scientific writing of non-native speakers](https://pmc.ncbi.nlm.nih.gov/articles/PMC10508892/)
- [JustDone: How AI Detection Fails Non-Native English Writers](https://justdone.com/blog/writing/ai-detection-for-esl)

### AI Review Simulation
- [Stanford Agentic Reviewer Technical Overview](https://paperreview.ai/tech-overview)
- [ReviewAgents: Bridging Human and AI Paper Reviews](https://arxiv.org/html/2503.08506)
- [ReviewerToo: Should AI Join the Program Committee?](https://arxiv.org/html/2510.08867)

### De-AI Detection
- [HumanizeAI: How to Humanize AI Text for Academic Writing](https://humanizeai.com/blog/how-to-humanize-ai-text/)
- [Paperpal: Understanding AI Detection Scores](https://paperpal.com/blog/press-release/why-paperpal-written-text-flagged-by-ai-detectors)

### Prompt Debt and Maintenance
- [PromptDebt: Technical Debt Across LLM Projects](https://arxiv.org/html/2509.20497v1)
- [Prompt Debt: The Silent Failure Mode](https://medium.com/@johnmunn/prompt-debt-6e6e05c7958a)

### Claude Code Skills
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills)
- [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)
- [Context Engineering from Anthropic](https://01.me/en/2025/12/context-engineering-from-claude/)
- [Context Overflow Troubleshooting](https://www.ramanean.com/troubleshooting-claude-code-fixing-the-context-prompt-too-long-error/)

### Semantic Scholar API
- [Semantic Scholar Academic Graph API](https://www.semanticscholar.org/product/api)
- [Semantic Scholar MCP Server](https://lobehub.com/mcp/fujishigetemma-semantic-scholar-mcp)

### Reference Project
- [awesome-ai-research-writing (Leey21)](https://github.com/Leey21/awesome-ai-research-writing)
