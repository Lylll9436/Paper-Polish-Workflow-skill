# Technology Stack

**Project:** Paper Polish Workflow (Academic Writing AI Skill Suite)
**Researched:** 2026-03-10
**Overall Confidence:** HIGH

> This is not a web application. The "stack" is the set of file formats, prompt engineering patterns, tool integrations, reference file structures, and authoring conventions that make Claude Code Skills effective for academic writing tasks.

---

## Recommended Stack

### 1. Skill File Format: Agent Skills Open Standard

| Component | Specification | Why |
|-----------|--------------|-----|
| File format | `SKILL.md` with YAML frontmatter + Markdown body | Agent Skills open standard (agentskills.io), adopted by Claude Code, Codex, Gemini CLI, VS Code Copilot, Cursor, and 20+ platforms. Future-proof and portable. |
| Frontmatter fields | `name` (required), `description` (required) | `name`: lowercase alphanumeric + hyphens, max 64 chars, must match parent directory. `description`: max 1024 chars, third person, includes trigger keywords. |
| Directory pattern | `skill-name/SKILL.md` + optional `references/`, `scripts/`, `examples/` | Progressive disclosure: metadata loaded at startup (zero cost), SKILL.md loaded on trigger, reference files loaded on demand. |
| Size limit | SKILL.md body under 500 lines | Anthropic's explicit recommendation. Once loaded, every token competes with conversation history. Move detail to reference files. |

**Confidence:** HIGH -- sourced from official Claude Code docs (code.claude.com/docs/en/skills) and Anthropic's best practices page (platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).

### 2. Skill Invocation Configuration

