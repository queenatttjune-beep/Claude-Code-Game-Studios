---
name: adopt
description: "棕地项目接入 — 审计现有项目工件是否符合模板格式要求（不仅仅是存在性），按影响分类差距，生成编号迁移计划。在加入进行中的项目或从旧模板版本升级时运行。与 /project-stage-detect（检查什么存在）不同 — 本 skill 检查已存在的内容是否能与模板的 skills 正常工作。"
argument-hint: "[focus: full | gdds | adrs | stories | infra]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, AskUserQuestion
model: sonnet
agent: technical-director
---

# Adopt — 棕地项目模板接入

本 skill 审计现有项目的工件是否符合模板 skill 流水线的**格式要求**，然后生成优先化的迁移计划。

**这不是 `/project-stage-detect`。**
`/project-stage-detect` 回答：*什么存在？*
`/adopt` 回答：*存在的内容能否与模板的 skills 正常工作？*

一个项目可能有 GDD、ADR 和故事——但如果这些工件内部格式不正确，每个格式敏感的 skill 仍然会静默失败或产生错误结果。

**输出：** `docs/adoption-plan-[date].md` — 持久化、可检查的迁移计划。

**参数模式：**

**审计模式：** `$ARGUMENTS[0]`（空白 = `full`）

- **无参数 / `full`**：完整审计——所有工件类型
- **`gdds`**：仅 GDD 格式合规性
- **`adrs`**：仅 ADR 格式合规性
- **`stories`**：仅故事格式合规性
- **`infra`**：仅基础设施工件缺口（注册表、清单、sprint-status、stage.txt）

---

## 阶段 1：检测项目状态

在读取前发出提示：`"Scanning project artifacts..."` — 这确认 skill 在静默读取阶段正在运行。

然后在展示任何内容之前静默读取。

### 存在性检查
- `production/stage.txt` — 如果存在，读取（权威阶段）
- `design/gdd/game-concept.md` — 概念存在？
- `design/gdd/systems-index.md` — 系统索引存在？
- 统计 GDD 文件：`design/gdd/*.md`（排除 game-concept.md 和 systems-index.md）
- 统计 ADR 文件：`docs/architecture/adr-*.md`
- 统计故事文件：`production/epics/**/*.md`（排除 EPIC.md）
- `.claude/docs/technical-preferences.md` — 引擎已配置？
- `docs/engine-reference/` — 引擎参考文档存在？
- Glob `docs/adoption-plan-*.md` — 如果有，记录最近的先前计划文件名

### 推断阶段（若无 stage.txt）
使用与 `/project-stage-detect` 相同的启发式规则：
- `src/` 中有 10+ 源文件 → Production
- `production/epics/` 中有故事 → Pre-Production
- 存在 ADR → Technical Setup
- 存在 systems-index.md → Systems Design
- 存在 game-concept.md → Concept
- 什么都没有 → Fresh（不是棕地项目——建议 `/start`）

如果项目看起来是全新的（没有任何工件），使用 `AskUserQuestion`：
- "这看起来是一个全新项目——没有发现已有工件。`/adopt` 适用于有需要迁移的工作的项目。您想怎么做？"
  - "运行 `/start` — 开始引导式首次接入"
  - "我的工件在非标准位置——帮我找到它们"
  - "取消"

然后停止——无论用户选择哪个选项，都不要继续进行审计（每个选项都指向不同的 skill 或手动调查）。

报告："检测阶段：[phase]。发现：[N] 个 GDD，[M] 个 ADR，[P] 个故事。"

---

## 阶段 2：格式审计

对于范围内的每种工件类型（基于参数模式），不仅要检查文件是否存在，还要检查是否包含模板所需的内部结构。

### 2a：GDD 格式审计

对每个找到的 GDD 文件，通过扫描标题检查 8 个必需部分：

| 必需部分 | 要查找的标题模式 |
|---|--|
| Overview | `## Overview` |
| Player Fantasy | `## Player Fantasy` |
| Detailed Rules / Design | `## Detailed` 或 `## Core Rules` 或 `## Detailed Design` |
| Formulas | `## Formulas` 或 `## Formula` |
| Edge Cases | `## Edge Cases` |
| Dependencies | `## Dependencies` 或 `## Depends` |
| Tuning Knobs | `## Tuning` |
| Acceptance Criteria | `## Acceptance` |

对每个 GDD，记录：
- 哪些部分存在
- 哪些部分缺失
- 现有部分是否有实际内容或只是占位文本（`[To be designed]` 或类似）

同时检查：每个 GDD 的头部块中是否有 `**Status**:` 字段？
有效值：`In Design`、`Designed`、`In Review`、`Approved`、`Needs Revision`。

### 2b：ADR 格式审计

对每个找到的 ADR 文件，检查这些关键部分：

