# Feature Landscape

**Domain:** AI-Assisted Academic Paper Writing Tool Suite (Claude Code Skills)
**Target User:** Single researcher in geography/urban science, Chinese-native, publishing in English journals
**Researched:** 2026-03-10
**Overall Confidence:** HIGH (grounded in reference project, competitor analysis, and domain research)

## Table Stakes

Features users expect from an academic writing AI tool suite. Missing any of these = the tool feels like a toy prompt collection, not a professional workflow.

### TS-1: Chinese-to-English Academic Translation

| Aspect | Detail |
|--------|--------|
| **Why Expected** | Primary entry point for Chinese researchers. Every single paper starts as a Chinese draft or outline that needs to become English prose. |
| **Complexity** | Medium |
| **What "Good" Looks Like** | Preserves LaTeX commands, mathematical notation, and domain-specific GIS/urban terminology. Output reads like native academic English, not Google Translate. Handles paragraph-level context, not sentence-by-sentence. |
| **Domain-Specific Needs** | Geography terms (e.g., spatial autocorrelation, built environment, land use), GIS software names (ArcGIS, QGIS, GeoDa), urban planning concepts (walkability, TOD, urban morphology) must be translated with field-standard terminology. |
| **Reference** | awesome-ai-research-writing "Chinese-to-English" prompt focuses on naturalness + academic register. DeepL and Wordvice AI are the SaaS benchmarks. This Skill must surpass them by understanding paper context (section type, journal target). |
| **Key Differentiation from SaaS** | SaaS translators are stateless -- they translate what you paste. This Skill reads the full paper context, knows the target journal, and can reference expression patterns. |

### TS-2: English Paper Polishing (Structure -> Logic -> Expression)

| Aspect | Detail |
|--------|--------|
| **Why Expected** | Core value proposition. The polishing workflow is the most-used feature in any academic writing assistant. |
| **Complexity** | High |
| **What "Good" Looks Like** | Top-down approach: macro structure first, per-sentence logic second, expression choices third. Interactive, not auto-replace. User retains control at every step. Batch processing of 3-5 sentences via AskUserQuestion for efficiency. |
| **Current State** | Existing SKILL.md implements a 6-step workflow (structure -> logic -> expression -> reference -> coherence -> write). The user considers this "a shell that needs to be redone." Key complaint: too rigid, lacks flexibility for different section types. |
| **Redesign Requirements** | (1) Flexible entry points -- user may want to polish just expression, not re-examine structure. (2) Section-aware behavior -- Abstract polishing differs from Discussion polishing. (3) Word budget awareness -- know the target count and manage it actively. (4) Journal style enforcement -- apply journal checklist automatically. |
| **Reference** | Paperpal provides 2x more edits than Grammarly Academic mode by training on academic edit pairs. Writefull's "Sentence Palette" and "Academizer" are strong UX references. |

### TS-3: Journal-Specific Formatting and Style

| Aspect | Detail |
|--------|--------|
| **Why Expected** | Every journal has distinct requirements (word limits, abstract format, highlights, data availability statements). Getting these wrong = desk rejection. |
| **Complexity** | Low (template-based) |
| **What "Good" Looks Like** | Structured template files in `references/journals/` that Skills can load. Each template includes: word budget breakdown, required sections, formatting rules, style checklist, highlight format (if applicable), and example patterns from published papers. |
| **Priority Journals** | CEUS (exists), IJGIS, Cities, Landscape and Urban Planning, Environment and Planning B, Applied Geography. CEUS first, then add 2-3 more to validate the template pattern. |
| **Key Data per Template** | Total word limit, abstract word limit, required sections, highlight format (count, char limit), data availability statement requirement, review type (single/double blind), and field-specific style notes. |

### TS-4: Expression Pattern Reference Library

| Aspect | Detail |
|--------|--------|
| **Why Expected** | Academic writing has established phrasal conventions. Researchers (especially non-native English speakers) need a library of professional patterns for sentence starters, transitions, hedging, and results presentation. |
| **Complexity** | Low |
| **Current State** | `references/expression-patterns.md` exists with patterns for background, gaps, methods, transitions, hedging, results, and conclusions. Needs expansion with geography/urban-science-specific patterns. |
| **What to Add** | (1) Geography-specific patterns (spatial distribution, urban morphology, built environment). (2) GIS methodology patterns (geoprocessing, spatial analysis, raster/vector). (3) Data description patterns (remote sensing, survey data, administrative data). (4) Limitation patterns common in geography papers. |

