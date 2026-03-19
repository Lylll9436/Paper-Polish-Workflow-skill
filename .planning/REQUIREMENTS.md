# Requirements: Paper Polish Workflow

**Defined:** 2026-03-19
**Core Value:** Every Skill must produce output that is directly usable in a real paper submission

## v2.1 Requirements

Requirements for Team Agents Orchestration. Each maps to roadmap phases.

### Foundation

- [x] **FNDN-01**: User can invoke `ppw:team` orchestrator Skill with a paper file path and target Skill name
- [x] **FNDN-02**: Orchestrator splits .tex/.md paper into H1/H2 sections as independent files in `.paper-team/sections/`
- [x] **FNDN-03**: Subagent can execute any parallel-eligible ppw:* Skill via dynamic prompt injection (proof-of-concept gate)

### Orchestration

- [ ] **ORCH-01**: User can run a single Skill in parallel across all paper sections simultaneously (one agent per section)
- [ ] **ORCH-02**: Orchestrator collects completed section outputs and merges back into a single paper file
- [ ] **ORCH-03**: Failed agents are detected and reported; user can retry individual failed sections

### Quality

- [ ] **QUAL-01**: Before dispatch, orchestrator extracts a terminology anchor (key terms, abbreviations, style notes) and injects into each agent's prompt
- [ ] **QUAL-02**: After merge, orchestrator automatically runs cross-section consistency review and flags terminology/tone/style issues

### UX

- [ ] **UX-01**: User sees real-time progress display showing each agent's section and completion status
- [ ] **UX-02**: User confirms section split before dispatch and reviews merged output before finalization

## v2.2 Requirements

Deferred to future release. Tracked but not in current roadmap.

### Pipeline

- **PIPE-01**: Multi-Skill pipeline mode (Skill chain like translate→polish→de-ai executed as sequential stages)
- **PIPE-02**: Stage-gate intervention between pipeline stages (inspect intermediate results, adjust parameters)
- **PIPE-03**: Smart section grouping (merge short sections for agent efficiency)
- **PIPE-04**: Cost estimation displayed before dispatch (estimated tokens and agents)

## Out of Scope

Explicitly excluded. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Agent Teams (experimental API) | Unstable, Windows limitations, unnecessary for independent section workers |
| Paragraph-level splitting | Too fine-grained; consistency cost outweighs speed gain |
| Non-eligible Skills parallel (logic, reviewer, abstract, etc.) | Require full-paper context; not section-splittable |
| Automatic fix of consistency issues | Flagging only; auto-fix risks introducing new errors |
| Real-time inter-agent coordination | Overkill; independent workers + post-merge review is sufficient |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| FNDN-01 | Phase 19 | Complete |
| FNDN-02 | Phase 19 | Complete |
| FNDN-03 | Phase 19 | Complete |
| ORCH-01 | Phase 20 | Pending |
| ORCH-02 | Phase 20 | Pending |
| ORCH-03 | Phase 20 | Pending |
| QUAL-01 | Phase 21 | Pending |
| QUAL-02 | Phase 21 | Pending |
| UX-01 | Phase 20 | Pending |
| UX-02 | Phase 20 | Pending |

**Coverage:**
- v2.1 requirements: 10 total
- Mapped to phases: 10
- Unmapped: 0

---
*Requirements defined: 2026-03-19*
*Last updated: 2026-03-19 after roadmap creation*
