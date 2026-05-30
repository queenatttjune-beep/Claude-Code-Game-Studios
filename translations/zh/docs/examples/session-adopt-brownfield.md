# 示例会话：/adopt — 棕地项目过渡

**上下文：** 开发人员在发现此 agent 系统之前已经制作游戏 3 个月。他们有代码、一些设计文档和粗略笔记——但格式不符合 skill 可用的要求。

**起始状态：**
- `src/gameplay/` — ~4000 行 Godot 4.6 GDScript
- `design/` — 3 个 markdown 文件，含粗略设计笔记（非 GDD 模板格式）
- 无 `design/gdd/` 目录
- 无 `docs/architecture/` 目录
- 无 `production/` 目录
- 无 `design/gdd/systems-index.md`
- CLAUDE.md 存在（引擎已配置：Godot 4.6）

**会话时长：** ~30 分钟
**回合：** 8
**结果：** 采用计划已写入，最紧迫的缺口（缺少系统索引）立即修复

---

## 第 1 回合：用户调用 /adopt

**用户：**
> /adopt

## 第 2 回合：Agent 检测项目状态

**Agent（technical-director，通过 /adopt，上下文 fork）：**
> 运行棕地审计...
>
> **阶段 1：项目状态检测**
>
> | 工件类别 | 找到 | 备注 |
> |------------------|-------|-------|
> | 引擎配置 | ✅ | GODOT 4.6 在 CLAUDE.md 中 |
> | 源代码 | ✅ | `src/gameplay/` 中约 4000 行 |
> | 设计文档 | ⚠️ | `design/` 中 3 个文件，非 GDD 格式 |
> | 架构文档 | ❌ | `docs/architecture/` 缺失 |
> | 系统索引 | ❌ | `design/gdd/systems-index.md` 缺失 |
> | 生产跟踪 | ❌ | `production/` 目录缺失 |
> | Sprint/Story 文件 | ❌ | 无 |
>
> **阶段 2：格式审计（现有文件）**
>
> 检查 3 个设计文件的内部结构...
>
> *`design/combat-notes.md`：*
> - 有非正式笔记，无 GDD 模板章节
> - 包含：一些规则描述、一个伤害公式、粗略平衡数字
> - GDD 模板缺失：概述、玩家幻想、边界情况、依赖关系、调节旋钮、验收标准（6/8 章节）
>
> *`design/crafting-ideas.md`：*
> - 头脑风暴式笔记，无结构
> - 无公式，无验收标准
> - 代表早期概念思考——对 GDD 作为输入有用，还不是 GDD
>
> *`design/inventory.md`：*
> - 最接近 GDD——有概述和规则部分
> - 缺失：玩家幻想、公式、边界情况、调节旋钮、验收标准（5/8 章节）
>
> **阶段 3：缺口分类**
>
> | 缺口 | 严重性 | 原因 |
> |-----|----------|--------|
> | 无系统索引 | BLOCKING | `/design-system`、`/create-stories`、`/gate-check` 都需要它 |
> | GDD 文件非模板格式 | HIGH | Skill 无法引用格式错误的 GDD 中的 TR-ID |
> | 无架构文档 | HIGH | 无法运行 `/architecture-review` 或 `/create-control-manifest` |
> | 无生产跟踪 | MEDIUM | Sprint 和 story skill 无法工作，但生产阶段前不需要 |
> | `design/crafting-ideas.md` — 前 GDD 内容 | LOW | 作为输入有用；在阻止任何内容前需要完整 GDD 创作 |

## 第 3 回合：Agent 呈现迁移计划

**Agent：**
> **阶段 4：迁移计划**
>
> 7 个有序步骤让你的项目完全对齐。绝不覆盖你现有的内容——每一步仅填补缺口。
>
> | # | 步骤 | Skill | 优先级 | 备注 |
> |---|------|-------|----------|-------|
> | 1 | 创建 `design/gdd/systems-index.md` | `/map-systems` 或 `/adopt` 内联 | BLOCKING | 解锁所有下游 skill |
> | 2 | 改造 `design/inventory.md` → GDD 格式 | `/design-system retrofit design/inventory.md` | HIGH | 添加 5 个缺失部分，保留现有内容 |
> | 3 | 改造 `design/combat-notes.md` → GDD 格式 | `/design-system retrofit design/combat-notes.md` | HIGH | 添加 6 个缺失部分 |
> | 4 | 将 `design/crafting-ideas.md` 转换为完整 GDD | `/design-system crafting`（新创作，使用想法作为输入） | MEDIUM | 不是改造——太非正式无法改造 |
> | 5 | 从现有代码创建架构 ADR | `/reverse-document` + `/architecture-decision` | HIGH | 捕获代码中已有的决策 |
> | 6 | 创建主架构文档 | `/create-architecture` | HIGH | 在 ADR 之后 |
> | 7 | 设置生产跟踪 | `/sprint-plan new` | MEDIUM | 在架构阶段之后 |
>
> **重要：** 第 1 步必须在任何其他步骤之前。其他所有步骤可以并行完成或根据优先级重新排序。
>
> 我可以将此计划写入 `docs/adoption-plan-2026-03-12.md` 吗？