### TS-5: Figure and Table Caption Generation

| Aspect | Detail |
|--------|--------|
| **Why Expected** | Every paper has 5-15 figures and tables. Over 50% of figure captions in published papers are poorly crafted (arXiv:2509.25817). Captions follow strict conventions that vary by section and figure type. |
| **Complexity** | Medium |
| **What "Good" Looks Like** | User describes a figure (or provides a file reference). Skill generates a caption following conventions: (1) Figure captions: what the figure shows, key takeaway, data source. (2) Table captions: what the table contains, how to read it. (3) Map figure captions: study area, data source, coordinate system, legend explanation. |
| **Domain-Specific** | Geography papers have map figures (study area maps, spatial distribution maps, heatmaps) that require specific caption elements: geographic extent, projection, data source, time period. |
| **Research** | SciCapenter (ACM CHI) supports caption composition with AI-generated drafts. Multi-LLM collaborative caption generation (Kim et al. 2025) shows ensemble approaches improve quality. |

### TS-6: Abstract Generation/Optimization

| Aspect | Detail |
|--------|--------|
| **Why Expected** | The abstract is the single most-read section. Strict word limits (150-300 words depending on journal). Reviewers and editors read abstracts first -- a weak abstract can lead to desk rejection or poor reviewer assignment. |
| **Complexity** | Medium |
| **What "Good" Looks Like** | Follows the 5-sentence Farquhar formula: (1) What you did, (2) Why it is hard/important, (3) How you did it (with keywords), (4) What evidence you have, (5) Most impressive result/number. Enforces word limit. Avoids generic openings ("X has attracted increasing attention..."). |
| **Journal Variation** | CEUS: <=250 words. IJGIS: <=250 words. Cities: <=200 words. Must be parameterized per journal template. |

### TS-7: Interactive User Control (Not Auto-Replace)

| Aspect | Detail |
|--------|--------|
| **Why Expected** | Researchers need to maintain ownership of their writing. Auto-replacement destroys voice and may introduce errors. The difference between a useful tool and an annoying one is whether the researcher controls each change. |
| **Complexity** | Low (native to Claude Code via AskUserQuestion) |
| **What "Good" Looks Like** | Every substantive change presents 2-4 options via AskUserQuestion. User can always type a custom alternative. Batch 3-5 sentences per interaction for efficiency. Never silently rewrite without confirmation. |

### TS-8: File I/O Integration

| Aspect | Detail |
|--------|--------|
| **Why Expected** | Users work with files (.tex, .md, .docx concepts), not clipboard paste. Read paper sections from files, write polished output back to files. |
| **Complexity** | Low (native Claude Code capability) |
| **What "Good" Looks Like** | Read source files, identify section boundaries, write polished output to `*_polished.md` or directly edit source. Preserve LaTeX formatting, citation keys, cross-references. |

---

## Differentiators

Features that distinguish this tool suite from (a) copy-paste prompt collections like awesome-ai-research-writing, (b) SaaS tools like Paperpal/Grammarly, and (c) generic ChatGPT usage.

### D-1: De-AI Detection and Rewriting

| Aspect | Detail |
|--------|--------|
| **Value Proposition** | In 2026, publishers and journals actively screen for AI-generated text. GPTZero, Turnitin, and Copyleaks flag even lightly edited AI drafts. A paper flagged as AI-generated faces immediate credibility damage. This Skill rewrites human-authored text that was polished by AI to restore natural human writing patterns. |
| **Complexity** | High |
| **How It Works** | AI detectors rely on two metrics: (1) low perplexity (predictable word choices), (2) low burstiness (uniform sentence structure). De-AI rewriting must increase perplexity (varied vocabulary, less predictable phrasing) and burstiness (mix short and long sentences, vary complexity). |
| **Implementation** | Curated anti-AI pattern library: (1) Common AI tells (overuse of "delve into", "it is worth noting", "significant", "robust"), (2) Structural tells (every paragraph opening with a topic sentence, formulaic transitions), (3) Rhythm tells (uniformly mid-length sentences). Rewriting rules that inject natural variation. |
| **Why Not SaaS** | Undetectable AI and similar humanizer tools do generic synonym replacement. This Skill understands academic register -- it must stay academic while adding natural variation. It knows that a geography paper about urban morphology should use field-specific vocabulary, not generic alternatives. |
| **Ethical Framing** | This is for researchers who write their own papers and use AI for polishing, not for generating papers from scratch. The goal is ensuring legitimately human-authored work isn't falsely flagged. |

