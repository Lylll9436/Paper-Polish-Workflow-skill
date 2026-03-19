# Roadmap: Paper Polish Workflow

## Milestones

- ✅ **v1.0 Paper Polish Workflow** — Phases 1-10 (shipped 2026-03-13)
- ✅ **v2.0 Repo-to-Paper & Bilingual Enhancement** — Phases 11-18 (shipped 2026-03-18)
- 🚧 **v2.1 Team Agents Orchestration** — Phases 19-21 (in progress)

## Phases

<details>
<summary>✅ v1.0 Paper Polish Workflow (Phases 1-10) — SHIPPED 2026-03-13</summary>

- [x] Phase 1: Reference Libraries (2/2 plans) — completed 2026-03-11
- [x] Phase 2: Skill Conventions (1/1 plan) — completed 2026-03-11
- [x] Phase 3: Translation Skill (1/1 plan) — completed 2026-03-12
- [x] Phase 4: Polish Skill (1/1 plan) — completed 2026-03-12
- [x] Phase 5: De-AI Skill (1/1 plan) — completed 2026-03-12
- [x] Phase 6: Reviewer Simulation Skill (1/1 plan) — completed 2026-03-12
- [x] Phase 7: Abstract and Experiment Skills (2/2 plans) — completed 2026-03-12
- [x] Phase 8: Figure/Table and Logic Skills (2/2 plans) — completed 2026-03-12
- [x] Phase 9: Literature and Support Skills (3/3 plans) — completed 2026-03-12
- [x] Phase 10: Documentation (1/1 plan) — completed 2026-03-12

Full details: `.planning/milestones/v1.0-ROADMAP.md`

</details>

<details>
<summary>✅ v2.0 Repo-to-Paper & Bilingual Enhancement (Phases 11-18) — SHIPPED 2026-03-18</summary>

- [x] Phase 11: Convention & Tech Debt (1/1 plan) — completed 2026-03-17
- [x] Phase 12: AskUserQuestion Fix (1/1 plan) — completed 2026-03-17
- [x] Phase 13: Bilingual Pattern Standardization (1/1 plan) — completed 2026-03-17
- [x] Phase 14: Repo-to-Paper Core Structure (1/1 plan) — completed 2026-03-18
- [x] Phase 15: Literature Integration (1/1 plan) — completed 2026-03-18
- [x] Phase 16: Body Generation & Bilingual Output (1/1 plan) — completed 2026-03-18
- [x] Phase 17: Existing Skills Bilingual Update (1/1 plan) — completed 2026-03-18
- [x] Phase 18: Workflow Memory (2/2 plans) — completed 2026-03-18

Full details: `.planning/milestones/v2.0-ROADMAP.md`

</details>

### 🚧 v2.1 Team Agents Orchestration (In Progress)

**Milestone Goal:** Add a universal team agents orchestration layer that parallelizes any eligible Skill across paper sections, with observable progress and automatic consistency review.

- [ ] **Phase 19: Foundation and Proof-of-Concept Gate** - Section splitter, subagent Skill injection validation, orchestrator entry point
- [ ] **Phase 20: Parallel Orchestration Engine** - Full dispatch-collect-merge cycle with progress display, error handling, and user confirmation flow
- [ ] **Phase 21: Quality Assurance Layer** - Terminology anchor extraction before dispatch and post-merge cross-section consistency review

## Phase Details

### Phase 19: Foundation and Proof-of-Concept Gate
**Goal**: The orchestrator Skill exists, can decompose a paper into sections, and has validated that a subagent can successfully execute injected Skill instructions on a single section
**Depends on**: Phase 18 (v2.0 complete)
**Requirements**: FNDN-01, FNDN-02, FNDN-03
**Success Criteria** (what must be TRUE):
  1. User can invoke `ppw:team` with a paper file path and a target Skill name (e.g., `ppw:polish`) and the orchestrator launches without error
  2. Orchestrator splits a .tex or .md paper into individual H1/H2 section files in `.paper-team/sections/`, excluding preamble and bibliography, and presents the section list for user confirmation
  3. A single subagent processes one section using dynamically injected Skill instructions and produces output comparable to main-session execution (proof-of-concept gate passed)
  4. The `.paper-team/` directory structure (sections/, output/, manifest.json, backup of original) is created and populated correctly
**Plans**: 2 plans

Plans:
- [ ] 19-01-PLAN.md — Create ppw:team orchestrator SKILL.md with section splitting and PoC gate logic
- [ ] 19-02-PLAN.md — End-to-end PoC validation (human-verify checkpoint)

### Phase 20: Parallel Orchestration Engine
**Goal**: Users can run a single Skill in parallel across all paper sections with full dispatch-collect-merge cycle, real-time progress, error resilience, and user control at key decision points
**Depends on**: Phase 19
**Requirements**: ORCH-01, ORCH-02, ORCH-03, UX-01, UX-02
**Success Criteria** (what must be TRUE):
  1. User sees all paper sections processed in parallel (one agent per section) when running a single eligible Skill via `ppw:team`, with non-eligible Skills refused with a clear error message
  2. Completed section outputs are merged back into a single paper file preserving original section order, LaTeX preamble, and document footer
  3. If an agent fails on a section, the failure is reported with the section name and error reason, the original section content is preserved, and user can retry that specific section without re-running the entire job
  4. User sees real-time progress display showing each section's agent status (queued, in-progress, done, failed) throughout the dispatch cycle
  5. User confirms the section split before any agents are dispatched, and reviews the merged output before it is written as the final result
**Plans**: TBD

Plans:
- [ ] 20-01: TBD
- [ ] 20-02: TBD

### Phase 21: Quality Assurance Layer
**Goal**: Parallel-processed papers maintain terminology and style consistency through pre-dispatch anchoring and post-merge review
**Depends on**: Phase 20
**Requirements**: QUAL-01, QUAL-02
**Success Criteria** (what must be TRUE):
  1. Before dispatch, the orchestrator extracts key terms, abbreviations, and style notes from the original paper into a terminology anchor and injects it into every agent's prompt
  2. After merge, the orchestrator automatically runs a cross-section consistency review that flags specific terminology variants, tone shifts, tense inconsistencies, and abbreviation usage issues with locations and suggested fixes
  3. User receives a structured consistency report and can decide which flagged issues to address before finalizing the output
**Plans**: TBD

Plans:
- [ ] 21-01: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 19 → 20 → 21

| Phase | Milestone | Plans Complete | Status | Completed |
|-------|-----------|----------------|--------|-----------|
| 19. Foundation and Proof-of-Concept Gate | v2.1 | 0/2 | Not started | - |
| 20. Parallel Orchestration Engine | v2.1 | 0/2 | Not started | - |
| 21. Quality Assurance Layer | v2.1 | 0/1 | Not started | - |
