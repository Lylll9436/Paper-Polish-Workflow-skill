# Anti-AI Patterns

A modular reference library for detecting and rewriting AI-sounding academic phrasing.

This overview file should remain the stable anti-AI entrypoint so future Skills can load it first and then choose one pattern category when needed.

---

## How to Use This Library

- Read this file when you need the risk model, category map, or a quick retrieval contract.
- Load one leaf file when the rewrite target is narrow, such as vocabulary inflation or transition overuse.
- Default retrieval format for downstream Skills is `Problem expression -> Replacement`.
- Use the risk tier to decide how aggressively to rewrite.

## Risk Tiers

| Tier | Meaning | Suggested use |
|------|---------|---------------|
| High Risk | Strong AI trace or promotional phrasing | Rewrite by default unless domain language truly requires it |
| Medium Risk | Common smoothing pattern that may sound over-produced | Rewrite when tone feels too polished or repetitive |
| Optional | Pattern is not necessarily wrong, but can sound generic if overused | Rewrite selectively based on surrounding context |

## Module Map

- `references/anti-ai-patterns/vocabulary.md`
- `references/anti-ai-patterns/sentence-patterns.md`
- `references/anti-ai-patterns/transitions-and-tone.md`

## Lightweight Retrieval Layer

| Category | Problem expression | Replacement |
|----------|--------------------|-------------|
| Vocabulary inflation | `groundbreaking` | `useful in practice` |
| Sentence overclaim | `This proves that ...` | `This suggests that ...` |
| Transition over-smoothing | `Moreover, it is worth noting that ...` | `Additionally, ...` or direct statement |

## Stable Heading Contract

Each leaf file should keep these headings:

- `## High Risk`
- `## Medium Risk`
- `## Optional`

## Maintenance Rules

- Keep each entry short enough for direct reuse inside a Skill.
- Prefer replacements that preserve formal academic tone.
- Add rationale only when it helps a later Skill explain the rewrite choice.
- Keep category filenames stable once downstream Skills begin referencing them.

---

*Entry point: references/anti-ai-patterns.md*
*Updated for modular loading: 2026-03-11*