**用户：**
> 是的。写入。

**Agent 写入采用计划。**

## 第 4 回合：Agent 主动修复最紧迫的缺口

**Agent：**
> BLOCKING 缺口是缺少的 `design/gdd/systems-index.md`。
>
> 我现在可以通过读取你现有代码和设计文档来创建一个，推断你构建了哪些系统。这比从头运行 `/map-systems` 更快——我会基于已有内容填充，然后你确认或调整。
>
> 要我现在就做吗？

**用户：**
> 是的，做吧。

## 第 5 回合：Agent 读取代码库并起草系统索引

**Agent：**
> 读取 `src/gameplay/` 以推断系统...
>
> 在代码中发现了这些系统边界：
>
> - `src/gameplay/combat/` — 伤害计算、命中检测、状态效果
> - `src/gameplay/inventory/` — 物品管理、重量、堆叠
> - `src/gameplay/crafting/` — 配方查找、结果生成（引用库存）
> - `src/gameplay/movement/` — CharacterBody2D、地形修改器（被战斗引用用于定位）
> - `src/gameplay/ui/` — HUD、库存屏幕、对话窗口
> - `src/gameplay/world/` — 瓦片地图、地形类型、区块加载
>
> **系统索引草稿：**
>
> | 系统 | 类别 | 优先级 | 依赖关系 | GDD 状态 |
> |--------|----------|----------|--------------|------------|
> | 移动 | 基础 | MVP | 无 | ❌ 缺失 |
> | 世界/地形 | 基础 | MVP | 移动 | ❌ 缺失 |
> | 战斗 | 核心 | MVP | 移动 | ⚠️ 需要改造 |
> | 库存 | 核心 | MVP | 无 | ⚠️ 需要改造 |
> | 制作 | 特性 | MVP | 库存 | ❌ 仅有前 GDD |
> | UI / HUD | 呈现 | MVP | 战斗、库存、移动 | ❌ 缺失 |
>
> 这看起来正确吗？有遗漏或分类错误吗？

**用户：**
> 看起来正确。添加一个耐力系统——它在 combat/ 中但应该是一个独立系统。

**Agent：**
> 已添加耐力作为核心/MVP 系统：
>
> | 耐力 | 核心 | MVP | 移动、战斗 | ❌ 缺失 |
>
> 已更新依赖关系：移动依赖于耐力（移动消耗耐力）。
>
> 我可以将此写入 `design/gdd/systems-index.md` 吗？

## 第 6 回合：用户批准

**用户：**
> 是的

**Agent 写入 `design/gdd/systems-index.md`。**

## 第 7 回合：Agent 显示现在已解锁的内容

**Agent：**
> `design/gdd/systems-index.md` 已创建。
>
> **BLOCKING 缺口已解决。** 以下 skill 现在可以运行：
>
> - ✅ `/design-system [名称]` — 为任何系统引导 GDD 创作
> - ✅ `/design-system retrofit [路径]` — 填补现有文档的缺失部分
> - ✅ `/gate-check` — 现在有系统索引可以验证
> - ✅ `/help` — 现在可以显示正确的阶段工作流
>
> **推荐的下一步（来自采用计划）：**
>
> 1. 运行 `/design-system retrofit design/inventory.md` — 你最完整的现有文档，最容易的胜利
> 2. 运行 `/design-system retrofit design/combat-notes.md`
> 3. 运行 `/design-system crafting` — 使用你的想法文件作为输入来创作制作 GDD
>
> 完成这 3 步后，你将有 5/7 个系统具有完整的 GDD。然后运行 `/review-all-gdds` 在架构工作前捕获跨系统一致性问题。

## 第 8 回合：用户确认计划

**用户：**
> 完美。我从库存改造开始。

**Agent：**
> 运行：
> ```
> /design-system retrofit design/inventory.md
> ```
>
> Skill 将检测你现有的概述和规则部分，显示哪 8 个部分缺失，并仅为缺口运行部分循环。你现有的内容不会被触及。

---

## 此示例展示了什么

- **FORMAT 审计，而非存在性审计**：`/adopt` 不只是检查文件是否存在——它检查它们的内部结构是否匹配 skill 期望。名为 `gdd.md` 但没有模板部分的文件会被标记为缺口。
- **迁移，而非替换**：现有内容从不覆盖。计划仅填补缺口。
- **先出现 BLOCKING 缺口**：缺少系统索引被识别为 BLOCKING，因为它阻止最多的 skill 运行。
- **紧急缺口的即时创建**：agent 不只是报告缺口，还在同一会话中主动修复。
- **从代码推断**：系统索引从代码库结构引导，而非从头编写——棕地代码已经包含答案。
- **改造 vs. 新创作**：`/design-system retrofit` 处理现有但不完整的文档；完整 `/design-system` 处理全新的文档。采用计划区别了适用于每个文件的选项。
- **上下文：fork**：`/adopt` 在 fork 上下文中运行，以避免用大量文件读取污染主会话。