| 部分 | 缺失的影响 |
|---|--|
| `## Status` | **BLOCKING** — `/story-readiness` 的 ADR 状态检查会静默通过所有内容 |
| `## ADR Dependencies` | HIGH — `/architecture-review` 中的依赖排序会中断 |
| `## Engine Compatibility` | HIGH — 版本截止日期后的 API 风险未知 |
| `## GDD Requirements Addressed` | MEDIUM — 可追溯性矩阵失去覆盖 |
| `## Performance Implications` | LOW — 非流水线关键 |

对每个 ADR，记录：哪些部分存在，哪些缺失，如果 Status 部分存在则记录当前 Status 值。

### 2c：systems-index.md 格式审计

如果 `design/gdd/systems-index.md` 存在：

1. **括号状态值** — Grep 查找任何包含括号的 Status 单元格：`"Needs Revision ("`、`"In Progress ("` 等。这会破坏 `/gate-check`、`/create-stories` 和 `/architecture-review` 中的精确字符串匹配。**BLOCKING。**

2. **有效状态值** — 检查 Status 列的值仅来自：`Not Started`、`In Progress`、`In Review`、`Designed`、`Approved`、`Needs Revision`。标记任何无法识别的值。

3. **列结构** — 检查表格至少有：System name、Layer、Priority、Status 列。缺少列会降低 skill 功能。

### 2d：故事格式审计

对每个找到的故事文件：

- **`Manifest Version:` 字段** — 故事头中存在？（LOW — 如果不存在则自动通过）
- **TR-ID 引用** — 故事是否包含 `TR-[a-z]+-[0-9]+` 模式？（MEDIUM — 无陈旧性跟踪）
- **ADR 引用** — 故事是否引用至少一个 ADR？（检查 `ADR-` 模式）
- **Status 字段** — 存在且可读？
- **验收标准** — 故事是否有复选框列表（`- [ ]`）？

### 2e：基础设施审计

| 工件 | 路径 | 缺失的影响 |
|---|---|---|
| TR 注册表 | `docs/architecture/tr-registry.yaml` | HIGH — 无稳定需求 ID |
| 控制清单 | `docs/architecture/control-manifest.md` | HIGH — 无故事层级规则 |
| 清单版本戳 | 清单头部：`Manifest Version:` | MEDIUM — 陈腐检查盲目 |
| Sprint 状态 | `production/sprint-status.yaml` | MEDIUM — `/sprint-status` 回退到 markdown |
| 阶段文件 | `production/stage.txt` | MEDIUM — 阶段自动检测不可靠 |
| 引擎参考 | `docs/engine-reference/[engine]/VERSION.md` | HIGH — ADR 引擎检查盲目 |
| 架构可追溯性 | `docs/architecture/architecture-traceability.md` | MEDIUM — 无持久化矩阵 |

### 2f：技术偏好审计

读取 `.claude/docs/technical-preferences.md`。检查每个字段是否为 `[TO BE CONFIGURED]`：
- Engine、Language、Rendering、Physics → 如果未配置则为 HIGH（ADR skills 会失败）
- Naming conventions → MEDIUM
- Performance budgets → MEDIUM
- Forbidden Patterns、Allowed Libraries → LOW（设计上初始为空）

---

## 阶段 3：分类和优先排序差距

将所有审计中发现的每个差距组织成四个严重级别：

**BLOCKING** — 会导致模板 skills 现在**立即**静默产生错误结果。
例如：ADR 缺少 Status 字段、systems-index 括号状态值、存在 ADR 但引擎未配置。

**HIGH** — 会导致故事生成时缺少安全检查，或基础设施引导失败。
例如：ADR 缺少 Engine Compatibility、GDD 缺少 Acceptance Criteria（无法从中生成故事）、tr-registry.yaml 缺失。

**MEDIUM** — 降低质量和流水线跟踪，但不破坏功能。
例如：GDD 缺少 Tuning Knobs 或 Formulas 部分、故事缺少 TR-ID、sprint-status.yaml 缺失。

**LOW** — 追溯性改进，有则更好但不紧急。
例如：故事缺少 Manifest Version 戳、GDD 缺少 Open Questions 部分。

统计每个级别的总数。如果没有 BLOCKING 和 HIGH 差距：报告项目与模板兼容，仅剩建议性改进。

---

## 阶段 4：构建迁移计划

编写编号的、有序的行动计划。排序规则：
1. BLOCKING 差距优先（必须在任何流水线 skill 可靠运行前修复）
2. HIGH 差距其次，基础设施优先于 GDD/ADR 内容（引导需要正确格式）
3. MEDIUM 差距排序：GDD 差距优先于 ADR 差距，ADR 差距优先于故事差距（故事依赖于 GDD 和 ADR）
4. LOW 差距最后

