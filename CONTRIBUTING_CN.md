# 贡献指南

[English](CONTRIBUTING.md) | [中文](CONTRIBUTING_CN.md)

感谢你对 Paper Polish Workflow 项目的关注。

## 如何贡献

### 创建新 Skill

新 Skill 必须遵循项目规范：

1. 将 [`references/skill-skeleton.md`](references/skill-skeleton.md) 复制到新目录并命名为 `SKILL.md`
2. 遵循 [`references/skill-conventions.md`](references/skill-conventions.md) 中的规则
3. 填写所有必需的前置元数据字段（`name`、`description`、`triggers`、`tools`、`references`、`input_modes`、`output_contract`）
4. 填写所有必需的正文章节（`Purpose`、`Trigger`、`Modes`、`References`、`Ask Strategy`、`Workflow`、`Output Contract`、`Edge Cases`、`Fallbacks`）
5. 保持在约 300 行的预算范围内；将可复用内容移至 `references/`
6. 为结构化交互工具不可用时定义降级行为

### 添加期刊契约

1. 在 `references/journals/` 目录下创建新文件
2. 命名为 `[journal-name].md`（小写，空格用连字符）
3. 保持稳定标题契约：
   - `## Submission Requirements`
   - `## Writing Preferences`
   - `## Quality Checks`
   - `## Section Guidance`
4. 保证 `references/journals/[journal].md` 可以被下游 Skill 直接读取

### 改进表达模式库

1. 保持 `references/expression-patterns.md` 作为稳定总览入口
2. 在 `references/expression-patterns/` 中新增或调整叶子模块
3. 按写作场景组织内容，不要按泛泛的语法类别堆叠
4. 每个叶子模块应保留：
   - `## Recommended Expressions`
   - `## Avoid Expressions`
   - `## Usage Scenarios`
   - `## Bilingual Example Patterns`

### 改进 Anti-AI 参考库

1. 保持 `references/anti-ai-patterns.md` 作为稳定总览入口
2. 在 `references/anti-ai-patterns/` 中新增或调整叶子模块
3. 按风险类别组织内容，而不是按论文章节组织
4. 每个叶子模块应保留：
   - `## High Risk`
   - `## Medium Risk`
   - `## Optional`
5. 优先使用轻量的 `Problem expression -> Replacement` 表格，方便未来 Skill 直接检索

### 修改技能文件

1. 编辑 `paper-polish-workflow/SKILL.md`
2. 保留 YAML 前置元数据：
   ```yaml
   ---
   name: paper-polish-workflow
   description: ...
   ---
   ```
3. 不要把长篇参考内容继续塞进 `SKILL.md`，而是放到 `references/`
4. 除非明确在做工作流重设计，否则保持原有流程结构

## SKILL.md 要求

所有 Skill 必须遵循 [Skill 编写规范](references/skill-conventions.md)。请以 [skill-skeleton.md](references/skill-skeleton.md) 作为起点。

### 前置元数据（必需）

每个 `SKILL.md` 都必须带 YAML 前置元数据，包含以下必需字段：

```yaml
---
name: skill-name
description: 包含触发关键词的简短描述
triggers:
  primary_intent: 技能用途
  examples: ["English phrase", "中文短语"]
tools: [Read, Write]
references:
  required: [references/expression-patterns.md]
input_modes: [file, pasted_text]
output_contract: [polished_english]
---
```

**规则：**
- `name`：最多 64 字符，格式 `^[a-z0-9]+(-[a-z0-9]+)*$`
- `description`：最多 1024 字符，包含触发关键词
- `name` 必须与父目录名称匹配
- `tools` 列出功能类别，而非特定厂商的工具名
- 完整字段参考请查看 [skill-conventions.md](references/skill-conventions.md)

## Pull Request 流程

1. Fork 仓库
2. 创建功能分支
3. 进行修改
4. 使用 Claude/OpenCode 测试
5. 提交 Pull Request

## 有问题？

欢迎提 Issue 或发起讨论。