### D-2: Reviewer-Perspective Paper Review

| Aspect | Detail |
|--------|--------|
| **Value Proposition** | Self-review before submission catches issues that lead to rejection. Simulates what a senior reviewer would critique. Costs nothing compared to rejection + 6-month resubmission cycle. |
| **Complexity** | Medium-High |
| **How It Works** | Reads full paper (or key sections). Evaluates against criteria real reviewers use: (1) Novelty -- is the contribution clear and new? (2) Methodology rigor -- is the approach sound? (3) Experimental design -- are baselines appropriate? (4) Clarity -- is the writing clear? (5) Significance -- does this matter? Outputs a structured review with scores and actionable feedback. |
| **Domain-Specific** | Geography/urban science reviewers care about: (a) geospatial perspective -- does the paper leverage geography, not just apply ML to spatial data? (b) policy implications -- so what for planners/policymakers? (c) reproducibility -- is the study area described, data accessible? (d) cross-city generalizability -- tested in one city or many? |
| **Reference** | CSPaper.org provides venue-specific AI reviews with official rubrics. Stanford's Agentic Reviewer (paperreview.ai) gives free reviews. Google's PAT (Paper Assistant Tool) was piloted at ICML 2026 with 94% satisfaction. The awesome-ai-research-writing reviewer prompt defaults to rejection mindset unless highlights are convincing. |
| **Key Design Choice** | Default to a critical stance (reject-unless-convinced), not a cheerful one. Researchers need honest critique, not encouragement. |

### D-3: Cross-Section Logic Verification

| Aspect | Detail |
|--------|--------|
| **Value Proposition** | A surprisingly common rejection reason: numbers in Abstract don't match Results, claims in Introduction aren't supported in Experiments, terminology is inconsistent across sections. This catches cross-section inconsistencies that manual proofreading misses. |
| **Complexity** | Medium |
| **How It Works** | (1) Extract key claims, numbers, and terms from each section. (2) Cross-reference: Abstract claims vs. Conclusion claims, Introduction goals vs. Results delivered, Methodology descriptions vs. Experiment execution, Terminology consistency (e.g., "SVI" vs "street view imagery" used interchangeably). (3) Report discrepancies with specific locations. |
| **Domain-Specific** | Geography papers often have: city counts that must match across sections, data time ranges that must be consistent, model accuracy numbers that must be exact, spatial resolution descriptions that must agree. |

### D-4: Literature Search via Semantic Scholar MCP