对每个差距，生成包含以下内容的计划条目：
- 清晰的问题陈述（一句话，无行话）
- 如果某个 skill 处理它，修复的确切命令
- 如果需直接编辑，手动步骤
- 时间估算（大概：5 分钟/30 分钟/1 个会话）
- 用于跟踪的复选框 `- [ ]`

**特殊情况 — systems-index 括号状态值：**
如果存在，始终是第一项。显示需要更改的确切值和替换文本。在编写计划前主动提供立即修复。

**特殊情况 — ADR 缺少 Status 字段：**
对每个受影响的 ADR，修复方法是：
`/architecture-decision retrofit docs/architecture/adr-[NNNN]-[slug].md`
将每个 ADR 列为单独的可检查项。

**特殊情况 — GDD 缺少部分：**
对每个受影响的 GDD，列出缺失的部分和修复方法：
`/design-system retrofit design/gdd/[filename].md`

**基础设施引导顺序** — 始终按此顺序呈现：
1. 先修复 ADR 格式（注册表依赖于读取 ADR Status 字段）
2. 运行 `/architecture-review` → 引导 `tr-registry.yaml`
3. 运行 `/create-control-manifest` → 创建带有版本戳的清单
4. 运行 `/sprint-plan update` → 创建 `sprint-status.yaml`
5. 运行 `/gate-check [phase]` → 权威写入 `stage.txt`

**现有故事** — 明确注明：
> "现有故事继续与所有模板 skills 配合工作——所有新的格式检查在字段不存在时自动通过。它们不会从 TR-ID 陈旧性跟踪或清单版本检查中受益，直到重新生成。这是有意为之：不要重新生成已经在进行中的故事。"

---

## 阶段 5：呈现摘要并请求写权限

在编写之前，呈现紧凑摘要：

```
## 接入审计摘要
检测阶段：[phase]
引擎：[已配置 / 未配置]
GDD 审计：[N]（[X] 完全合规，[Y] 有差距）
ADR 审计：[N]（[X] 完全合规，[Y] 有差距）
故事审计：[N]

差距计数：
  BLOCKING：[N] — 模板 skills 若不修复将发生故障
  HIGH：[N] — 运行 /create-stories 或 /story-readiness 不安全
  MEDIUM：[N] — 质量退化
  LOW：[N] — 可选改进

预计修复时间：[X blocking 项 × ~Y 分钟每项 = 大约 Z 小时]
```

在请求写权限前，显示**差距预览**：
- 将每个 BLOCKING 差距列为一行动态点，描述实际问题（例如 `systems-index.md: 3 行有括号状态值`、`adr-0002.md: 缺少 ## Status 部分`）。不显示计数——显示实际项。
- 将 HIGH / MEDIUM / LOW 显示为仅计数（例如 `HIGH: 4, MEDIUM: 2, LOW: 1`）。

这给用户足够的上下文来判断范围，然后才决定写入文件。

如果在阶段 1 中检测到先前的接入计划，添加一条注释：
> "在 `docs/adoption-plan-[prior-date].md` 存在先前计划。新计划将反映当前项目状态——它不会与先前的运行进行差异比较。"

使用 `AskUserQuestion`：
- "准备好编写迁移计划了吗？"
  - "是的——写入 `docs/adoption-plan-[date].md`"
  - "先显示完整的计划预览（暂时不写）"
  - "取消——我会手动处理迁移"

如果用户选择"先显示完整的计划预览"，将完整计划作为围栏 markdown 块输出。然后再次询问相同的三个选项。

---

## 阶段 6：编写接入计划

如果批准，写入 `docs/adoption-plan-[date].md`，结构如下：

```markdown
# 接入计划

> **生成日期**：[date]
> **项目阶段**：[phase]
> **引擎**：[名称 + 版本，或"未配置"]
> **模板版本**：v1.0+

按顺序完成这些步骤。完成每个项目后勾选。
随时重新运行 `/adopt` 以检查剩余差距。

---

## 步骤 1：修复 Blocking 差距

[每个 blocking 差距一个子部分，包含问题、修复命令、时间估算、复选框]

---

## 步骤 2：修复高优先级差距

[每个 high 差距一个子部分]

---

## 步骤 3：引导基础设施

### 3a. 注册现有需求（创建 tr-registry.yaml）
运行 `/architecture-review`——即使 ADR 已存在，此次运行会从您现有的 GDD 和 ADR 引导 TR 注册表。
**时间**：1 个会话（大型代码库的审查可能较长）
- [ ] tr-registry.yaml 已创建

### 3b. 创建控制清单
运行 `/create-control-manifest`
**时间**：30 分钟
- [ ] docs/architecture/control-manifest.md 已创建

### 3c. 创建 sprint 跟踪文件
运行 `/sprint-plan update`
**时间**：5 分钟（如果 sprint 计划已存在为 markdown）
- [ ] production/sprint-status.yaml 已创建

### 3d. 设置权威项目阶段
运行 `/gate-check [current-phase]`
**时间**：5 分钟
- [ ] production/stage.txt 已写入

---

## 步骤 4：中等优先级差距

[每个 medium 差距一个子部分]

---

## 步骤 5：可选改进

[每个 low 差距一个子部分]

---

## 现有故事的预期

现有故事继续与所有模板 skills 正常工作。新的格式检查（TR-ID 验证、清单版本陈旧性）在字段不存在时自动通过——所以不会破坏任何东西。它们在重新生成之前不会从陈旧性跟踪中受益。不要重新生成已经在进行中或已完成的故事。

---

## 重新运行

完成步骤 3 后再次运行 `/adopt`，以验证所有 blocking 和 high 差距已解决。新运行将反映项目的当前状态。
```

