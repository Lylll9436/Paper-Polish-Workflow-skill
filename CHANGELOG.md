# Changelog

All notable changes to this project will be documented in this file.

## [2.0.0] - 2026-03-19

### Added
- `ppw:repo-to-paper` — scan experiment repo, generate H1/H2/H3 outline with user checkpoints, body text with `[SOURCE: file:line]` annotations
- `ppw:update` — sync latest skills and references from GitHub repo
- Bilingual paragraph-by-paragraph comparison output for 7 Skills (default ON, opt-out with keywords)
- Shared bilingual output spec (`references/bilingual-output.md`)
- Body generation rules reference (`references/body-generation-rules.md`)
- Repo scan patterns reference (`references/repo-patterns.md`)
- Workflow Memory system — Skills record invocations, detect frequent chains, offer direct mode
- Literature integration at H2 stage in repo-to-paper (Semantic Scholar batch search)

### Changed
- **BREAKING:** All skills renamed from `*-skill` to `ppw:*` namespace (e.g., `polish-skill` → `ppw:polish`)
- **BREAKING:** Skill directories renamed from `*-skill/` to `ppw-*/` (e.g., `.claude/skills/polish-skill/` → `.claude/skills/ppw-polish/`)
- All 12 existing Skills now use AskUserQuestion for structured interaction
- skill-conventions.md updated with Workflow Memory section and AskUserQuestion enforcement
- skill-skeleton.md updated with Step 0 template and record-write instruction
- README rewritten with ppw:* naming, 13 skills, updated scenarios

### Removed
- `.planning/` directory removed from git tracking (internal development state)
- `.sisyphus/` directory removed from git tracking
- `.claude/settings.local.json` removed from git tracking

## [1.0.0] - 2025-01-30

### Added
- Initial release as standardized Claude/OpenCode skill
- YAML frontmatter for skill discovery
- CEUS journal template in references/journals/
- Academic expression patterns library
- GitHub Actions CI for skill validation
- Bilingual contributing guide (EN/CN)

### Changed
- Restructured from skill/ to paper-polish-workflow/ directory
- Extracted journal specs to references/ for extensibility