| Field | Value | Rationale |
|-------|-------|-----------|
| `disable-model-invocation` | `false` (default) for most skills | Academic writing skills should auto-trigger when Claude detects relevant user intent (e.g., "polish my abstract", "translate this section"). Manual-only invocation would add friction. |
| `disable-model-invocation` | `true` for heavy/destructive skills | Skills like "reviewer simulation" or "full-paper logic check" are expensive and should only run when explicitly invoked via `/skill-name`. |
| `user-invocable` | `true` (default) for all skills | Every skill should be available via `/skill-name` for direct invocation. |
| `allowed-tools` | Skill-specific | Polish/translate skills: `Read, Write, Edit, Glob, Grep`. Literature search skill: add Semantic Scholar MCP tools. Review skill: `Read, Glob, Grep` only (read-only). |
| `context` | Not set (inline) for most skills | Academic writing skills need conversation history (the user's paper, prior decisions). Forking would lose this context. Only use `context: fork` for independent research tasks like literature search. |

**Confidence:** HIGH -- official Claude Code documentation.

### 3. Reference File Organization

Use progressive disclosure. SKILL.md is the router; reference files hold domain knowledge.

```
.claude/skills/
├── paper-polish/
│   ├── SKILL.md                    # Core workflow (< 500 lines)
│   └── references/
│       └── expression-patterns.md  # Loaded when user needs phrasing help
├── paper-translate/
│   ├── SKILL.md
│   └── references/
│       └── latex-escaping.md       # LaTeX special char rules
├── paper-deai/
│   ├── SKILL.md
│   └── references/
│       └── ai-patterns.md          # 24+ AI writing tells to detect
├── paper-review/
│   └── SKILL.md
├── paper-literature/
│   └── SKILL.md
├── paper-logic-check/
│   └── SKILL.md
├── paper-figure-caption/
│   └── SKILL.md
├── paper-table-caption/
│   └── SKILL.md
├── paper-experiment-analysis/
│   └── SKILL.md
├── paper-abstract-gen/
│   └── SKILL.md
├── paper-cover-letter/
│   └── SKILL.md
└── paper-viz-recommend/
    └── SKILL.md

references/                         # Project-level shared references
├── journals/
│   ├── ceus.md                     # CEUS requirements (exists)
│   ├── ijgis.md                    # IJGIS requirements (future)
│   └── cities.md                   # Cities requirements (future)
└── expression-patterns.md          # Shared expression pattern library (exists)
```

**Key structural decisions:**

| Decision | Rationale |
|----------|-----------|
| Each skill gets its own directory | Agent Skills standard requires `skill-name/SKILL.md`. Enables skill-specific reference files via progressive disclosure. |
| Skill-specific references inside skill directory | Loaded only when that skill runs. AI pattern detection lists are irrelevant during translation. Zero token cost when not used. |
| Shared references at project root `references/` | Journal templates and expression patterns are used across multiple skills. Single source of truth avoids duplication. |
| References one level deep from SKILL.md | Anthropic explicitly warns: Claude may partially read files referenced from other referenced files. Keep reference depth flat. |
| No `scripts/` directory for this project | Pure prompt-based skills. No executable code needed. Journal word counting and LaTeX validation happen in Claude's native reasoning. |

**Confidence:** HIGH -- progressive disclosure is the foundational design principle per official docs.

### 4. MCP Tool Integration

| MCP Server | Purpose | Required? |
|------------|---------|-----------|
| **Semantic Scholar** (akapet00/semantic-scholar-mcp) | Literature search, citation verification, BibTeX export | Required for literature search skill. Optional for other skills that may need citation validation. |
| **Built-in Claude Code tools** | `Read`, `Write`, `Edit`, `Glob`, `Grep` for file operations | Always available. Core tools for reading papers and writing output. |

**Semantic Scholar MCP setup (recommended: akapet00/semantic-scholar-mcp):**

```bash
claude mcp add semantic-scholar -s user \
  -e SEMANTIC_SCHOLAR_API_KEY=your_key \
  -- uvx --from git+https://github.com/akapet00/semantic-scholar-mcp semantic-scholar-mcp
```

**Why akapet00 over other implementations:**
- Dedicated `bibtex.py` module for BibTeX export (critical for academic writing)
- Session-based paper tracking for batch operations
- ML-based paper recommendations
- Author deduplication
- Alternatives (FujishigeTemma, JackKuo, Benhao Tang) lack BibTeX module or session management

**Why an API key matters:** Unauthenticated Semantic Scholar requests share a global 100-requests-per-5-minutes limit. An authenticated key gives 1 request/second personal limit. The key is free. Essential for reliable agentic workflows that may search for multiple papers.

**Tool reference in SKILL.md:** Use fully qualified MCP tool names per Anthropic best practices:

```markdown
Use semantic-scholar:search_papers to find relevant literature.
Use semantic-scholar:export_bibtex to generate BibTeX entries.
```

**Confidence:** HIGH for Semantic Scholar integration. Multiple mature implementations exist. The API is stable.

### 5. Prompt Engineering Patterns

These are the "technology choices" for a prompt-based skill suite. Based on analysis of:
- awesome-ai-research-writing (17 prompts from MSRA, SH AI Lab, PKU, USTC, SJTU)
- Anthropic's official skill authoring best practices
- The humanizer skill (24 AI detection patterns)
- Claude prompt engineering best practices for 2025-2026

#### Pattern 1: Role Assignment + Domain Constraints

**Use because:** Academic writing has strict formatting rules. Claude must act as a domain expert, not a general assistant.

```markdown
You are a senior academic editor specializing in geography and urban science journals.
Your edits preserve the author's voice while elevating clarity and precision.

## Constraints
- Preserve all LaTeX commands and formatting
- Never add bold or italic markup
- Maintain consistent terminology throughout
- Use past tense for methods and results, present tense for discussion
```

**Source:** awesome-ai-research-writing prompts universally use role assignment. Anthropic best practices confirm that Claude benefits from clear role definition.

#### Pattern 2: Negative Prompting (Prohibition Lists)

**Use because:** LLMs default to predictable vocabulary. Academic writing demands precise, varied language.

```markdown
## Prohibited Vocabulary
Do not use these overused AI-characteristic words:
- "delve", "leverage", "tapestry", "pivotal", "vibrant", "nestled"
- "groundbreaking", "novel" (unless quoting the paper's own language)
- "it is important to note that", "it is worth mentioning"
- "in order to" (use "to")

## Prohibited Formatting
- No bullet lists in paper output (use flowing paragraphs)
- No bold text in paper output
- No Markdown formatting in paper output (output LaTeX or plain text)
```

**Source:** awesome-ai-research-writing's de-AI prompts and the humanizer skill both rely on prohibition lists. The humanizer documents 24 specific AI-writing tells organized into content, language, style, and communication categories.

#### Pattern 3: Modification Logging (Show Your Work)

**Use because:** Authors need to understand and approve every change. Blind rewrites are unacceptable in academic writing.

```markdown
## Output Format
For each modification, provide:

| Location | Original | Modified | Reason |
|----------|----------|----------|--------|
| S3 | "utilized a novel approach" | "used a graph-based method" | Removed vague "novel"; specified actual method |
```

**Source:** awesome-ai-research-writing's polishing and compression prompts all require modification logging. This is a domain requirement, not just a nice-to-have.

#### Pattern 4: Multi-Option Selection via Conversation

**Use because:** Academic writing is subjective. The author must choose the expression, not the AI.

```markdown
## Expression Selection
For each sentence, provide 2-4 expression options in a numbered table.
Always include an option for the user to provide their own version.
Process 3-5 sentences per batch for efficiency.
Wait for user selection before proceeding to the next batch.
```

**Note on `mcp_question`:** The existing SKILL.md references an `mcp_question` tool. This tool may or may not be available in the user's Claude Code environment. Skills should present options using standard conversation patterns (numbered tables) and use `mcp_question` as an enhancement when available, not as a hard dependency.

#### Pattern 5: Structured Workflow with User Checkpoints

**Use because:** Academic polishing is not a single-pass operation. Each step builds on confirmed decisions from the previous step.

```markdown
## Workflow
1. **Confirm structure** - User approves before proceeding
2. **Confirm sentence logic** - User approves before proceeding
3. **Select expressions** - User chooses from options
4. **Check coherence** - User reviews suggestions
5. **Final review** - User confirms output

Do not skip steps. Each step requires explicit user confirmation before moving forward.
```

**Source:** Core pattern from the existing project. awesome-ai-research-writing's Part II agent skills also use step-based workflows with user gates.

#### Pattern 6: Format-Aware Output (LaTeX Preservation)

**Use because:** Academic papers use LaTeX. Any skill that modifies text must preserve LaTeX commands, citations, cross-references, and math mode.

```markdown
## LaTeX Rules
- Preserve all \cite{}, \ref{}, \label{} commands exactly
- Preserve all math mode ($...$, \[...\]) exactly
- Escape special characters: % -> \%, & -> \&, # -> \#
- Do not add \textbf{} or \textit{} unless the original had them
- Preserve \paragraph{} structure in experiment analysis sections
```

**Source:** awesome-ai-research-writing's Chinese-to-English translation prompt has extensive LaTeX preservation rules. This is a hard requirement.

#### Pattern 7: Calm, Direct Instructions (Not Aggressive)

**Use because:** Claude 4.x models take aggressive language literally and overtrigger. Calm, direct instructions produce better and more consistent results.

```markdown
# Good
Preserve all LaTeX formatting. Do not modify citation commands.

# Bad (avoid)
CRITICAL: You MUST NEVER modify any LaTeX commands! ALWAYS preserve formatting!
```

**Source:** Anthropic's 2025-2026 prompt engineering documentation explicitly warns that phrases like "CRITICAL!", "YOU MUST", "NEVER EVER" hurt newer Claude models.

**Confidence:** HIGH for all patterns -- multiple corroborating sources across official docs and community projects.

### 6. Bilingual Support Pattern

| Aspect | Approach | Rationale |
|--------|----------|-----------|
| Trigger keywords | Include both English and Chinese in `description` | Users interact in both languages. E.g., "polish paper, revise paper, 润色论文, 精修论文" |
| Skill content | Write in English with Chinese annotations for key terms | English is the primary output language. Chinese annotations help Chinese-speaking users understand workflow steps. |
| Output language | Determined by input language and task | Translation skills output in target language. Polish skills output in the input language. |

**Confidence:** MEDIUM -- project-specific design, not an ecosystem standard.

---

## Not Applicable

This project has **no runtime dependencies**. There are:
- No npm packages (Skills are pure markdown)
- No Python packages in the skills themselves (only the MCP server needs Python)
- No build steps or compilation
- No database
- No infrastructure or deployment

The "stack" is entirely: Claude Code + SKILL.md files + reference markdown files + Semantic Scholar MCP server.

---

## Alternatives Considered

| Category | Recommended | Alternative | Why Not |
|----------|-------------|-------------|---------|
| Platform | Claude Code Skills (Agent Skills spec) | Cursor Rules / .cursorrules | PROJECT.md explicitly scopes to Claude Code. Agent Skills is an open standard with broader compatibility. |
| Skill architecture | Multiple independent skills (12) | Single skill with routing logic | Official docs cap SKILL.md at 500 lines. 12 capabilities in one file would be 2000+ lines. Each skill needs different tools and context. |
| Semantic Scholar MCP | akapet00/semantic-scholar-mcp | FujishigeTemma or JackKuo implementations | akapet00 has dedicated BibTeX module, session tracking, and ML recommendations. Others lack one or more of these. |
| Reference format | Markdown files | JSON or YAML data files | Claude parses markdown tables natively and efficiently. JSON adds parsing complexity without benefit for this content type. |
| De-AI approach | Pattern-based detection + two-pass rewrite | Single-pass "humanize" instruction | The humanizer skill demonstrates that a single pass misses patterns. Two passes (rewrite then audit then fix) catches more AI tells. |
| Interactive selection | Natural conversation (numbered options) | Relying solely on mcp_question | mcp_question may not be universally available. Natural conversation patterns are always available and work across all Claude Code environments. |
| Prompt style | Calm, specific instructions | Aggressive emphasis ("MUST", "CRITICAL") | Anthropic docs: aggressive language actively hurts Claude 4.x. Calm instructions are more effective and consistent. |

---

## What NOT to Do

| Anti-Pattern | Why It Fails |
|-------------|-------------|
| **Single massive SKILL.md with all capabilities** | Exceeds 500-line limit. Loads all context for every task. Claude's attention degrades with excessive instructions. |
| **Deeply nested reference files** (SKILL.md -> guide.md -> details.md) | Claude may only partially read files referenced from other referenced files. Keep references one level deep from SKILL.md. |
| **Using `context: fork` for polishing skills** | Forked subagents lose conversation history. Academic polishing requires iterative dialogue. Use fork only for independent tasks like literature search. |
| **Hardcoding journal requirements in SKILL.md** | Journal specs change and multiple skills reference them. Put in shared `references/journals/` for a single source of truth. |
| **Treating `mcp_question` as a hard dependency** | This tool may not exist in all environments. Design skills to work with natural conversation patterns, using mcp_question as an optional enhancement. |
| **Aggressive prompt language** | "CRITICAL!", "YOU MUST NEVER" -- Claude 4.x overtriggers on these. Use calm, direct language instead. |
| **Including generic explanations Claude already knows** | "LaTeX is a document preparation system" wastes tokens. Only include novel, domain-specific context. |
| **Embedding API keys in SKILL.md** | Semantic Scholar API keys must be environment variables, never in skill files. |
| **Windows-style paths in skills** | `references\journals\ceus.md` breaks on Unix. Always use forward slashes. |
| **Offering too many alternatives without a default** | "You can use pypdf, or pdfplumber, or PyMuPDF" is bad. Pick one default and mention alternatives only when they serve a different use case. |
| **Vague skill descriptions** | "Helps with academic writing" tells Claude nothing about when to activate. Be specific: "Polishes English academic paper sections using a structure-to-expression workflow." |

---

## Tool Access Per Skill

| Skill | Read | Write | Edit | Glob | Grep | Semantic Scholar | Invocation |
|-------|------|-------|------|------|------|------------------|------------|
| paper-polish | Yes | Yes | Yes | Yes | Yes | No | Auto + manual |
| paper-translate | Yes | Yes | Yes | No | No | No | Auto + manual |
| paper-deai | Yes | Yes | Yes | No | Yes | No | Auto + manual |
| paper-review | Yes | No | No | Yes | Yes | No | Manual only |
| paper-literature | Yes | Yes | No | Yes | Yes | Yes | Manual only |
| paper-logic-check | Yes | No | No | Yes | Yes | No | Manual only |
| paper-figure-caption | Yes | Yes | No | No | No | No | Auto + manual |
| paper-table-caption | Yes | Yes | No | No | No | No | Auto + manual |
| paper-experiment-analysis | Yes | Yes | No | No | No | No | Auto + manual |
| paper-abstract-gen | Yes | Yes | No | Yes | Yes | No | Auto + manual |
| paper-cover-letter | Yes | Yes | No | Yes | Yes | No | Auto + manual |
| paper-viz-recommend | Yes | Yes | No | No | No | No | Auto + manual |

---

## Installation

```bash
# 1. Copy skills into your project's Claude Code skills directory
cp -r .claude/skills/ /path/to/your-project/.claude/skills/

# 2. Copy shared references to your project root
cp -r references/ /path/to/your-project/references/

# 3. Set up Semantic Scholar MCP (optional, for literature search skill)
claude mcp add semantic-scholar -s user \
  -e SEMANTIC_SCHOLAR_API_KEY=your_key \
  -- uvx --from git+https://github.com/akapet00/semantic-scholar-mcp semantic-scholar-mcp

# 4. Get a free Semantic Scholar API key (recommended for reliability)
# Visit: https://www.semanticscholar.org/product/api#api-key
```

No `npm install` or `pip install` needed for the skills themselves.

---

## Sources

### Official Documentation (HIGH confidence)
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills) -- Skill format, frontmatter, discovery, progressive disclosure, advanced patterns
- [Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) -- Progressive disclosure, conciseness, evaluation-driven development, anti-patterns
- [Agent Skills Specification](https://agentskills.io/specification) -- Open standard for SKILL.md format, adopted across 20+ platforms

### Reference Projects (HIGH confidence)
- [awesome-ai-research-writing](https://github.com/Leey21/awesome-ai-research-writing) -- 17 prompt templates from MSRA, Seed, SH AI Lab, PKU, USTC, SJTU covering translation, polishing, de-AI, review, captions, experiment analysis
- [Anthropic Skills Repository](https://github.com/anthropics/skills) -- Official example skills demonstrating directory structure and patterns
- [Humanizer Skill](https://github.com/blader/humanizer) -- De-AI writing skill with 24 detection patterns, two-pass rewrite approach

### MCP Servers (HIGH confidence)
- [akapet00/semantic-scholar-mcp](https://github.com/akapet00/semantic-scholar-mcp) -- Recommended: BibTeX export, session tracking, ML recommendations
- [FujishigeTemma/semantic-scholar-mcp](https://github.com/FujishigeTemma/semantic-scholar-mcp) -- Alternative: citation format export, simpler setup

### Prompt Engineering (MEDIUM confidence)
- [Claude Prompt Engineering Best Practices 2026](https://promptbuilder.cc/blog/claude-prompt-engineering-best-practices-2026) -- 4-block pattern, calm instruction language for Claude 4.x
- [awesome-prompt-for-academic](https://github.com/BevalZ/awesome-prompt-for-academic) -- Academic prompt collection with multi-language support
- [chatgpt-prompts-for-academic-writing](https://github.com/ahmetbersoz/chatgpt-prompts-for-academic-writing) -- Broader academic writing prompt reference