---

## 阶段 6b：设置审查模式

写入接入计划后（或用户取消写入后），检查 `production/review-mode.txt` 是否存在。

**如果存在**：读取它并注明当前模式——"Review mode 已设置为 `[current]`。"——跳过提示。

**如果不存在**：使用 `AskUserQuestion`：

- **提示**："还有一个设置步骤：在工作流中您希望进行多少设计审查？"
- **选项**：
  - `Full` — 在每个关键工作流步骤由总监专家审查。最适合团队、学习工作流，或在每个决策上都想获得全面反馈时使用。
  - `Lean (推荐)` — 仅在阶段关卡过渡（/gate-check）时由总监审查。跳过错skill级别的审查。平衡方式，适合 solo 开发者和小型团队。
  - `Solo` — 完全没有总监审查。最大速度。最适合游戏 jam、原型制作，或审查感觉像是额外负担时。

选择后立即写入 `production/review-mode.txt`——无需单独的"May I write?"：
- `Full` → 写入 `full`
- `Lean (推荐)` → 写入 `lean`
- `Solo` → 写入 `solo`

如果 `production/` 目录不存在则创建。

---

## 阶段 7：提供第一个行动

写入计划后不要停在那里。选择最高优先级的差距，并使用 `AskUserQuestion` 立即处理它。选择第一个适用的分支：

**如果 systems-index.md 中存在括号状态值：**
使用 `AskUserQuestion`：
- "最紧急的修复是 `systems-index.md`——[N] 行有括号状态值（例如 `Needs Revision (see notes)`），这会破坏 /gate-check、/create-stories 和 /architecture-review。我可以原地修复这些。"
  - "立即修复——编辑 systems-index.md"
  - "我自己修复"
  - "完成——把计划留给我"

**如果 ADR 缺少 `## Status`（且无括号问题）：**
使用 `AskUserQuestion`：
- "最紧急的修复是向 [N] 个 ADR 添加 `## Status`：[文件名列表]。缺少它，/story-readiness 会静默通过所有 ADR 检查。从 [第一个受影响的文件名] 开始？"
  - "是的——立即改造 [第一个受影响的文件名]"
  - "逐个改造所有 [N] 个 ADR"
  - "我会自行处理 ADR"

**如果 GDD 缺少 Acceptance Criteria（且以上无 blocking 问题）：**
使用 `AskUserQuestion`：
- "最紧急的差距是 [N] 个 GDD 缺少 Acceptance Criteria：[文件名列表]。没有它们，/create-stories 无法生成故事。从 [最高优先级 GDD 文件名] 开始？"
  - "是的——立即向 [GDD 文件名] 添加 Acceptance Criteria"
  - "逐个完成所有 [N] 个 GDD"
  - "我会自行处理 GDD"

**如果没有 BLOCKING 或 HIGH 差距：**
使用 `AskUserQuestion`：
- "没有 blocking 差距——此项目与模板兼容。接下来做什么？"
  - "带我了解中等优先级的改进"
  - "运行 /project-stage-detect 进行更广泛的健康检查"
  - "完成——我会按自己的节奏执行计划"

> **接入计划已保存到 `docs/adoption-plan-[date].md`。** 随时重新运行 `/adopt`，在完成项目时重新检查剩余差距。

---

## 协作协议

1. **静默读取**——在展示任何内容前完成完整审计
2. **先显示摘要**——在请求写入前让用户看到范围
3. **写入前询问**——在创建接入计划文件前始终确认
4. **提供，不强制**——计划是建议性的；用户决定修复什么和何时修复
5. **一次一个行动**——交出计划后，提供一个特定的下一步，而不是六个同时要做的项目列表
6. **绝不重新生成现有工件**——仅填补已存在内容的空白；不要重写已有内容的 GDD、ADR 或故事
