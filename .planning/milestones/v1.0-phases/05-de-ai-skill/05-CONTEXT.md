# Phase 5: De-AI Skill - Context

**Gathered:** 2026-03-11
**Status:** Ready for planning

<domain>
## Phase Boundary

Build a De-AI Skill that detects AI-generated patterns in English academic text and rewrites flagged passages to reduce detection scores while maintaining academic quality. The Skill uses the existing anti-AI patterns reference library (vocabulary, sentence-patterns, transitions-and-tone) as its detection basis, presents results with risk-level tagging, and lets users choose which items to rewrite by risk level.

</domain>

<decisions>
## Implementation Decisions

### Detection presentation
-逐句高亮 + 三级风险标签（High Risk / Medium Risk / Optional），沿用 anti-AI 库的风险分层
- 先展示总体摘要（"发现 12 处 AI 模式（5 High / 4 Medium / 3 Optional）"），再展示详细列表
- 每个检测项包含：原文片段、风险等级、模式类别（词汇膨胀/句式模式/过渡语气）、建议替换方案

### Rewriting strategy
- 检测后用户选择再改写（不是检测和改写一步到位）
- 用户按风险等级批量选择："改所有 High Risk" / "改 High + Medium" / "全部改"
- 原地编辑 + LaTeX 注释，沿用 Polish Skill 的 `% [De-AI] Original:` 格式
- 改写完成后生成改写报告：改写数量统计、按类别分布、主要改写类型摘要

### Input and output modes
- 支持文件路径和粘贴文本双输入模式（沿用 Translation/Polish Skill 模式）
- 文件输入：原地编辑 + LaTeX 注释标注
- 粘贴文本：在对话中展示检测结果 + 改写版本

### Upstream workflow integration
- 独立运行，不感知上游处理历史（Translation/Polish 已预防性加载 anti-AI 库）
- 支持指定目标期刊风格，改写时考虑期刊偏好；缺少模板则拒绝（沿用 Phase 3/4 决策）

### Detection scope and depth
- 三个维度全检测：vocabulary（词汇膨胀）、sentence-patterns（句式模式）、transitions-and-tone（过渡与语气）
- 全文一次性扫描（非分段），可发现跨段落的模式重复
- 仅匹配 anti-AI 库中的已知模式，不做统计性结构分析
- 领域术语保护：当检测到的词汇是领域标准术语时（如地理学中的 "landscape"），标记为跳过而非误报

### Claude's Discretion
- 具体的领域术语保护词表如何构建（硬编码 vs 动态推断）
- 检测报告的精确排版格式
- 改写时如何保持上下文连贯性
- 全文扫描的内部处理策略

</decisions>

<specifics>
## Specific Ideas

- De-AI 的核心价值是"可解释的改写"——用户能看到每个改写的原因（来自 anti-AI 库的哪个类别、哪个风险层级）
- 按风险等级批量选择避免了逐条确认的繁琐，同时保持用户控制
- 改写不应是简单的同义词替换，而是在保持学术质量的前提下重组表达
- LaTeX 注释标注让用户可以在编辑器中方便地审查和回退改写

</specifics>

<code_context>
## Existing Code Insights

### Reusable Assets
- `references/anti-ai-patterns.md` + 三个叶模块（vocabulary.md、sentence-patterns.md、transitions-and-tone.md）：De-AI 的核心检测依据，按 High/Medium/Optional 三级风险组织
- `.claude/skills/polish-skill/SKILL.md`：已实现的 LaTeX 注释标注模式（`% [Polish] Original:`）和 checklist 选择流程，De-AI 可复用相同模式
- `.claude/skills/translation-skill/SKILL.md`：双输入模式（文件+粘贴）的实现参考
- `references/skill-conventions.md` + `references/skill-skeleton.md`：Skill 结构规范
- `references/journals/ceus.md`：期刊模板，改写时的风格参考

### Established Patterns
- 稳定入口 + 叶模块的渐进式加载（Phase 1）
- Frontmatter 声明 `references.required` 和 `references.leaf_hints`（Phase 2）
- 期刊模板缺失 → 拒绝而非降级（Phase 3/4 决策）
- Anti-AI 模式主动加载（Phase 3/4 中的预防性使用）
- 原地编辑 + LaTeX 注释标注 + 清理流程（Phase 4 模式）

### Integration Points
- Skill 位于 `.claude/skills/de-ai-skill/SKILL.md`
- 接收 Polish Skill 的 summary 推荐作为触发场景
- 使用 Edit 工具进行原地修改
- 加载全部三个 anti-AI 叶模块作为检测依据

</code_context>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 05-de-ai-skill*
*Context gathered: 2026-03-11*