| Aspect | Detail |
|--------|--------|
| **Value Proposition** | Programmatic, verified citation search within the writing workflow. No tab-switching to Google Scholar. Citations are real (from Semantic Scholar's database of 200M+ papers), not hallucinated. |
| **Complexity** | Medium |
| **How It Works** | Uses `mcp__semantic-scholar__papers-search-basic` to search by topic, retrieve paper details (title, authors, year, abstract, citation count), and generate BibTeX entries. Can find related work for a given paper, trace citation networks, and identify highly-cited references for a topic. |
| **Key Differentiator** | Unlike ChatGPT/Claude vanilla, which hallucinate ~40% of citations, this provides verified citations from an authoritative database. Every citation includes a Semantic Scholar URL for manual verification. |
| **External Dependency** | Requires Semantic Scholar MCP server to be configured. Multiple implementations exist (JackKuo666, FujishigeTemma, Benhao Tang, AIRA-SemanticScholar). API key recommended for higher rate limits. |
| **Integration** | Should output BibTeX that can be directly added to `.bib` files. Should flag when a search returns few results (suggesting the topic may need different search terms). |

### D-5: Experiment Result Analysis and Discussion Generation

| Aspect | Detail |
|--------|--------|
| **Value Proposition** | Turning results tables into narrative discussion is one of the most time-consuming writing tasks. This Skill reads experiment results and generates structured analysis paragraphs. |
| **Complexity** | High |
| **How It Works** | (1) Read results table (from file or user input). (2) Identify: best performer, worst performer, improvements over baselines, statistical significance indicators. (3) Generate discussion paragraphs: what worked, why (hypothesis), comparison to prior work, limitations, and implications. |
| **Domain-Specific** | Geography/urban science experiments often involve: (a) multi-city comparison results, (b) ablation studies on spatial features, (c) comparison across different urban typologies, (d) scale-dependent results (neighborhood vs city-level). Discussion must connect results to planning/policy implications. |
| **Risk** | Generating discussion from results without understanding the actual methodology is dangerous -- the Skill must ask the user about causal hypotheses, not fabricate them. |

### D-6: Visualization Recommendation

| Aspect | Detail |
|--------|--------|
| **Value Proposition** | Recommends the right chart type for experimental results based on data characteristics. Saves researchers from defaulting to bar charts for everything. |
| **Complexity** | Low-Medium |
| **How It Works** | Guidance-based, not image generation. User describes data (e.g., "comparison of model accuracy across 8 cities"). Skill recommends chart type (grouped bar chart, heatmap, radar chart, etc.), layout, color scheme, and provides example code snippets (matplotlib/seaborn/plotly). |
| **Domain-Specific** | Geography papers need: (a) choropleth maps for spatial distribution, (b) scatter plots with spatial lag for autocorrelation, (c) multi-panel layouts for multi-city comparison, (d) feature importance visualizations for ML models. Should reference tools like GeoPandas, folium, kepler.gl alongside matplotlib/seaborn. |

### D-7: Cover Letter Generation

| Aspect | Detail |
|--------|--------|
| **Value Proposition** | A weak cover letter can lead to desk rejection. Most researchers hate writing them. Template-driven generation with paper-specific content filling. |
| **Complexity** | Low |
| **How It Works** | Reads paper abstract and key contributions. Fills a journal-specific cover letter template with: (1) Editor salutation, (2) Submission declaration, (3) Research overview (2-3 sentences), (4) Why this journal (scope match), (5) Key contributions (3 bullets), (6) Suggested reviewers (optional). |
| **Template Source** | Taylor & Francis, AJE, and Oreate AI all provide standard templates. The Skill should include a base template per journal. |

### D-8: Bilingual Interaction (EN/CN Triggers and Instructions)

| Aspect | Detail |
|--------|--------|
| **Value Proposition** | Chinese researchers think and discuss in Chinese but write in English. The tool must accept Chinese instructions ("帮我润色这段") and produce English output. This removes the cognitive overhead of switching languages mid-workflow. |
| **Complexity** | Low |
| **How It Works** | Skill YAML frontmatter includes both English and Chinese trigger keywords. Skill instructions note that user may interact in either language. Output language follows the task (translation outputs English, polishing outputs in the language being polished). |

---

## Anti-Features

Things to deliberately NOT build. These are scope traps that would waste effort or cause harm.

### AF-1: Automatic Paper Generation from Scratch

| Why Avoid | What to Do Instead |
|-----------|-------------------|
| Crosses ethical lines. Conferences (ICLR 2026, CVPR 2025) now require LLM use disclosure. A tool that generates papers would expose users to academic integrity violations. | All Skills take existing human-written content as input. The tool assists and improves, never originates. |

### AF-2: Web UI or Standalone Application

| Why Avoid | What to Do Instead |
|-----------|-------------------|
| PROJECT.md explicitly scopes to Claude Code Skills. Building a UI multiplies complexity 10x for no benefit -- the target user already has Claude Code. | Keep as CLI-interactive Skills in `.claude/skills/`. |

### AF-3: Citation Fabrication

| Why Avoid | What to Do Instead |
|-----------|-------------------|
| AI-generated citations have ~40% error rate. A single fabricated citation discovered by reviewers = credibility destruction + likely rejection. | Use Semantic Scholar MCP for verified citations. Any citation not verified via Semantic Scholar must be flagged as `[CITATION NEEDED]` or `\cite{PLACEHOLDER_author2024_verify}`. |

### AF-4: Grammar-Only Checking

| Why Avoid | What to Do Instead |
|-----------|-------------------|
| Grammarly, Paperpal, and Writefull already do this far better than a prompt-based Skill can. Competing on grammar checking is a losing battle. | Focus on structure, logic, and academic register -- things that require full-paper context and domain knowledge. Grammar issues are incidentally caught during polishing, but are not the primary goal. |

### AF-5: Multi-Paper Batch Processing

| Why Avoid | What to Do Instead |
|-----------|-------------------|
| Overengineering. A single researcher works on one paper at a time. Batch processing adds complexity (error handling, progress tracking) for a scenario that almost never occurs. | Single-paper, single-section workflows. If a researcher has multiple papers, they invoke the Skill multiple times. |

### AF-6: Automated LaTeX Compilation

| Why Avoid | What to Do Instead |
|-----------|-------------------|
| Users have their own LaTeX toolchain (Overleaf, local TeX Live, etc.). Compilation involves complex dependency management (style files, bibliography). | Output polished LaTeX source code. User compiles in their own environment. |

### AF-7: Non-Academic Writing Support

| Why Avoid | What to Do Instead |
|-----------|-------------------|
| Dilutes focus. Academic and non-academic writing have fundamentally different conventions (hedging language, passive voice acceptability, citation requirements). Supporting both makes each worse. | Explicitly scope to research papers. Blog posts, grant applications, and presentations are out of scope (grant apps could be a future extension but not now). |

### AF-8: Image/Figure Generation

| Why Avoid | What to Do Instead |
|-----------|-------------------|
| Claude Code cannot generate images. Tools like Figgen exist for this. Attempting to generate figures within a text-based Skill is pointless. | Provide visualization *recommendations* (chart type, layout, code snippets) and *caption generation*. Leave actual figure creation to matplotlib/Python or dedicated tools. |

### AF-9: Plagiarism Checking

| Why Avoid | What to Do Instead |
|-----------|-------------------|
| Requires database access (Turnitin has 16B+ pages, Paperpal has 99B pages). A Claude Code Skill cannot replicate this. False confidence from a naive similarity check is worse than no check at all. | Recommend users run their own plagiarism check via institutional tools (Turnitin, iThenticate) or Paperpal before submission. |

---

## Feature Dependencies

```
Shared References (must exist first):
  references/expression-patterns.md ──────┐
  references/journals/{journal}.md ────────┤
  references/anti-ai-patterns.md ──────────┤ (new, needed for De-AI)
                                           │
Skills (all independent, depend only on    │
shared references, never on each other):   │
                                           │
  ┌── Paper Polish Skill ◄─────────────────┤ (expression-patterns + journal template)
  │                                        │
  ├── Paper Translate Skill ◄──────────────┤ (expression-patterns + journal template)
  │                                        │
  ├── De-AI Skill ◄────────────────────────┤ (anti-ai-patterns + expression-patterns)
  │                                        │
  ├── Abstract Skill ◄────────────────────┤ (expression-patterns + journal template)
  │                                        │
  ├── Reviewer Skill ◄────────────────────┤ (journal template for rubric)
  │                                        │
  ├── Logic Verification Skill ◄───────────┘ (no specific reference dependency)
  │
  ├── Caption Skill                        (no reference dependency)
  │
  ├── Experiment Analysis Skill            (no reference dependency)
  │
  ├── Cover Letter Skill ◄──── journal template (for editor name, scope description)
  │
  ├── Visualization Skill                  (no reference dependency)
  │
  └── Lit Search Skill ◄────── Semantic Scholar MCP (external dependency)
```

**Critical Path:** Expression patterns and journal templates must exist before most Skills can work effectively. These are the foundation layer.

**No Inter-Skill Dependencies:** Each Skill is invoked independently. A user can translate without polishing, or review without de-AI-ing. This is by design -- modular over monolithic.

---

## Feature Priority Matrix

### Impact vs. Complexity

```
                    HIGH IMPACT
                        │
     D-2 Reviewer ──────┼────── TS-2 Polish (redesign)
     D-1 De-AI ─────────┤
     D-5 Experiment ─────┤
                        │
     D-3 Logic ─────────┼────── TS-1 Translate
     D-4 Lit Search ────┤
                        │
     TS-6 Abstract ─────┼────── TS-5 Captions
     D-7 Cover Letter ──┤
     D-6 Visualization ─┤
                        │
                   LOW IMPACT
     ────────────────────┼─────────────────
         LOW COMPLEXITY      HIGH COMPLEXITY
```

---

## MVP Recommendation

Prioritize for first usable release (a researcher can actually use this to prepare a real paper):

### Phase 1: Foundation + Core Workflow
1. **Expression patterns reference** (Low complexity, blocks 5+ Skills, partially exists)
2. **CEUS journal template** (Low complexity, blocks 6+ Skills, already exists but needs validation)
3. **Paper Polish Skill** (High complexity, core value -- this is what users invoke first and most)
4. **Paper Translate Skill** (Medium complexity, the other primary entry point for Chinese researchers)

### Phase 2: Quality Assurance
5. **De-AI Skill + anti-AI patterns reference** (High value differentiator in 2026 where detection is aggressive)
6. **Reviewer Skill** (Medium complexity, high impact on submission success)
7. **Logic Verification Skill** (Medium complexity, catches cross-section errors)

### Phase 3: Completeness
8. **Abstract/Cover Letter Skill** (Low complexity, completes the submission workflow)
9. **Figure/Table Caption Skill** (Medium complexity, saves time on a repetitive task)
10. **Experiment Analysis Skill** (High complexity, but transformative for discussion writing)

### Phase 4: Nice-to-Haves
11. **Literature Search Skill** (depends on Semantic Scholar MCP setup, which varies by user)
12. **Visualization Recommendation Skill** (guidance-based, low urgency)
13. **Additional journal templates** (IJGIS, Cities, etc. -- validate the CEUS pattern first)

### Rationale for This Ordering

- **Phase 1 first** because a researcher needs translate + polish to produce any usable output. Without these, nothing else matters.
- **Phase 2 second** because quality assurance (de-AI, review, logic check) is what prevents rejection -- the difference between a tool and a submission workflow.
- **Phase 3 third** because these complete the paper (abstract, captions, discussion) but can be done manually in the interim.
- **Phase 4 last** because these are genuinely optional -- lit search requires setup, visualization is guidance-only, and extra templates can be added incrementally.

---

## Geography/Urban Science Feature Priorities

For a researcher specifically in geography/urban science, the following features have disproportionate impact:

| Feature | Why Especially Important |
|---------|------------------------|
| **Translate** | Chinese geography researchers publish heavily in English journals (CEUS, IJGIS, Cities). Translation quality with GIS/urban terminology directly affects acceptance. |
| **Reviewer Simulation** | Geography reviewers specifically look for: geospatial perspective (not just ML on spatial data), policy implications, reproducibility, multi-city generalizability. A generic reviewer prompt misses these. |
| **Caption Generation** | Map figures are unique to geography. Captions must specify: study area extent, projection/CRS, data source, temporal coverage, legend explanation. Generic caption tools don't know this. |
| **Visualization Recommendation** | Geography has unique visualization needs: choropleth maps, spatial autocorrelation plots, multi-city comparison panels, feature importance for spatial ML. The recommendation engine must know GeoPandas/folium, not just matplotlib. |
| **Logic Verification** | Geography papers with multi-city experiments have many numbers (city count, sample size per city, time ranges, accuracy per city) that must be perfectly consistent across sections. |

---

## Sources

### HIGH Confidence
- [PROJECT.md](../../PROJECT.md) -- Primary source of truth for requirements and constraints
- [Existing SKILL.md](../../paper-polish-workflow/SKILL.md) -- Current implementation to redesign
- [awesome-ai-research-writing](https://github.com/Leey21/awesome-ai-research-writing) -- Reference project with 17 prompt categories
- [Skill authoring best practices (Anthropic)](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) -- Informs Skill design patterns
- [ICML 2026 PAT announcement](https://blog.icml.cc/2026/01/14/icml-experimental-program-using-googles-paper-assistant-tool-pat/) -- Google's Paper Assistant Tool with 94% satisfaction

### MEDIUM Confidence
- [Paperpal vs Grammarly comparison](https://paperpal.com/blog/news-updates/an-analysis-of-how-paperpal-outperforms-other-ai-writing-tools) -- Feature landscape of SaaS competitors
- [CSPaper.org](https://cspaper.org/) -- Venue-specific AI reviews with official rubrics
- [Stanford Agentic Reviewer](https://paperreview.ai/) -- Free AI paper review
- [Semantic Scholar MCP servers](https://github.com/JackKuo666/semanticscholar-MCP-Server) -- Multiple implementations available
- [Personalized figure caption generation (arXiv:2509.25817)](https://arxiv.org/abs/2509.25817) -- 50%+ of captions poorly crafted
- [Understanding AI-generated captions (arXiv:2501.06317)](https://arxiv.org/abs/2501.06317) -- Authors copy-refine AI captions
- [AJE cover letter guide](https://blog.aje.com/en/writing-cover-letter) -- Standard cover letter template

### LOW Confidence (needs validation)
- De-AI detection bypass strategies -- primarily from commercial blog posts, not peer-reviewed research. The core metrics (burstiness, perplexity) are well-established, but specific bypass techniques evolve rapidly.
- GPTZero detection rates -- claims of 45% reduction via paraphrasing come from a single 2025 study, not widely replicated.
