# Methods and Data  module is designed to be read on its own.

 module is designed to be read on its own.

## Recommended Expressions

| Scenario | Expression | 中文说明 |
|----------|------------|----------|
| Dataset introduction | `We use [dataset] to capture [phenomenon / variable / behavior].` | 数据介绍要先说用途，再说来源。 |
| Study area description | `The study area covers [location], which provides a suitable setting for examining [question].` | 适合地理与城市研究中的 study area 说明。 |
| Method overview | `Our framework consists of [number] components: [component 1], [component 2], and [component 3].` | 适合概括方法结构。 |
| Procedure step | `First, we [action]. We then [action]. Finally, we [action].` | 用于稳定描述流程。 |
| Implementation rationale | `This design enables [benefit] while preserving [constraint / property].` | 解释方法选择的理由，而不是只报步骤。 |

## Avoid Expressions

| Avoid | Better Direction | 中文说明 |
|-------|------------------|----------|
| `We simply use ...` | Explain the design reason | `simply` 常让方法显得随意。 |
| `The data is from ...` | State source plus analytical role | 只说来源不够，要交代数据在研究中的作用。 |
| `Then the model can better learn ...` | Specify what is learned and how it is evaluated | 避免模糊的能力描述。 |
| `This method is efficient and robust.` | Provide mechanism or evidence | 方法优点需要有依据。 |

## Usage Scenarios

- 数据章节中描述数据来源、规模、时间范围和筛选逻辑。
- 方法章节中概述 pipeline、模块分工和设计 rationale。
- 实验设置里说明 baselines、参数、评价指标和 reproducibility 细节。
- study area 描述里连接城市背景与研究问题。

## Bilingual Example  module is designed to be read on its own.

| 中文意图 | English Pattern | Example |
|----------|-----------------|---------|
| 介绍数据来源与用途 | `We use [dataset] to capture [phenomenon].` | `We use street-view imagery and parcel-level land-use records to capture both visual cues and functional urban context.` |
| 解释研究区为何合适 | `The study area covers [location], which provides a suitable setting for examining [question].` | `The study area covers central Shanghai, which provides a suitable setting for examining the interaction between dense morphology and perceived walkability.` |
| 说明方法结构 | `Our framework consists of [number] components ...` | `Our framework consists of three components: reference retrieval, journal-specific constraint loading, and targeted rewriting.` |

---

*Module: methods-and-data*

