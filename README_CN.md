# 论文润色工作流

[English](README.md) | [中文](README_CN.md)

一个系统化的、AI 辅助的学术论文润色工作流。专为 Claude/OpenCode AI 助手设计。

## 这是什么？

一个引导你**逐步**润色论文的 AI 技能：

```
结构 → 句子逻辑 → 表达方式
```

AI 不会随意修改，而是帮助你：
1. 确认每个章节的整体结构
2. 确认每句话的逻辑
3. 从多个表达选项中选择
4. 检查一致性和连贯性

## 特点

- **自上而下**：确认逻辑之前不会跳到措辞
- **用户主导**：每一步都需要你的确认
- **选项制**：使用交互式选择提高效率
- **参考驱动**：通过共享参考库复用表达与约束，而不是把长表格塞进 Skill
- **期刊感知**：包含稳定的 CEUS 期刊契约（可扩展到其他期刊）
- **模块化参考库**：保留稳定入口文件，同时支持按需加载更细的叶子模块

## 安装

### OpenCode 用户

```bash
# 将技能目录复制到你的项目
cp -r paper-polish-workflow/ .opencode/skills/
```

### Claude Code 用户

```bash
# 复制技能目录
cp -r paper-polish-workflow/ .claude/skills/
```

## 快速开始

直接让 AI 帮你润色论文：

```
用户：帮我润色摘要

AI：[阶段 0]
    - 目标期刊是什么？
    - 示例论文在哪里？

用户：CEUS，示例在 docs/example

AI：[步骤 1] 展示结构供确认...
AI：[步骤 2] 展示每句话的逻辑...
AI：[步骤 3] 展示表达选项...
AI：[步骤 5] 检查连贯性，建议过渡词...
AI：将润色后的版本写入文件
```

## 工作流步骤

| 步骤 | 内容 |
|------|------|
| **阶段 0** | AI 询问目标期刊、字数限制、示例论文 |
| **步骤 1** | AI 展示章节结构供确认 |
| **步骤 2** | AI 展示每句话的逻辑供确认 |
| **步骤 3** | AI 展示表达选项（多选题） |
| **步骤 4** | AI 根据需要参考示例论文 |
| **步骤 5** | AI 检查重复和连贯性 |
| **写入** | AI 展示最终版本并写入文件 |

## 编写新 Skill

所有新 Skill 必须遵循项目的 Skill 编写规范。从这里开始：

- [`references/skill-conventions.md`](references/skill-conventions.md)：规范化编写规则（前置元数据契约、模式、降级策略、行数预算）
- [`references/skill-skeleton.md`](references/skill-skeleton.md)：可直接复制的示例骨架

详细的编写指导请参阅 [CONTRIBUTING_CN.md](CONTRIBUTING_CN.md)。

## 共享参考库

项目使用”稳定入口 + 按需叶子模块”的参考库结构。

### 稳定入口文件

- [`references/expression-patterns.md`](references/expression-patterns.md)：学术表达模式库总览与模块地图
- [`references/anti-ai-patterns.md`](references/anti-ai-patterns.md)：Anti-AI 风险模型与总览入口
- [`references/journals/ceus.md`](references/journals/ceus.md)：CEUS 期刊契约
- [`references/skill-conventions.md`](references/skill-conventions.md)：Skill 编写规范
- [`references/skill-skeleton.md`](references/skill-skeleton.md)：可复制的 Skill 模板

### 为什么这样组织？

- Skill 可以继续依赖稳定的顶层路径。
- 长流程只需加载 `references/expression-patterns/` 或 `references/anti-ai-patterns/` 下的相关叶子模块。
- 后续新增期刊也能沿用 `references/journals/[journal].md` 的稳定契约。
- 新 Skill 从骨架模板开始，遵循统一规范，确保整个套件的一致性。

## 支持的期刊

### CEUS (Computers, Environment and Urban Systems)

内置支持：
- 字数限制（总计 <=8,000 字，摘要 <=250 字）
- Highlights 生成（3-5 条，每条 <=85 字符）
- 写作偏好与质量检查清单
- 面向下游 Skill 的分章节指导

查看 [`references/journals/ceus.md`](references/journals/ceus.md) 获取当前契约内容。

### 添加其他期刊

在 `references/journals/` 目录下创建新文件，并遵循与 CEUS 相同的标题契约：
- `## Submission Requirements`
- `## Writing Preferences`
- `## Quality Checks`
- `## Section Guidance`

## 项目结构

```text
paper-polish-workflow/
├── paper-polish-workflow/
│   └── SKILL.md
├── references/
│   ├── skill-conventions.md            # Skill 编写规范
│   ├── skill-skeleton.md               # 可复制的 Skill 模板
│   ├── expression-patterns.md          # 稳定入口文件
│   ├── expression-patterns/            # 按场景拆分的表达模块
│   ├── anti-ai-patterns.md             # 稳定入口文件
│   ├── anti-ai-patterns/               # 按风险类别拆分的 Anti-AI 模块
│   └── journals/
│       └── ceus.md                     # 稳定 CEUS 契约
├── examples/
│   └── abstract-polishing-session.md
├── README.md
├── README_CN.md
├── CONTRIBUTING.md
└── CONTRIBUTING_CN.md
```

## 要求

- 支持工具访问的 OpenCode 或 Claude Code
- 需要的工具：
  - `mcp_question` - 用于选项选择
  - `mcp_read`、`mcp_write` - 用于文件操作
  - `mcp_look_at` - 用于 PDF 分析（可选）

## 示例会话

查看 [examples/abstract-polishing-session.md](examples/abstract-polishing-session.md) 获取完整演示。

## 贡献

查看 [CONTRIBUTING_CN.md](CONTRIBUTING_CN.md) 了解贡献指南。特别欢迎以下方向：
- 在 `references/journals/` 下新增期刊契约
- 在 `references/expression-patterns/` 下新增表达模块
- 在 `references/anti-ai-patterns/` 下新增 Anti-AI 模块

## 许可证

MIT 许可证 - 详见 [LICENSE](LICENSE)。

## 致谢

在为 CEUS 期刊投稿润色双层城市感知论文的过程中，通过反复迭代开发而成。
