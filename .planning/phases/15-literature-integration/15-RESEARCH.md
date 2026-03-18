# Phase 15: Literature Integration - Research

**Researched:** 2026-03-18
**Domain:** Semantic Scholar MCP batch integration into repo-to-paper-skill
**Confidence:** HIGH

## Summary

Phase 15 adds a Literature Collection step (Step 2.5) to the existing repo-to-paper-skill, inserted between H2 confirmation (current Step 3) and H3 generation (current Step 4). The implementation modifies a single file: `.claude/skills/repo-to-paper-skill/SKILL.md` (currently 286 lines). The step derives search queries from H1+H2 titles, calls Semantic Scholar MCP in a batch pattern (non-interactive, unlike the standalone literature-skill's numbered-selection flow), and writes per-section ref files to `{repo_path}/.paper-refs/`.

The core technical challenge is managing 15-30 sequential MCP calls (one per H2 subsection) within Semantic Scholar's rate limits (1 RPS authenticated, shared pool unauthenticated). The MCP tool surface is well-established in this project: `mcp__semantic-scholar__papers-search-basic` for keyword search and `mcp__semantic-scholar__get-paper-abstract` for abstract fetching are both verified working from Phase 9 (literature-skill). The main design work is adapting the interactive single-search pattern to a batch non-interactive pattern with progress display.

All user decisions from the CONTEXT.md are locked and specific. The ref file format, query strategy, volume targets, user review flow, and fallback behavior are fully specified. Claude's discretion covers implementation details: query formulation algorithm, relevance filtering threshold, abstract summary length, rate limiting handling, and citation key format.

**Primary recommendation:** Implement Step 2.5 as a self-contained block within repo-to-paper-skill SKILL.md. Reuse literature-skill's MCP pre-flight check pattern verbatim. Keep query derivation simple (H1 topic + H2 keywords, 2-5 terms). Write ref files using the Markdown card format specified in CONTEXT.md.

<user_constraints>

## User Constraints (from CONTEXT.md)

### Locked Decisions

- **Integration point**: Step 2.5 (decimal numbering), inserted between H2 confirmation (Step 3) and H3 generation (Step 4) in repo-to-paper-skill
- **Line budget**: Allow exceeding the 300-line Skill convention; functional completeness takes priority
- **MCP fallback**: If Semantic Scholar MCP unavailable at pre-flight, skip entire Step 2.5; insert `[CITATION NEEDED]` placeholders; continue to H3 without blocking
- **Query strategy**: H1 + H2 combined; extract core terms from English titles/descriptions only; 2-5 key terms per search
- **Reference volume**: 5-10 refs per H2 subsection, claim-density driven (not fixed count)
- **User review**: Automatic collection with progress display per H2; summary table after all H2 processed; user confirms summary to proceed
- **Zero results**: Display warning `⚠ 1.1 Research Background: 0 refs found` + mark as `[CITATION NEEDED]`; continue
- **Ref file organization**: Per-H1-section files in `{repo_path}/.paper-refs/` (e.g., `introduction.md`, `methods.md`)
- **Ref card format**: Markdown cards with Title, Authors, Year, Citations, Relevance, Abstract summary, BibTeX
- **Duplicate handling**: Allow same paper in multiple section files; BibTeX dedup deferred to Phase 16
- **Directory location**: `{repo_path}/.paper-refs/` in target experiment repo root

### Claude's Discretion

- Exact query formulation algorithm (how to combine H1 + H2 terms)
- Relevance filtering threshold (which papers from search results to keep vs discard)
- Abstract summary length (1-2 sentences extracted from MCP abstract data)
- How to handle MCP rate limiting if encountered during batch queries
- BibTeX citation key generation format (reuse literature-skill's `firstAuthorYYYYkeyword` pattern)

### Deferred Ideas (OUT OF SCOPE)

None -- discussion stayed within phase scope.

</user_constraints>

<phase_requirements>

## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| REPO-04 | At H2 completion, all section references are collected via Semantic Scholar with metadata + abstracts and saved to ref files | MCP tools verified (`papers-search-basic`, `get-paper-abstract`); ref file format specified in CONTEXT.md; pre-flight check pattern available from literature-skill; batch query architecture documented below |

</phase_requirements>

---

## Standard Stack

### Core

| Component | Path/Version | Purpose | Why Standard |
|-----------|-------------|---------|--------------|
| `mcp__semantic-scholar__papers-search-basic` | MCP tool | Search papers by keyword query | Already used by literature-skill; confirmed in project permissions; primary discovery tool |
| `mcp__semantic-scholar__get-paper-abstract` | MCP tool | Fetch abstract for papers where search result abstract is empty | Used by literature-skill Step 3; needed for Springer papers (abstracts not in search results per licensing) |
| `repo-to-paper-skill/SKILL.md` | `.claude/skills/repo-to-paper-skill/SKILL.md` | File being modified (currently 286 lines, 4-step workflow) | The target Skill |
| `literature-skill/SKILL.md` | `.claude/skills/literature-skill/SKILL.md` | Source of reusable MCP patterns (pre-flight check, search call, BibTeX generation) | Established MCP interaction patterns from Phase 9 |

### Supporting

| Component | Path | Purpose | When to Use |
|-----------|------|---------|-------------|
| `references/bilingual-output.md` | Stable path | Bilingual display format for progress/summary if bilingual mode active | Always loaded (already in repo-to-paper required refs) |
| `references/skill-conventions.md` | Stable path | Line budget guidance, Skill structure rules | Reference during authoring |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| `papers-search-basic` | `paper-search-advanced` | Advanced supports boolean operators and filters; but basic is simpler for auto-derived queries and more likely to return relevant results for short keyword queries |
| Per-H1-section ref files | Single `references-collected.md` | ARCHITECTURE.md originally proposed single file; CONTEXT.md decided per-H1 files for self-contained section review. Follow CONTEXT.md (locked decision) |
| `papers-batch` | Sequential `papers-search-basic` calls | Batch endpoint is for looking up known paper IDs, not keyword search. Not applicable for discovery. |

---

## Architecture Patterns

### Integration Point in repo-to-paper-skill

The current 4-step workflow becomes a 5-step workflow with decimal numbering to avoid renumbering:

```
Step 1: Scan Repository          (unchanged)
Step 2: Generate H1 Outline      (unchanged)
Step 3: Generate H2 Outline      (unchanged -- user confirms H2 here)
Step 2.5: Literature Collection   (NEW -- runs after Step 3 confirmation)
Step 4: Generate H3 Outline      (unchanged)
```

Note: The step numbering is intentionally non-sequential (2.5 after 3) per CONTEXT.md decision. This avoids renumbering Steps 3 and 4 which are referenced by other documentation and phases.

### Pattern 1: Batch MCP Search (Non-Interactive)

**What:** Loop over confirmed H2 subsections, call Semantic Scholar for each, auto-select top results by relevance. No numbered list, no user selection per query. This contrasts with literature-skill's interactive single-search pattern.

**When to use:** When collecting references for multiple sections automatically.

**Workflow pseudocode:**
```
Pre-flight: Call papers-search-basic({"query": "test", "limit": 1})
  If fails -> skip Step 2.5, insert [CITATION NEEDED] everywhere, continue

FOR each H1 section:
  FOR each H2 subsection under this H1:
    1. Derive query: extract 2-5 key terms from H2 title + description, contextualized by H1 title
    2. Call papers-search-basic({"query": derived_query, "limit": 10})
    3. For each result missing abstract: call get-paper-abstract(paper_id)
    4. Filter results by relevance (Claude assesses claim-density needs)
    5. Keep 5-10 papers based on section needs
    6. Display progress: "check 1.1 Research Background: 8 refs found"
    7. Append to section ref file
  END FOR
END FOR

Display summary table
Wait for user confirmation
```

### Pattern 2: MCP Pre-flight Check (Reuse from literature-skill)

**What:** Before executing batch search, probe MCP with a trivial call.

**Reuse verbatim from literature-skill Step 1:**
```
Call mcp__semantic-scholar__papers-search-basic with {"query": "test", "limit": 1}
- Success -> proceed to batch search
- Failure -> skip Step 2.5 entirely, insert [CITATION NEEDED] in outline, continue to Step 4
```

Key difference from literature-skill: literature-skill **refuses** on MCP failure; repo-to-paper **skips and continues** (graceful degradation, not hard failure).

### Pattern 3: Ref File Write Pattern

**What:** Per-H1-section Markdown files in `.paper-refs/` directory.

**File structure:**
```
{repo_path}/.paper-refs/
  introduction.md      # Refs for H1 "Introduction" section
  methods.md           # Refs for H1 "Methods" section
  results.md           # Refs for H1 "Results" section
  ...
```

**Each file structure:**
```markdown
# Introduction - References

## 1.1 Research Background and Motivation

### Smith et al. (2023)
**Title:** Full paper title from MCP
**Authors:** Smith, J.; Lee, K.
**Year:** 2023 | **Citations:** 142
**Relevance:** [1.1 Research Background and Motivation]
One-sentence explanation of relevance to this subsection.

> Abstract summary (1-2 sentences from MCP data)

```bibtex
@article{smith2023urban, ...}
```

## 1.2 Literature Gap and Contribution

[same card format]
```

### Pattern 4: Citation Key Generation

**Reuse literature-skill's format:** `firstAuthorLastnameLowercaseYYYYfirstKeyword`

Examples: `smith2023urban`, `zhang2022spatiotemporal`, `li2024deep`

### Anti-Patterns to Avoid

- **Skill-calling-Skill:** Do NOT invoke literature-skill as a sub-Skill. Claude Code does not support Skill chaining. Call MCP tools directly within repo-to-paper-skill. (Source: ARCHITECTURE.md Anti-Pattern 1)
- **Putting refs in `references/`:** Do NOT save collected literature to the project's `references/` directory. That directory is for shared, version-controlled reference files. Use `.paper-refs/` in the target repo. (Source: ARCHITECTURE.md Anti-Pattern 2)
- **Interactive selection during batch:** Do NOT present numbered lists for user selection on each H2. The batch pattern auto-selects by relevance. User reviews the complete summary table after all H2s are processed.
- **Filling missing MCP fields from prior knowledge:** Never supplement missing title, authors, abstract, or year from Claude's training data. If a field is missing from MCP response, omit it (for BibTeX) or mark "not available" (for display).

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Paper search | Custom HTTP calls to Semantic Scholar API | `mcp__semantic-scholar__papers-search-basic` | MCP handles auth, rate limiting, response parsing |
| Abstract fetching | Scraping or API calls | `mcp__semantic-scholar__get-paper-abstract` | MCP provides clean abstract text; handles Springer licensing issues |
| BibTeX entry construction | Template strings from scratch | Reuse literature-skill's field mapping pattern | Edge cases: missing DOI, varying publication types (@article/@inproceedings/@misc), author name formatting |
| Citation key generation | Random or sequential IDs | `firstAuthorYYYYkeyword` pattern from literature-skill | Deterministic, human-readable, deduplication-friendly |

**Key insight:** The Semantic Scholar MCP abstracts away all API complexity (authentication, rate limiting, response parsing, paper ID formats). The Skill only needs to formulate queries and format responses.

---

## Common Pitfalls

### Pitfall 1: Rate Limiting on Batch Queries

**What goes wrong:** With 15 H2 subsections, the Skill makes ~15 search calls + ~50-100 abstract fetch calls = 65-115 MCP calls. At 1 RPS authenticated rate, this takes 1-2 minutes minimum. Unauthenticated may be slower or throttled.

**Why it happens:** The standalone literature-skill makes 1-2 calls total; the batch pattern makes 10-100x more.

**How to avoid:**
- The MCP server likely handles rate limiting internally (queuing requests)
- If MCP calls fail mid-batch, catch the error for that specific H2 subsection, mark it as `[CITATION NEEDED]`, and continue with the next H2
- Do not retry failed calls in a loop; move on
- Display progress per H2 so the user sees the Skill is working, not hung

**Warning signs:** MCP calls returning errors after the first few succeed; long pauses between calls.

### Pitfall 2: Query Too Broad or Too Narrow

**What goes wrong:** Using just the H2 title as query (e.g., "Research Background") returns irrelevant results. Using the full H2 description as query returns nothing.

**Why it happens:** H2 titles can be generic; H2 descriptions can be too specific or verbose for keyword search.

**How to avoid:**
- Combine H1 section title (provides domain context) with H2 subsection title (provides specificity)
- Extract 2-5 key technical terms, strip filler words ("and", "of", "the")
- Example: H1="Methods", H2="Gradient Boosting Feature Engineering" -> query: "gradient boosting feature engineering urban"

**Warning signs:** Search returns 0 results for multiple H2s; or returns only tangentially related papers.

### Pitfall 3: Springer Abstracts Missing

**What goes wrong:** Semantic Scholar API does not return abstracts for Springer-published papers due to licensing agreement. The search result has an empty abstract field.

**Why it happens:** Legal restriction on Springer content in Semantic Scholar API.

**How to avoid:**
- Always check if abstract is empty in search results
- Call `mcp__semantic-scholar__get-paper-abstract` for papers with empty abstracts (may still be empty for Springer)
- If abstract remains empty after fetch, display "Abstract not available" in the ref card
- Still include the paper if title and metadata are relevant

**Warning signs:** Multiple papers from the same publisher all show empty abstracts.

### Pitfall 4: Line Budget Overflow

**What goes wrong:** Adding Step 2.5 to a 286-line SKILL.md could push it well past 400 lines.

**Why it happens:** The batch search workflow, ref file format specification, progress display format, and fallback handling are all new content.

**How to avoid:**
- CONTEXT.md explicitly allows exceeding the 300-line convention for functional completeness
- Keep Step 2.5 description concise -- focus on the algorithm, not extensive examples
- Do not duplicate the ref card format in both the Workflow section and Output Contract -- define once, reference once
- The ref card format specification could potentially be moved to a reference file if the Skill grows too large

**Warning signs:** Draft exceeds 400 lines; large blocks of example output inline.

### Pitfall 5: BibTeX Field Hallucination

**What goes wrong:** Claude fills in missing BibTeX fields (volume, pages, DOI) from training data instead of leaving them empty.

**Why it happens:** The anti-hallucination rule is explicit in literature-skill but could be forgotten during batch generation when speed pressure is high.

**How to avoid:**
- State the anti-hallucination rule in Step 2.5: "All BibTeX fields MUST come from MCP-returned data. If a field is not in the MCP response, OMIT it."
- Use `@article` for journal papers, `@inproceedings` for conference, `@misc` for preprints (follow MCP paper type)
- Add `% DOI not available -- verify manually` comment if DOI is missing

**Warning signs:** BibTeX entries with suspiciously complete field sets across all papers.

---

## Code Examples

### Example 1: Pre-flight Check (adapted from literature-skill)

```markdown
### Step 2.5: Literature Collection

**Pre-flight:**
- Call `mcp__semantic-scholar__papers-search-basic` with `{"query": "test", "limit": 1}`
- If call succeeds: proceed to literature collection
- If call fails or tool unavailable: skip Step 2.5 entirely
  - Insert `[CITATION NEEDED]` after each H2 subsection description in the outline
  - Display: "Semantic Scholar MCP not available. Skipping literature collection. [CITATION NEEDED] markers added."
  - Proceed directly to Step 4 (H3 generation)
```

### Example 2: Query Derivation

```
H1: "Introduction"
H2: "1.1 Research Background and Motivation"
H2 description: "Urban heat island measurement requires fine-grained spatial analysis"

Derived query: "urban heat island spatial analysis measurement"
(H1 provides no useful terms; H2 title + description provide domain keywords)
```

```
H1: "Methods"
H2: "3.2 Gradient Boosting Prediction Framework"
H2 description: "Feature engineering using street-level semantic segmentation data"

Derived query: "gradient boosting prediction street-level semantic segmentation"
(Combine H2 title keywords + description keywords; keep to 5 terms)
```

### Example 3: Progress Display

```
Collecting literature references...

✓ 1.1 Research Background and Motivation: 8 refs found
✓ 1.2 Literature Gap and Contribution: 6 refs found
⚠ 2.1 Study Area Description: 0 refs found
✓ 2.2 Data Sources and Preprocessing: 7 refs found
✓ 3.1 Feature Engineering: 9 refs found
✓ 3.2 Gradient Boosting Framework: 5 refs found
...

Literature collection complete: 42 refs across 12 subsections (1 subsection with 0 refs).
```

### Example 4: Summary Table

```
| Section | Subsection | Refs | Top Reference |
|---------|-----------|------|---------------|
| 1. Introduction | 1.1 Research Background | 8 | Smith et al. (2023) - 142 citations |
| 1. Introduction | 1.2 Literature Gap | 6 | Zhang et al. (2022) - 89 citations |
| 2. Study Area | 2.1 Study Area Description | 0 | [CITATION NEEDED] |
| 2. Study Area | 2.2 Data Sources | 7 | Li et al. (2024) - 201 citations |
| ... | ... | ... | ... |

Total: 42 references collected. Confirm to proceed to H3 outline generation.
```

### Example 5: Ref Card in File

```markdown
### Smith et al. (2023)
**Title:** Urban Heat Island Mitigation through Green Infrastructure
**Authors:** Smith, J.; Lee, K.
**Year:** 2023 | **Citations:** 142
**Relevance:** [1.1 Research Background and Motivation]
This study evaluates green roof strategies for reducing UHI intensity, directly supporting the background on spatial analysis approaches.

> This study evaluates green roof and tree-planting strategies for reducing UHI intensity in dense urban cores using remote sensing data.

```bibtex
@article{smith2023urban,
  title     = {Urban Heat Island Mitigation through Green Infrastructure},
  author    = {Smith, John and Lee, Kwang-Ho},
  journal   = {Computers, Environment and Urban Systems},
  year      = {2023},
  volume    = {101},
  pages     = {101943},
  doi       = {10.1016/j.compenvurbsys.2023.101943}
}
```
```

---

## State of the Art

| Old Approach (literature-skill v1.0) | New Approach (repo-to-paper Step 2.5) | Why Changed | Impact |
|--------------------------------------|---------------------------------------|-------------|--------|
| Interactive single-search: user selects from numbered list | Batch non-interactive: auto-select by relevance | User cannot interactively select from 15+ searches; batch pattern needed | No numbered selection UI in Step 2.5 |
| MCP failure = hard refuse | MCP failure = skip + placeholders | Literature is supporting, not primary output; don't block outline generation | Graceful degradation instead of refusal |
| Single ref file per session | Per-H1-section ref files | Body generation (Phase 16) loads refs per-section; keeps context narrow | Multiple files in `.paper-refs/` |
| User-driven topic selection | Auto-derived queries from H2 titles | H2 titles are already user-confirmed; no need to ask again | Zero-question literature collection |

---

## Semantic Scholar MCP Tool Reference

### Available Tools (verified from Phase 9 research + project usage)

| Tool Name | Parameters | Returns | Rate Limit |
|-----------|-----------|---------|------------|
| `papers-search-basic` | `query` (string), `limit` (int, default 100, max 100) | List of papers with paperId, title, authors, year, citationCount, abstract (may be empty) | 1 RPS (authenticated) |
| `get-paper-abstract` | `paper_id` (string -- Semantic Scholar ID) | Abstract text | 10 RPS (authenticated) |
| `search-paper-title` | Title string | Single best-match paper | 1 RPS |
| `papers-batch` | List of paper IDs | Batch paper details | 1 RPS |

### Key API Behaviors

- **Abstract availability:** Not all papers have abstracts in search results. Springer papers specifically lack abstracts due to licensing. Always check and fetch separately if needed.
- **Rate limits:** Authenticated: 1 RPS for search endpoints, 10 RPS for detail endpoints. Unauthenticated: shared pool of 5,000 requests per 5 minutes. Exponential backoff required by API policy.
- **Result quality:** `papers-search-basic` uses relevance ranking. First results are generally most relevant. Limiting to 10 results per query and taking top 5-10 is a reasonable strategy.
- **Paper types:** MCP returns paper type (journal article, conference paper, preprint). Use this to set BibTeX entry type.

---

## Open Questions

1. **Rate limiting behavior at scale**
   - What we know: Authenticated rate is 1 RPS. For 15 H2 subsections with 10 search results each, we need ~15 search calls + up to ~150 abstract calls = ~165 calls, taking ~2-3 minutes minimum.
   - What's unclear: Whether the MCP server handles queuing/throttling transparently or whether Claude Code will see timeout errors.
   - Recommendation: Instruct the Skill to proceed sequentially (no parallel calls assumed), catch per-call failures gracefully, and mark failed H2s as `[CITATION NEEDED]`. The progress display will reassure the user during the wait.

2. **Optimal search result limit per query**
   - What we know: CONTEXT.md says 5-10 refs per H2. API default limit is 100, max 100.
   - What's unclear: Whether requesting `limit: 10` is optimal or if requesting more (e.g., 15-20) and filtering down yields better results.
   - Recommendation: Use `limit: 10` per query. The relevance ranking means the top 10 are already the most relevant. Claude filters down to 5-10 based on claim density assessment. This minimizes API calls and abstract fetches.

3. **Line count after modification**
   - What we know: Current SKILL.md is 286 lines. Step 2.5 needs: pre-flight (5 lines), query derivation (10 lines), batch loop (15 lines), progress display (5 lines), summary table (5 lines), ref file format spec (15 lines), fallback handling (5 lines) = ~60 lines.
   - What's unclear: Whether 346 lines is acceptable or needs reference-file extraction.
   - Recommendation: 346 lines is within acceptable bounds per CONTEXT.md's explicit override of the 300-line convention. If it grows beyond ~380, extract the ref card format to a reference file.

---

## Validation Architecture

### Test Framework

| Property | Value |
|----------|-------|
| Framework | Manual verification (Claude Code Skill project -- no executable test framework) |
| Config file | N/A -- Skills are prompt documents, not executable code |
| Quick run command | Manual: invoke repo-to-paper-skill on a test repo |
| Full suite command | Manual: full guided workflow through all steps |

### Phase Requirements -> Test Map

| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| REPO-04 | At H2 completion, literature refs are collected via Semantic Scholar and saved to `.paper-refs/` | manual | Invoke repo-to-paper-skill, confirm H2, verify `.paper-refs/` files created with correct format | N/A -- Skill project |

### Sampling Rate

- **Per task commit:** Manual review of SKILL.md changes for completeness and correctness
- **Per wave merge:** Invoke the full repo-to-paper-skill workflow on a test repo to verify Step 2.5 integration
- **Phase gate:** Verify: (1) SKILL.md contains Step 2.5 with pre-flight, batch search, progress display, ref file writing; (2) MCP fallback produces `[CITATION NEEDED]` placeholders; (3) ref card format matches CONTEXT.md spec

### Wave 0 Gaps

None -- this phase modifies an existing Skill (SKILL.md). No test infrastructure to create.

---

## Sources

### Primary (HIGH confidence)
- `.claude/skills/literature-skill/SKILL.md` -- MCP pre-flight check pattern, search call pattern, BibTeX generation format, citation key convention
- `.claude/skills/repo-to-paper-skill/SKILL.md` -- Current 4-step workflow structure, line count (286), integration points
- `.planning/phases/15-literature-integration/15-CONTEXT.md` -- All locked decisions, ref file format spec, query strategy, volume targets
- `.planning/research/ARCHITECTURE.md` -- Anti-patterns (Skill-calling-Skill, refs in references/), integration point diagram
- `.planning/research/STACK.md` -- Literature reference file format rationale, Semantic Scholar integration approach
- `.planning/milestones/v1.0-phases/09-literature-support-skills/09-RESEARCH.md` -- Semantic Scholar MCP tool inventory, pre-flight pattern, tool permissions

### Secondary (MEDIUM confidence)
- [Semantic Scholar API docs](https://api.semanticscholar.org/api-docs/graph) -- Endpoint details: `/paper/search` (limit max 100), `/paper/batch` (500 IDs), paper fields including abstract
- [Semantic Scholar API Release Notes](https://github.com/allenai/s2-folks/blob/main/API_RELEASE_NOTES.md) -- Rate limits: 1 RPS search (authenticated), 10 RPS detail, 5000/5min unauthenticated; exponential backoff required; Springer abstract restriction

### Tertiary (LOW confidence)
- [MCP Server implementations](https://mcpservers.org/servers/FujishigeTemma/semantic-scholar-mcp) -- Tool surface varies by MCP server implementation; project uses `semanticscholar` server key but exact implementation not verified in this research

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH -- reuses verified MCP tools from Phase 9; no new dependencies
- Architecture: HIGH -- integration point, ref file format, and workflow all specified in CONTEXT.md decisions
- Pitfalls: MEDIUM -- rate limiting behavior at 100+ calls untested in practice; Springer abstract gap is documented but impact on ref quality unknown
- MCP tool surface: HIGH for `papers-search-basic` and `get-paper-abstract` (verified in literature-skill); MEDIUM for exact parameter behavior at batch scale

**Research date:** 2026-03-18
**Valid until:** 2026-04-17 (stable -- Semantic Scholar API changes slowly; MCP tool surface established in v1.0)
