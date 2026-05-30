# Claude Code Game Studios -- 完整工作流指南

> **如何使用 Agent 架构从零到发布游戏。**
>
> 本指南带你了解使用 49-agent 系统、73 个斜杠命令和 12 个自动化钩子的游戏开发的所有阶段。假设你已经安装 Claude Code 并从项目根目录工作。
>
> 管线有 7 个阶段。每个阶段有一个必须通过才能前进的正式关卡（`/gate-check`）。权威的阶段顺序在 `.claude/docs/workflow-catalog.yaml` 中定义，并由 `/help` 读取。

---

## 目录

1. [快速开始](#快速开始)
2. [阶段 1：概念](#阶段-1概念)
3. [阶段 2：系统设计](#阶段-2系统设计)
4. [阶段 3：技术设置](#阶段-3技术设置)
5. [阶段 4：预生产](#阶段-4预生产)
6. [阶段 5：生产](#阶段-5生产)
7. [阶段 6：打磨](#阶段-6打磨)
8. [阶段 7：发布](#阶段-7发布)
9. [横切关注点](#横切关注点)
10. [附录 A：Agent 快速参考](#附录-aagent-快速参考)
11. [附录 B：斜杠命令快速参考](#附录-b斜杠命令快速参考)
12. [附录 C：常见工作流](#附录-c常见工作流)

---

## 快速开始

### 你需要什么

开始之前，请确保你有：

- **Claude Code** 已安装并正常工作
- **Git**，带 Git Bash（Windows）或标准终端（Mac/Linux）
- **jq**（可选但推荐——钩子在缺失时回退到 `grep`）
- **Python 3**（可选——某些钩子用它进行 JSON 验证）

### 第 1 步：克隆并打开

```bash
git clone <仓库-url> my-game
cd my-game
```

### 第 2 步：运行 /start

如果这是你的首次会话：

```
/start
```

此引导式启动会询问你当前的位置，并将你路由到正确的阶段：

- **路径 A** -- 还不知道：路由到 `/brainstorm`
- **路径 B** -- 模糊想法：路由到带初始想法的 `/brainstorm`
- **路径 C** -- 清晰概念：路由到 `/setup-engine` 和 `/map-systems`
- **路径 D1** -- 现有项目，少量工件：正常流程
- **路径 D2** -- 现有项目，已有 GDD/ADR：运行 `/project-stage-detect`
  然后运行 `/adopt` 进行棕地迁移

### 第 3 步：验证钩子是否正常工作

启动一个新的 Claude Code 会话。你应该看到 `session-start.sh` 钩子的输出：

```
=== Claude Code Game Studios -- 会话上下文 ===
分支：main
最近提交：
  abc1234 初始提交
===================================
```

如果看到这个，钩子正常工作。如果没有，检查 `.claude/settings.json`，确保钩子路径对你的操作系统正确。

### 第 4 步：随时请求帮助

在任何时候，运行：

```
/help
```

这会从 `production/stage.txt` 读取你的当前阶段，检查存在哪些工件，并准确告诉你下一步做什么。它区分必需的下一步和可选的机会。

### 第 5 步：创建你的目录结构

目录按需创建。系统期望以下布局：

```
src/                  # 游戏源代码
  core/               # 引擎/框架代码
  gameplay/           # 游戏系统
  ai/                 # AI 系统
  networking/         # 多人游戏代码
  ui/                 # UI 代码
  tools/              # 开发工具
assets/               # 游戏资源
  art/                # 精灵、模型、纹理
  audio/              # 音乐、SFX
  vfx/                # 粒子效果
  shaders/            # 着色器文件
  data/               # JSON 配置/平衡数据
design/               # 设计文档
  gdd/                # 游戏设计文档
  narrative/          # 故事、世界观、对话
  levels/             # 关卡设计文档
  balance/            # 平衡电子表格和数据
  ux/                 # UX 规范
docs/                 # 技术文档
  architecture/       # 架构决策记录
  api/                # API 文档
  postmortems/        # 事后分析
tests/                # 测试套件
prototypes/           # 一次性原型
production/           # 冲刺计划、里程碑、发布
  sprints/
  milestones/
  releases/
  epics/              # Epic 和 story 文件（来自 /create-epics + /create-stories）
  playtests/          # 游戏测试报告
  session-state/      # 临时会话状态（被 gitignore）
  session-logs/       # 会话审计追踪（被 gitignore）
```

> **提示：** 你不需要在第一天就有所有这些目录。当你进入需要它们的阶段时再创建。重要的是在创建时遵循此结构，因为**规则系统**基于文件路径强制执行标准。`src/gameplay/` 中的代码获得游戏规则，`src/ai/` 中的代码获得 AI 规则，以此类推。

---

## 阶段 1：概念

### 此阶段发生什么

你从"没有想法"或"模糊想法"到结构化的游戏概念文档，包含已定义的支柱和玩家旅程。这是你弄清楚**制作什么**和**为什么**的阶段。

### 阶段 1 管线

```
/brainstorm  -->  game-concept.md  -->  /design-review  -->  /setup-engine
     |                                        |                    |
     v                                        v                    v
  10 个概念      概念文档含支柱、MDA、    概念文档验证          引擎在
  MDA 分析       核心循环、USP                                    technical-preferences.md 中固定
  玩家动机                                                                   |
                                                                           v
                                                                     /prototype
                                                               （概念原型 — 1-3 天）
                                                               继续 ↓     转向 → /brainstorm
                                                                           |
                                                                           v（继续）
                                                                     /map-systems
                                                                           |
                                                                           v
                                                                    systems-index.md
                                                                   （所有系统、依赖、
                                                                    优先级层级）
```

### 第 1.1 步：用 /brainstorm 进行头脑风暴

这是你的起点。运行头脑风暴 skill：

```
/brainstorm
```

或带类型提示：

```
/brainstorm roguelike deckbuilder
```

**发生了什么：** 头脑风暴 skill 引导你完成一个协作式的 6 阶段构思过程，使用专业工作室技术：

1. 询问你的兴趣、主题和约束
2. 生成 10 个概念种子及 MDA（机制、动态、美学）分析
3. 你选择 2-3 个最喜欢的进行深入分析
4. 执行玩家动机映射和受众定位
5. 你选择获胜概念
6. 将其正式化为 `design/gdd/game-concept.md`

概念文档包括：

- 电梯演讲（一句话）
- 核心幻想（玩家想象自己在做什么）
- MDA 分解
- 目标受众（Bartle 类型、人口统计）
- 核心循环图
- 独特卖点
- 可比作品和差异化
- 游戏支柱（3-5 个不可协商的设计价值）
- 反支柱（游戏有意避免的内容）

### 第 1.2 步：审查概念（可选但推荐）

```
/design-review design/gdd/game-concept.md
```

在你继续之前验证结构和完整性。

### 第 1.3 步：选择你的引擎

```
/setup-engine
```

或指定特定引擎：

```
/setup-engine godot 4.6
```

**/setup-engine 的作用：**

- 用命名约定、性能预算和引擎特定默认值填充 `.claude/docs/technical-preferences.md`
- 检测知识空白（引擎版本比 LLM 训练数据更新），并建议交叉参考 `docs/engine-reference/`
- 在 `docs/engine-reference/` 中创建版本固定的参考文档

**为什么这很重要：** 一旦你设置好引擎，系统就知道使用哪些引擎专业 agent。如果你选择 Godot，像 `godot-specialist`、`godot-gdscript-specialist` 和 `godot-shader-specialist` 这样的 agent 就成为你的首选专家。

### 第 1.4 步：将概念分解为系统

在编写单个 GDD 之前，列举你游戏所需的所有系统：

```
/map-systems
```

这创建 `design/gdd/systems-index.md` -- 一个主跟踪文档，它将：

- 列出游戏需要的每个系统（战斗、移动、UI 等）
- 映射系统之间的依赖关系
- 分配优先级层级（MVP、垂直切片、Alpha、完整愿景）
- 确定设计顺序（基础 > 核心 > 特性 > 呈现 > 打磨）

此步骤是**必需的**，然后才能进入阶段 2。来自 155 个游戏事后分析的研究证实，跳过系统枚举会在生产中付出 5-10 倍的代价。

### 阶段 1 关卡

```
/gate-check concept
```

**通过要求：**

- 在 `technical-preferences.md` 中配置了引擎
- `design/gdd/game-concept.md` 存在，包含支柱
- `design/gdd/systems-index.md` 存在，包含依赖顺序

**裁决：** 通过 / 关注 / 失败。关注可在确认风险的情况下通过。失败阻止前进。

---

## 阶段 2：系统设计

### 此阶段发生什么

你创建所有定义游戏如何工作的设计文档。没有任何东西被编码——这是纯设计。在系统索引中识别的每个系统都有自己的 GDD，逐部分创作，单独审查，然后所有 GDD 被交叉检查一致性。

### 阶段 2 管线

```
/map-systems next  -->  /design-system  -->  /design-review
       |                     |                     |
       v                     v                     v
  从系统索引中拾取       逐部分 GDD            验证 8 个
  下一个系统             创作（增量写入）       必需部分
                                               批准/需要修改
       |
       |  （对每个 MVP 系统重复）
       v
/review-all-gdds
       |
       v
  跨 GDD 一致性 + 设计理论审查
  通过 / 关注 / 失败
```

### 第 2.1 步：编写系统 GDD

按依赖顺序使用引导工作流设计每个系统：

```
/map-systems next
```

这拾取最高优先级的未设计系统，并移交给 `/design-system`，后者引导你逐部分创建其 GDD。

你也可以直接设计特定系统：

```
/design-system combat-system
```

**/design-system 的作用：**

1. 读取你的游戏概念、系统索引和任何上游/下游 GDD
2. 运行技术可行性预检查（领域映射 + 可行性简报）
3. 一次一个地引导你完成 8 个必需的 GDD 部分
4. 每个部分遵循：背景 > 问题 > 选项 > 决策 > 草稿 > 批准 > 写入
5. 每个部分在批准后立即写入文件（崩溃后可存活）
6. 标记与现有已批准 GDD 的冲突
7. 按类别路由到专业 agent（systems-designer 用于数学、economy-designer 用于经济、narrative-director 用于故事系统）

**8 个必需的 GDD 部分：**

| # | 部分 | 这里包含什么 |
|---|---------|---------------|
| 1 | **概述** | 系统的一段落摘要 |
| 2 | **玩家幻想** | 玩家在使用此系统时想象/感觉到什么 |
| 3 | **详细规则** | 明确的机械规则 |
| 4 | **公式** | 每个计算，含变量定义和范围 |
| 5 | **边界情况** | 在怪异情况下会发生什么？明确解决。 |
| 6 | **依赖关系** | 这连接的其他系统（双向） |
| 7 | **调节旋钮** | 设计师可以安全更改的值，含安全范围 |
| 8 | **验收标准** | 你如何测试这个工作？具体、可衡量。 |

外加一个**游戏感觉**部分：感觉参考、输入响应性（毫秒/帧）、动画感觉目标（启动/活动/恢复）、冲击时刻、重量档案。

### 第 2.2 步：审查每个 GDD

在下一个系统开始之前，验证当前系统的：

```
/design-review design/gdd/combat-system.md
```

检查所有 8 个部分的完整性、公式清晰度、边界情况解决、双向依赖关系和可测试的验收标准。

**裁决：** 批准 / 需要修改 / 重大修改。只有批准的 GDD 才能继续。

### 第 2.3 步：小型变更不需要完整 GDD

对于调整变更、小补充或不需要完整 GDD 的调整：

```
/quick-design "为侧翼攻击添加 10% 伤害加成"
```

这在 `design/quick-specs/` 中创建一个轻量级规范，而不是完整的 8 部分 GDD。用于调整、数字变更和小补充。

### 第 2.4 步：跨 GDD 一致性审查

在所有 MVP 系统 GDD 单独批准后：

```
/review-all-gdds
```

这会同时读取所有 GDD 并运行两个分析阶段：

**阶段 1 -- 跨 GDD 一致性：**
- 依赖双向性（A 引用 B，B 是否引用 A？）
- 系统间的规则矛盾
- 对重命名或移除系统的过时引用
- 所有权冲突（两个系统声称同一职责）
- 公式范围兼容性（系统 A 的输出是否适合系统 B 的输入？）
- 验收标准交叉检查

**阶段 2 -- 设计理论（游戏设计整体性）：**
- 竞争性进度循环（两个系统是否争夺同一奖励空间？）
- 认知负荷（同时超过 4 个活动系统？）
- 主导策略（使所有其他策略无关的一个方法）
- 经济循环分析（来源和消耗是否平衡？）
- 跨系统的难度曲线一致性
- 支柱对齐性和反支柱违反
- 玩家幻想连贯性

**输出：** `design/gdd/gdd-cross-review-[日期].md`，带有裁决。

### 第 2.5 步：叙事设计（如果适用）

如果你的游戏有故事、世界观或对话，这就是你构建它的时候：

1. **世界构建** -- 使用 `world-builder` 定义派系、历史、地理和世界规则
2. **故事结构** -- 使用 `narrative-director` 设计故事弧、角色弧和叙事节拍
3. **角色表** -- 使用 `narrative-character-sheet.md` 模板

### 阶段 2 关卡

```
/gate-check systems-design
```

**通过要求：**

- `systems-index.md` 中的所有 MVP 系统都已 `状态：已批准`
- 每个 MVP 系统都有一个已审查的 GDD
- 跨 GDD 审查报告存在（`design/gdd/gdd-cross-review-*.md`）
  裁决为通过或关注（非失败）

---

## 阶段 3：技术设置

### 此阶段发生什么

你做出关键技术决策，将它们记录为架构决策记录（ADR），通过审查验证它们，并生成一个给程序员扁平、可操作规则的控制清单。你还建立 UX 基础。

### 阶段 3 管线

```
/create-architecture  -->  /architecture-decision（×N）-->  /architecture-review
        |                          |                                   |
        v                          v                                   v
  主架构文档涵盖               每决策 ADR                    验证完整性、
  所有系统                     在 docs/architecture/          依赖顺序、
                                adr-*.md                      引擎兼容性
                                                                      |
                                                                      v
                                                         /create-control-manifest
                                                                      |
                                                                      v
                                                         扁平程序员规则
                                                         docs/architecture/
                                                         control-manifest.md
        同样在此阶段：
        -------------------
        /ux-design  -->  /ux-review
        可访问性需求文档
        交互模式库
```

### 第 3.1 步：主架构文档

```
/create-architecture
```

在 `docs/architecture/architecture.md` 中创建总括架构文档，覆盖系统边界、数据流和集成点。

### 第 3.2 步：架构决策记录（ADR）

为每个重要的技术决策：

```
/architecture-decision "用于 NPC AI 的状态机 vs 行为树"
```

**发生了什么：** Skill 引导你创建 ADR，包含：
- 背景和决策驱动因素
- 所有选项，含优缺点和引擎兼容性
- 选定的选项及理由
- 后果（积极、消极、风险）
- 依赖关系（依赖于、启用、阻止、排序说明）
- 解决的 GDD 需求（通过 TR-ID 链接）

ADR 经历一个生命周期：已提议 > 已接受 > 已取代/已弃用。

**关卡检查前至少需要 3 个基础层 ADR。**

**改造现有 ADR：** 如果你已经有棕地项目的 ADR：

```
/architecture-decision retrofit docs/architecture/adr-005.md
```

这检测模板的哪些部分缺失并仅添加那些部分，从不覆盖现有内容。

### 第 3.3 步：架构审查

```
/architecture-review
```

验证所有 ADR 一起：
- ADR 依赖的拓扑排序（检测循环）
- 引擎兼容性验证
- GDD 修订标记（标记基于 ADR 选择需要更新的 GDD 部分）
- TR-ID 注册表维护（`docs/architecture/tr-registry.yaml`）

### 第 3.4 步：控制清单

```
/create-control-manifest
```

获取所有已接受的 ADR 并生成一个扁平的程序员规则表：

```
docs/architecture/control-manifest.md
```

这包含必需的、禁止的模式和护栏，按代码层组织。之后创建的 story 嵌入清单版本日期，以便可以检测过时。

### 第 3.5 步：可访问性需求

使用模板创建 `design/accessibility-requirements.md`。承诺一个层级（基本 / 标准 / 全面 / 示范）并填写 4 轴特性矩阵（视觉、运动、认知、听觉）。

本文档在阶段 3 中是必需的，因为 UX 规范（阶段 4 中编写）引用此层级——这是设计的前提条件，而不是 UX 的交付物。

### 阶段 3 关卡

```
/gate-check technical-setup
```

**通过要求：**

- `docs/architecture/architecture.md` 存在
- 至少存在 3 个 ADR 且已接受
- 架构审查报告存在
- `docs/architecture/control-manifest.md` 存在
- `design/accessibility-requirements.md` 存在

---

## 阶段 4：预生产

### 此阶段发生什么

你为关键屏幕创建 UX 规范，对危险机制进行原型设计，将设计文档转化为可实现的 story，计划你的第一次冲刺，并构建一个证明核心循环有趣的垂直切片。

### 阶段 4 管线

```
/ux-design  -->  /vertical-slice  -->  /create-epics  -->  /create-stories  -->  /sprint-plan
    |                   |                   |                   |                       |
    v                   v                   v                   v                       v
  UX 规范        生产质量端到端      Epic 文件在          Story 文件在            带优先级的
 design/ux/      构建在              production/          production/              首个冲刺
                 prototypes/         epics/*/EPIC.md      epics/*/story-*.md       production/sprints/
                 继续/转向/终止      （每个模块一个）     （每个行为一个）         sprint-*.md
    |                                                          |
    v                                                          v
 /ux-review                                             /story-readiness
（在 epic 前验证                                      （在拾取前验证
 规范）                                                每个 story）
                                                               |
                                                               v
                                                           /dev-story
                                                         （实现 story，
                                                          路由到正确的 agent）
```

### 第 4.1 步：关键屏幕的 UX 规范

在编写 epic 之前，创建 UX 规范，以便 story 作者知道存在哪些屏幕以及它们必须支持哪些玩家交互。

**UX 规范：**

```
/ux-design main-menu
/ux-design core-gameplay-hud
```

三种模式：屏幕/流程、HUD 和交互模式。输出到 `design/ux/`。每个规范包括：玩家需求、布局区域、状态、交互图、数据需求、触发事件、可访问性、本地化。

读取你的 `accessibility-requirements.md`（阶段 3 中编写）和你的输入方法配置（来自 `technical-preferences.md`）以驱动可访问性和输入覆盖检查——无需在每个屏幕中重新指定它们。

> **提示：** `/design-system` 为每个有 UI 需求的系统发射 📌 UX 标记。使用这些标记作为需要规范的屏幕的清单。

**交互模式库：**

```
/ux-design interaction-patterns
```

创建 `design/ux/interaction-patterns.md` — 16 个标准控件加上游戏特定模式（库存栏位、能力图标、HUD 条、对话框等），含动画和声音标准。

**UX 审查：**

```
/ux-review all
```

验证 UX 规范的 GDD 对齐性和可访问性层级合规性。产生批准 / 需要修改 / 需要重大修改裁决。

### 第 4.2 步：构建垂直切片

垂直切片是在承诺完整生产之前，你可以端到端构建完整游戏循环的生产质量证明。

```
/vertical-slice
```

**它证明什么：** 一个从零开始的玩家是否在几分钟内体验到核心幻想，而不需要开发者指导？

**它构建什么：** 一个接近生产质量的可玩构建，覆盖至少一个完整的 [开始 → 挑战 → 解决] 循环。使用真实的架构层、真实的命名约定、没有硬编码值——但非最终的美术或音频。这不像概念原型那样是一次性的；它演示了生产管线的可行性。

**关于概念原型的注意：** 如果你在阶段 1（概念）中运行了 `/prototype`，你已经验证了核心想法是有趣的。现在垂直切片验证你可以正确构建它。它们回答不同的问题。如果你跳过了概念原型，现在是在投资完整切片之前先运行一个的合理时机。

**裁决：** 垂直切片产生继续 / 转向 / 终止裁决。
- **继续** → 移动到第 4.3 步（epic 和 story）
- **转向** → 用 `/design-system [机制]` 修改受影响的 GDD，然后重新运行 `/vertical-slice`
- **终止** → 用你学到的内容返回到 `/brainstorm`

### 第 4.3 步：从设计工件创建 Epic 和 Story

```
/create-epics layer: foundation
/create-stories [epic-slug]   # 对每个 epic 重复
/create-epics layer: core
/create-stories [epic-slug]   # 对每个核心 epic 重复
```

`/create-epics` 读取你的 GDD、ADR 和架构以定义 epic 范围——每个架构模块一个 epic。然后 `/create-stories` 将每个 epic 分解为可实现的 story 文件，位于 `production/epics/[slug]/`。每个 story 嵌入：
- GDD 需求引用（TR-ID，而非引用文本——保持新鲜）
- ADR 引用（仅来自已接受的 ADR；提议的 ADR 导致 `状态：已阻止`）
- 控制清单版本日期（用于过时检测）
- 引擎特定的实现说明
- 来自 GDD 的验收标准

一旦 story 存在，运行 `/dev-story [story-path]` 实现一个——它自动路由到正确的程序员 agent。

### 第 4.4 步：在拾取前验证 Story

```
/story-readiness production/epics/combat/story-combat-damage-calc.md
```

检查：设计完整性、架构覆盖、范围清晰度、完成定义。裁决：就绪 / 需要工作 / 已阻止。

### 第 4.5 步：工作量评估

```
/estimate production/epics/combat/story-combat-damage-calc.md
```

提供带风险评估的工作量估算。

### 第 4.6 步：计划你的第一次冲刺

```
/sprint-plan new
```

**发生了什么：** `producer` agent 协作冲刺计划：
- 询问冲刺目标和可用时间
- 将目标分解为必须拥有 / 应该拥有 / 可以拥有的任务
- 识别风险和阻止因素
- 创建 `production/sprints/sprint-01.md`
- 填充 `production/sprint-status.yaml`（机器可读的 story 跟踪）

### 第 4.7 步：垂直切片（硬关卡）

在进入生产之前，你必须构建并游戏测试一个垂直切片：

- 一个完整的端到端核心循环，从头到尾可玩
- 代表性质量（不是一切用占位符）
- 至少在 3 个会话中无人引导地玩过
- 游戏测试报告已写入（`/playtest-report`）

这是一个**硬关卡**——如果人类没有无人引导地玩过构建，`/gate-check` 将自动失败。

### 阶段 4 关卡

```
/gate-check pre-production
```

**通过要求：**

- 至少在 `design/ux/` 中审查了 1 个 UX 规范
- UX 审查已完成（批准或需要修改，含记录的风险）
- 至少 1 个原型带 README
- Story 文件存在于 `production/epics/[epic-slug]/`
- 至少存在 1 个冲刺计划
- 至少存在 1 个游戏测试报告（垂直切片在 3+ 个会话中玩过）

---

## 阶段 5：生产

### 此阶段发生什么

这是核心生产循环。你按冲刺工作（通常 1-2 周），逐个 story 实现特性，跟踪进度，并通过结构化的完成审查关闭 story。此阶段重复直到你的游戏内容完成。

### 阶段 5 管线（每冲刺）

```
/sprint-plan new  -->  /story-readiness  -->  实现  -->  /story-done
       |                     |                    |                |
       v                     v                    v                v
 冲刺已创建             Story 已验证          代码已写入         8 阶段审查：
 sprint-status.yaml     就绪裁决              测试通过           验证标准、
 已填充                                                          检查偏差、
                                                                 更新 story 状态
       |
       |  （为每个 story 重复直到冲刺完成）
       v
  /sprint-status  （任何时候的快速 30 行快照）
  /scope-check    （如果范围正在增长）
  /retrospective  （冲刺结束时）
```

### 第 5.1 步：Story 生命周期

生产阶段围绕 **story 生命周期**：

```
/story-readiness  -->  实现  -->  /story-done  -->  下一个 story
```

**1. Story 就绪检查：** 在拾取 story 之前，验证它：

```
/story-readiness production/epics/combat/story-combat-damage-calc.md
```

这检查设计完整性、架构覆盖、ADR 状态（如果 ADR 仍为 Proposed 则阻止）、控制清单版本（如果过时则警告）和范围清晰度。裁决：就绪 / 需要工作 / 已阻止。

**2. 实现：** 与适当的 agent 合作：

- `gameplay-programmer` 用于游戏系统
- `engine-programmer` 用于核心引擎工作
- `ai-programmer` 用于 AI 行为
- `network-programmer` 用于多人游戏
- `ui-programmer` 用于 UI 代码
- `tools-programmer` 用于开发工具

所有 agent 遵循协作协议：他们阅读设计文档，问澄清性问题，呈现架构选项，获得你的批准，然后实现。

**3. Story 完成：** 当 story 完成时：

```
/story-done production/epics/combat/story-combat-damage-calc.md
```

这运行一个 8 阶段完成审查：
1. 查找并读取 story 文件
2. 加载引用的 GDD、ADR 和控制清单
3. 验证验收标准（自动可检查、手动、延迟）
4. 检查 GDD/ADR 偏差（阻止 / 建议 / 范围外）
5. 提示代码审查
6. 生成完成报告（完成 / 带备注完成 / 已阻止）
7. 更新 story `状态：完成` 并附完成备注
8. 浮现下一个就绪的 story

审查期间发现的技术债务记录到 `docs/tech-debt-register.md`。

### 第 5.2 步：冲刺跟踪

随时检查进度：

```
/sprint-status
```

从 `production/sprint-status.yaml` 读取的快速 30 行快照。

如果范围在增长：

```
/scope-check production/sprints/sprint-03.md
```

这将当前范围与原始计划比较，标记范围增加，推荐削减。

### 第 5.3 步：内容跟踪

```
/content-audit
```

比较 GDD 指定的内容与已实现的内容。尽早捕获内容缺口。

### 第 5.4 步：设计变更传播

当 GDD 在 story 创建后更改时：

```
/propagate-design-change design/gdd/combat-system.md
```

对 GDD 进行 git-diff，找到受影响的 ADR，生成影响报告，并引导你完成已取代/更新/保留决策。

### 第 5.5 步：多系统特性（团队编排）

对于跨越多个领域的特性，使用团队 skill：

```
/team-combat "带 HoT 和净化的治疗能力"
/team-narrative "第 2 幕故事内容"
/team-ui "库存屏幕重新设计"
/team-level "森林地下城关卡"
/team-audio "战斗音频通道"
```

每个团队 skill 协调一个 6 阶段协作工作流：
1. **设计** -- game-designer 提问、呈现选项
2. **架构** -- lead-programmer 提出代码结构
3. **并行实现** -- 专业人员同时工作
4. **集成** -- gameplay-programmer 将一切连接起来
5. **验证** -- qa-tester 对照验收标准运行
6. **报告** -- 协调员总结状态

编排是自动化的，但**决策点仍然在你手中**。

### 第 5.6 步：冲刺审查和下一个冲刺

在冲刺结束时：

```
/retrospective
```

分析计划 vs. 完成、速度、阻止因素和可操作的改进。

然后计划下一个冲刺：

```
/sprint-plan new
```

### 第 5.7 步：里程碑审查

在里程碑检查点：

```
/milestone-review "alpha"
```

生成特性完成度、质量指标、风险评估和 go/no-go 建议。

### 阶段 5 关卡

```
/gate-check production
```

**通过要求：**

- 所有 MVP story 完成
- 游戏测试：3 个会话，覆盖新玩家、游戏中期和难度曲线
- 乐趣假设已验证
- 游戏测试数据中没有困惑循环

---

## 阶段 6：打磨

### 此阶段发生什么

你的游戏特性已完成。现在你让它变得好。此阶段专注于性能、平衡、可访问性、音频、视觉打磨和游戏测试。

### 阶段 6 管线

```
/perf-profile  -->  /balance-check  -->  /asset-audit  -->  /playtest-report（×3）
       |                  |                    |                    |
       v                  v                    v                    v
 分析 CPU/GPU        分析公式和数据             验证命名、           覆盖：新玩家、
 内存、优化          是否有断开的                格式、大小         游戏中期、难度
 瓶颈                进度                                                    曲线

  /tech-debt  -->  /team-polish
       |                |
       v                v
 跟踪并             协调通道：
 优先排序           性能 + 美术 +
 债务项             音频 + UX + QA
```

### 第 6.1 步：性能分析

```
/perf-profile
```

引导你完成结构化性能分析：
- 建立目标（FPS、内存、平台）
- 按影响排序识别瓶颈
- 生成可操作的优化任务，含代码位置和预期收益

### 第 6.2 步：平衡分析

```
/balance-check assets/data/combat_damage.json
```

分析平衡数据的统计异常值、断开的进度曲线、退化策略和经济不平衡。

### 第 6.3 步：资源审计

```
/asset-audit
```

验证所有资源的命名约定、文件格式标准和大小预算。

### 第 6.4 步：游戏测试（必需：3 个会话）

```
/playtest-report
```

生成结构化的游戏测试报告。需要三个会话，覆盖：
- 新玩家体验
- 游戏中期系统
- 难度曲线

### 第 6.5 步：技术债务评估

```
/tech-debt
```

扫描 TODO/FIXME/HACK 注释、代码重复、过于复杂的函数、缺失的测试和过时的依赖。每个项被分类和优先排序。

### 第 6.6 步：协调的打磨通道

```
/team-polish "combat system"
```

并行协调 4 个专业人员：
1. 性能优化（performance-analyst）
2. 视觉打磨（technical-artist）
3. 音频打磨（sound-designer）
4. 感觉/润色（gameplay-programmer + technical-artist）

你设定优先级；团队在你的批准下在每个步骤执行。

### 第 6.7 步：本地化和可访问性

```
/localize src/
```

扫描硬编码字符串、破坏翻译的连接、未考虑扩展的文本和缺失的语言环境文件。

可访问性根据阶段 3 的可访问性需求文档中承诺的层级进行审计。

### 阶段 6 关卡

```
/gate-check polish
```

**通过要求：**

- 至少存在 3 个游戏测试报告
- 协调的打磨通道已完成（`/team-polish`）
- 无阻止性性能问题
- 可访问性层级要求已满足

---

## 阶段 7：发布

### 此阶段发生什么

你的游戏已打磨、测试并就绪。现在你发布它。

### 阶段 7 管线

```
/release-checklist  -->  /launch-checklist  -->  /team-release
        |                       |                      |
        v                       v                      v
  发布前验证              完整跨部门                协调：
  跨代码、内容、          验证（每部门               构建、QA 签核、
  商店、法律              的 Go/No-Go               部署、发布
                    同样：/changelog、/patch-notes、/hotfix
```

### 第 7.1 步：发布清单

```
/release-checklist v1.0.0
```

生成全面的发布前清单，涵盖：
- 构建验证（所有平台编译和运行）
- 认证要求（平台特定）
- 商店元数据（描述、截图、预告片）
- 法律合规（EULA、隐私政策、评级）
- 存档游戏兼容性
- 分析验证

### 第 7.2 步：发布就绪（完整验证）

```
/launch-checklist
```

完整的跨部门验证：

| 部门 | 检查内容 |
|-----------|---------------|
| **工程** | 构建稳定性、崩溃率、内存泄漏、加载时间 |
| **设计** | 特性完整性、教程流程、难度曲线 |
| **美术** | 资源质量、缺失纹理、LOD 级别 |
| **音频** | 缺失声音、混音级别、空间音频 |
| **QA** | 按严重性的未结错误计数、回归套件通过率 |
| **叙事** | 对话完整性、世界观一致性、错别字 |
| **本地化** | 所有字符串已翻译、无截断、语言环境测试 |
| **可访问性** | 合规清单、辅助功能测试 |
| **商店** | 元数据完整、截图已批准、定价已设置 |
| **营销** | 媒体资料包就绪、发布预告片、社交媒体已安排 |
| **社区** | 补丁说明草稿、FAQ 已准备、支持通道就绪 |
| **基础设施** | 服务器已扩展、CDN 已配置、监控活跃 |
| **法律** | EULA 已定稿、隐私政策、COPPA/GDPR 合规 |

每个项获得 **Go / No-Go** 状态。全部必须是 Go 才能发布。

### 第 7.3 步：生成面向玩家的内容

```
/patch-notes v1.0.0
```

从 git 历史和冲刺数据生成面向玩家的补丁说明。将开发者语言翻译为玩家语言。

```
/changelog v1.0.0
```

生成内部变更日志（更技术性，面向团队）。

### 第 7.4 步：协调发布

```
/team-release
```

协调 release-manager、QA 和 DevOps 通过：
1. 发布前验证
2. 构建管理
3. 最终 QA 签核
4. 部署准备
5. Go/No-Go 决策

### 第 7.5 步：发布

`validate-push` 钩子会在推送到 `main` 或 `develop` 时警告你。这是故意的——发布推送应经过深思熟虑：

```bash
git tag v1.0.0
git push origin main --tags
```

### 第 7.6 步：发布后

**热修复工作流**用于关键生产错误：

```
/hotfix "当库存超过 99 项时玩家丢失存档数据"
```

绕过正常的冲刺流程，具有完整的审计追踪：
1. 创建热修复分支
2. 实现修复
3. 确保反向移植到开发分支
4. 记录事件

**发布稳定后的事后分析：**

```
要求 Claude 使用模板创建事后分析
.claude/docs/templates/post-mortem.md
```

---

## 横切关注点

这些主题适用于所有阶段。

### 导演审查模式

导演关卡是专业 agent，在关键工作流步骤审查你的工作。默认情况下它们在每个检查点运行。你可以控制你得到的审查量。

**在 `/start` 期间设置一次你的审查强度。** 保存到 `production/review-mode.txt`。

| 模式 | 运行内容 | 最适合 |
|------|-----------|----------|
| `full` | 每一步的所有导演关卡 | 新项目、学习系统 |
| `lean` | 导演仅在阶段过渡时（`/gate-check`） | 有经验的开发者 |
| `solo` | 无导演审查 | Game Jam、原型、最大速度 |

**为单次运行覆盖**而不更改全局设置：

```
/brainstorm space horror --review full
/architecture-decision --review solo
```

`--review` 标志适用于所有使用关卡的 skill。随时通过直接编辑 `production/review-mode.txt` 或重新运行 `/start` 更改全局模式。

完整的关卡定义和检查模式：`.claude/docs/director-gates.md`

---

### 协作协议

此系统是**用户驱动的协作**，不是自主的。

**模式：** 提问 > 选项 > 决策 > 草稿 > 批准

每次 agent 交互遵循此模式：
1. Agent 提出澄清性问题
2. Agent 呈现 2-4 个选项，含权衡和推理
3. 你决定
4. Agent 基于你的决策起草
5. 你审查和完善
6. Agent 在写入前问"我可以写入此文件路径吗？"

参见 `docs/COLLABORATIVE-DESIGN-PRINCIPLE.md` 获取完整协议及示例。

### AskUserQuestion 工具

Agent 使用 `AskUserQuestion` 工具进行结构化选项呈现。模式是先解释后捕获：先在对话文本中完整分析，然后为决策提供一个干净的 UI 选择器。用于设计选择、架构决策和战略问题。不要用于开放式发现问题或简单的是/否确认。

### Agent 协调（3 层层级）

```
第 1 层（导演层）：    creative-director、technical-director、producer
                                          |
第 2 层（主管层）：    game-designer、lead-programmer、art-director、
                       audio-director、narrative-director、qa-lead、
                       release-manager、localization-lead
                                          |
第 3 层（专业层）：    gameplay-programmer、engine-programmer、
                       ai-programmer、network-programmer、ui-programmer、
                       tools-programmer、systems-designer、level-designer、
                       economy-designer、world-builder、writer、
                       technical-artist、sound-designer、ux-designer、
                       qa-tester、performance-analyst、devops-engineer、
                       analytics-engineer、accessibility-specialist、
                       live-ops-designer、prototyper、security-engineer、
                       community-manager、godot-specialist、
                       godot-gdscript-specialist、godot-shader-specialist、
                       godot-csharp-specialist、godot-gdextension-specialist、
                       unity-specialist、unity-dots-specialist、
                       unity-shader-specialist、unity-addressables-specialist、
                       unity-ui-specialist、unreal-specialist、
                       ue-blueprint-specialist、ue-gas-specialist、
                       ue-replication-specialist、ue-umg-specialist
```

**协调规则：**
- 垂直委托：导演 > 主管 > 专业人员。对于复杂决策，切勿跳过层级。
- 水平咨询：同层级的 agent 可以相互咨询，但不能在其领域之外做出有约束力的决策。
- 冲突解决：设计冲突升级到 `creative-director`。技术冲突升级到 `technical-director`。范围冲突升级到 `producer`。
- 无单方面跨域更改。

### 自动化钩子（安全网）

系统有 12 个自动运行的钩子：

| 钩子 | 触发 | 作用 |
|------|---------|-------------|
| `session-start.sh` | 会话开始 | 显示分支、最近提交、检测 active.md 用于恢复 |
| `detect-gaps.sh` | 会话开始 | 检测新项目（无引擎、无概念）并建议 `/start` |
| `pre-compact.sh` | 压缩前 | 将会话状态转储到对话中用于自动恢复 |
| `post-compact.sh` | 压缩后 | 提醒 Claude 从 `active.md` 恢复会话状态 |
| `notify.sh` | 通知事件 | 通过 PowerShell 显示 Windows 通知 |
| `validate-commit.sh` | 提交前 | 检查设计文档引用、有效 JSON、无硬编码值 |
| `validate-push.sh` | 推送前 | 在推送到 main/develop 时警告 |
| `validate-assets.sh` | 提交前 | 检查资源命名和大小 |
| `validate-skill-change.sh` | Skill 文件写入 | 在 `.claude/skills/` 更改后建议运行 `/skill-test` |
| `log-agent.sh` | Agent 开始 | 记录 agent 调用用于审计追踪 |
| `log-agent-stop.sh` | Agent 停止 | 完成 agent 审计追踪（开始 + 停止） |
| `session-stop.sh` | 会话结束 | 最终会话日志 |

### 上下文弹性

**会话状态文件：** `production/session-state/active.md` 是一个活的检查点。在每个重要里程碑后更新它。在任何中断后（压缩、崩溃、`/clear`），先读取此文件。

**增量写入：** 创建多章节文档时，在批准后立即将每个章节写入文件。这意味着已完成的章节在崩溃和上下文压缩后仍然存在。关于已写入章节的先前讨论可以安全地压缩。

**自动恢复：** `session-start.sh` 钩子自动检测并预览 `active.md`。`pre-compact.sh` 钩子在压缩前将状态转储到对话中。

**冲刺状态跟踪：** `production/sprint-status.yaml` 是机器可读的 story 跟踪器。由 `/sprint-plan`（初始化）和 `/story-done`（状态更新）写入。由 `/sprint-status`、`/help` 和 `/story-done`（下一个 story）读取。消除了脆弱的 markdown 扫描。

### 棕地采用

对于已经有某些工件的现有项目：

```
/adopt
```

或针对性：

```
/adopt gdds
/adopt adrs
/adopt stories
/adopt infra
```

这审计现有工件的**格式**（而非存在性），将缺口分类为 BLOCKING/HIGH/MEDIUM/LOW，构建有序的迁移计划，并写入 `docs/adoption-plan-[日期].md`。核心原则：迁移而非替换——它从不重新生成现有工作，仅填补缺口。

单个 skill 也支持改造模式：

```
/design-system retrofit design/gdd/combat-system.md
/architecture-decision retrofit docs/architecture/adr-005.md
```

这些检测哪些部分存在与缺失，并仅填补缺口。

### 关卡系统

阶段关卡是正式的检查点。运行 `/gate-check` 并带上过渡名称：

```
/gate-check concept              # 概念 -> 系统设计
/gate-check systems-design       # 系统设计 -> 技术设置
/gate-check technical-setup      # 技术设置 -> 预生产
/gate-check pre-production       # 预生产 -> 生产
/gate-check production           # 生产 -> 打磨
/gate-check polish               # 打磨 -> 发布
```

**裁决：**
- **通过** -- 所有要求满足，前进到下一阶段
- **关注** -- 要求满足，但确认了风险，可通过
- **失败** -- 要求未满足，阻止前进，带有具体修复措施

当关卡通过时，`production/stage.txt` 被更新（仅那时），这控制状态行和 `/help` 行为。

### 反向文档

对于存在的没有设计文档的代码（在棕地采用后很常见）：

```
/reverse-document src/gameplay/combat/
```

读取现有代码并从中生成 GDD 格式的设计文档。

---

## 附录 A：Agent 快速参考

### "我需要做 X —— 使用哪个 agent？"

| 我需要... | Agent | 层级 |
|-------------|-------|------|
| 想出一个游戏点子 | `/brainstorm` skill | -- |
| 设计一个游戏机制 | `game-designer` | 2 |
| 设计特定公式/数字 | `systems-designer` | 3 |
| 设计一个游戏关卡 | `level-designer` | 3 |
| 设计战利品表/经济 | `economy-designer` | 3 |
| 构建世界世界观 | `world-builder` | 3 |
| 写对话 | `writer` | 3 |
| 计划故事 | `narrative-director` | 2 |
| 计划一个冲刺 | `producer` | 1 |
| 做一个创意决策 | `creative-director` | 1 |
| 做一个技术决策 | `technical-director` | 1 |
| 实现游戏代码 | `gameplay-programmer` | 3 |
| 实现核心引擎系统 | `engine-programmer` | 3 |
| 实现 AI 行为 | `ai-programmer` | 3 |
| 实现多人游戏 | `network-programmer` | 3 |
| 实现 UI | `ui-programmer` | 3 |
| 构建开发工具 | `tools-programmer` | 3 |
| 审查代码架构 | `lead-programmer` | 2 |
| 创建着色器/VFX | `technical-artist` | 3 |
| 定义视觉风格 | `art-director` | 2 |
| 定义音频风格 | `audio-director` | 2 |
| 设计音效 | `sound-designer` | 3 |
| 设计 UX 流程 | `ux-designer` | 3 |
| 编写测试用例 | `qa-tester` | 3 |
| 计划测试策略 | `qa-lead` | 2 |
| 分析性能 | `performance-analyst` | 3 |
| 设置 CI/CD | `devops-engineer` | 3 |
| 设计分析 | `analytics-engineer` | 3 |
| 检查可访问性 | `accessibility-specialist` | 3 |
| 计划持续运营 | `live-ops-designer` | 3 |
| 管理发布 | `release-manager` | 2 |
| 管理本地化 | `localization-lead` | 2 |
| 快速原型 | `prototyper` | 3 |
| 审计安全 | `security-engineer` | 3 |
| 与玩家沟通 | `community-manager` | 3 |
| Godot 特定帮助 | `godot-specialist` | 3 |
| GDScript 特定帮助 | `godot-gdscript-specialist` | 3 |
| Godot 着色器帮助 | `godot-shader-specialist` | 3 |
| GDExtension 模块 | `godot-gdextension-specialist` | 3 |
| Unity 特定帮助 | `unity-specialist` | 3 |
| Unity DOTS/ECS | `unity-dots-specialist` | 3 |
| Unity 着色器/VFX | `unity-shader-specialist` | 3 |
| Unity Addressables | `unity-addressables-specialist` | 3 |
| Unity UI Toolkit | `unity-ui-specialist` | 3 |
| Unreal 特定帮助 | `unreal-specialist` | 3 |
| Unreal GAS | `ue-gas-specialist` | 3 |
| Unreal Blueprints | `ue-blueprint-specialist` | 3 |
| Unreal 复制 | `ue-replication-specialist` | 3 |
| Unreal UMG/CommonUI | `ue-umg-specialist` | 3 |

### Agent 层级

```
                    creative-director / technical-director / producer
                                         |
          ---------------------------------------------------------------
          |            |           |           |          |        |       |
    game-designer  lead-prog  art-dir  audio-dir  narr-dir  qa-lead  release-mgr
          |            |           |           |          |        |        |
     专业人员     程序员     技术美术   音效设计   作家    qa-tester  devops
     (systems,    (gameplay,             (sound)     (world-  (perf,     (analytics,
      economy,     engine,                            builder)  access.)   security)
      level)       ai, net,
                   ui, tools)
```

**升级规则：** 如果两个 agent 意见不一致，向上升级。设计冲突升级到 `creative-director`。技术冲突升级到 `technical-director`。范围冲突升级到 `producer`。

---

## 附录 B：斜杠命令快速参考

### 按类别的所有 73 个命令

#### 入职和导航（6）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/start` | 引导式入职，路由到正确工作流 | 任意（首次会话） |
| `/help` | 上下文感知的"我下一步做什么？" | 任意 |
| `/project-stage-detect` | 完整的项目审计以确定当前阶段 | 任意 |
| `/setup-engine` | 配置引擎、固定版本、设置偏好 | 1 |
| `/adopt` | 棕地审计和迁移计划 | 任意（现有项目） |
| `/skill-improve` | 通过测试-修复-再测试循环改进 skill | 任意 |

#### 游戏设计（6）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/brainstorm` | 带 MDA 分析的协作构思 | 1 |
| `/map-systems` | 将概念分解为系统索引 | 1-2 |
| `/design-system` | 引导式逐部分 GDD 创作 | 2 |
| `/quick-design` | 小型变更的轻量级规范 | 2+ |
| `/review-all-gdds` | 跨 GDD 一致性和设计理论审查 | 2 |
| `/propagate-design-change` | 查找受 GDD 变更影响的 ADR/story | 5 |

#### UX 和界面（2）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/ux-design` | 编写 UX 规范（屏幕/流程、HUD、模式） | 4 |
| `/ux-review` | 验证 UX 规范的可访问性和 GDD 对齐性 | 4 |

#### 架构（4）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/create-architecture` | 主架构文档 | 3 |
| `/architecture-decision` | 创建或改造 ADR | 3 |
| `/architecture-review` | 验证所有 ADR、依赖顺序 | 3 |
| `/create-control-manifest` | 来自已接受 ADR 的扁平程序员规则 | 3 |

#### Story 和冲刺（8）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/create-epics` | 将 GDD + ADR 翻译为 epic（每个模块一个） | 4 |
| `/create-stories` | 将单个 epic 分解为 story 文件 | 4 |
| `/dev-story` | 实现 story — 路由到正确的程序员 agent | 5 |
| `/sprint-plan` | 创建或管理冲刺计划 | 4-5 |
| `/sprint-status` | 快速 30 行冲刺快照 | 5 |
| `/story-readiness` | 验证 story 已准备好实现 | 4-5 |
| `/story-done` | 8 阶段 story 完成审查 | 5 |
| `/estimate` | 带风险评估的工作量估算 | 4-5 |

#### 审查和分析（13）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/design-review` | 验证 GDD 的 8 部分标准 | 1-2 |
| `/code-review` | 架构代码审查 | 5+ |
| `/balance-check` | 游戏平衡公式分析 | 5-6 |
| `/asset-audit` | 资源命名、格式、大小验证 | 6 |
| `/asset-spec` | 每资源视觉规范和 AI 生成提示 | 5-6 |
| `/content-audit` | GDD 指定内容 vs. 已实现 | 5 |
| `/consistency-check` | 跨 GDD 实体和公式不一致扫描 | 2+ |
| `/scope-check` | 范围蔓延检测 | 5 |
| `/perf-profile` | 性能分析工作流 | 6 |
| `/tech-debt` | 技术债务扫描和优先排序 | 6 |
| `/gate-check` | 带通过/关注/失败裁决的正式阶段关卡 | 所有过渡 |
| `/reverse-document` | 从现有代码创建设计文档 | 任意 |
| `/security-audit` | 安全漏洞审计（存档、网络、输入） | 6-7 |

#### QA 和测试（9）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/qa-plan` | 为冲刺或特性生成 QA 测试计划 | 5 |
| `/smoke-check` | QA 交接前的关键路径冒烟测试关卡 | 5-6 |
| `/soak-test` | 扩展游戏会话的浸泡测试协议 | 6 |
| `/regression-suite` | 映射测试覆盖、识别缺少回归测试的已修复错误 | 5-6 |
| `/test-setup` | 搭建测试框架和 CI/CD 管线 | 4 |
| `/test-helpers` | 生成引擎特定的测试辅助库 | 4-5 |
| `/test-evidence-review` | 测试文件和手动证据的质量审查 | 5 |
| `/test-flakiness` | 从 CI 日志检测非确定性测试 | 5-6 |
| `/skill-test` | 验证 skill 文件的结构和行为正确性 | 任意 |

#### 生产管理（6）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/milestone-review` | 里程碑进度和 go/no-go | 5 |
| `/retrospective` | 冲刺后分析 | 5 |
| `/bug-report` | 结构化错误报告创建 | 5+ |
| `/bug-triage` | 重新评估未结错误的优先级、严重性和负责人 | 5+ |
| `/playtest-report` | 结构化游戏测试会话报告 | 4-6 |
| `/onboard` | 入职新团队成员 | 任意 |

#### 发布（6）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/release-checklist` | 发布前验证 | 7 |
| `/launch-checklist` | 完整跨部门发布就绪检查 | 7 |
| `/changelog` | 自动生成内部变更日志 | 7 |
| `/patch-notes` | 面向玩家的补丁说明 | 7 |
| `/hotfix` | 紧急修复工作流 | 7+ |
| `/day-one-patch` | 金元后发现问题的作用域补丁 | 7+ |

#### 创意（4）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/prototype` | 概念原型 — 在 GDD 前验证核心想法 | 1 |
| `/art-bible` | 引导式 Art Bible 创作 — 视觉身份规范 | 1-2 |
| `/vertical-slice` | 生产前生产质量的端到端构建 | 4 |
| `/localize` | 字符串提取和验证 | 6-7 |

#### 团队编排（9）

| 命令 | 用途 | 阶段 |
|---------|---------|-------|
| `/team-combat` | 战斗特性：从设计到实现 | 5 |
| `/team-narrative` | 叙事内容：从结构到对话 | 5 |
| `/team-ui` | UI 特性：从 UX 规范到打磨实现 | 5 |
| `/team-level` | 关卡：从布局到有布设的遭遇 | 5 |
| `/team-audio` | 音频：从方向到已实现的事件 | 5-6 |
| `/team-polish` | 协调打磨：性能 + 美术 + 音频 + QA | 6 |
| `/team-release` | 发布协调：构建 + QA + 部署 | 7 |
| `/team-live-ops` | 持续运营规划：季节活动、战斗通行证、留存 | 7+ |
| `/team-qa` | 完整 QA 周期：策略、执行、覆盖、签核 | 6-7 |

---

## 附录 C：常见工作流

### 工作流 1："我刚起步，没有游戏想法"

```
1. /start（基于你的位置路由）
2. /brainstorm（协作构思，选择一个概念）
3. /setup-engine（固定引擎和版本）
4. /design-review 概念文档（可选，推荐）
5. /map-systems（将概念分解为带依赖和优先级的系统）
6. /gate-check concept（验证你准备好进入系统设计）
7. /design-system 每个系统（引导式 GDD 创作）
```

### 工作流 2："我有设计，想开始编码"

```
1. 每个 GDD 的 /design-review（确保它们扎实）
2. /review-all-gdds（跨 GDD 一致性）
3. /gate-check systems-design
4. /create-architecture + /architecture-decision（每个主要决策）
5. /architecture-review
6. /create-control-manifest
7. /gate-check technical-setup
8. /create-epics layer: foundation + /create-stories [slug]（定义 epic，分解为 story）
9. /sprint-plan new
10. /story-readiness -> 实现 -> /story-done（story 生命周期）
```

### 工作流 3："我需要在中生产期添加一个复杂特性"

```
1. /design-system 或 /quick-design（取决于范围）
2. /design-review 验证
3. /propagate-design-change 如果修改现有 GDD
4. /estimate 用于工作量和风险
5. /team-combat、/team-narrative、/team-ui 等（适当的团队 skill）
6. /story-done 完成时
7. /balance-check 如果影响游戏平衡
```

### 工作流 4："生产中出现问题"

```
1. /hotfix "问题描述"
2. 修复在热修复分支上实现
3. /code-review 修复
4. 运行测试
5. /release-checklist 用于热修复构建
6. 部署和反向移植
```

### 工作流 5："我有现有项目，想使用此系统"

```
1. /start（选择路径 D -- 现有工作）
2. /project-stage-detect（确定当前阶段）
3. /adopt（审计现有工件，构建迁移计划）
4. /design-system retrofit [路径]（填补 GDD 缺口）
5. /architecture-decision retrofit [路径]（填补 ADR 缺口）
6. /gate-check 在适当的过渡点
```

### 工作流 6："开始一个新的冲刺"

```
1. /retrospective（审查上一个冲刺）
2. /sprint-plan new（创建下一个冲刺）
3. /scope-check（确保范围可管理）
4. 拾取前的 /story-readiness 每个 story
5. 实现 story
6. 每个完成 story 的 /story-done
7. 快速进度检查的 /sprint-status
```

### 工作流 7："发布游戏"

```
1. /gate-check polish（验证打磨阶段完成）
2. /tech-debt（决定发布时可接受的内容）
3. /localize（最终本地化通道）
4. /release-checklist v1.0.0
5. /launch-checklist（完整跨部门验证）
6. /team-release（协调发布）
7. /patch-notes 和 /changelog
8. 发布！
9. 如果发布后出现问题，/hotfix
10. 发布稳定后的事后分析
```

### 工作流 8："我迷路了 / 不知道下一步做什么"

```
1. /help（读取你的阶段，检查工件，告诉你下一步）
2. 如果 /help 无法帮助：/project-stage-detect（完整审计）
3. 如果阶段似乎不对：/gate-check 在你认为的过渡点
```

---

## 充分利用系统的技巧

1. **始终从设计开始，然后实现。** Agent 系统围绕设计文档在代码编写前存在的假设构建。Agent 不断引用 GDD。

2. **在横切特性上使用团队 skill。** 不要试图手动协调 4 个 agent --让 `/team-combat`、`/team-narrative` 等处理编排。

3. **信任规则系统。** 当规则在代码中标记了什么，修复它。规则编码了来之不易的游戏开发智慧（数据驱动值、delta time、可访问性等）。

4. **主动压缩。** 在约 65-70% 上下文使用时，压缩或 `/clear`。预压缩钩子保存你的进度。不要等到你到达极限。

5. **使用正确层级的 agent。** 不要要求 `creative-director` 写着色器。不要要求 `qa-tester` 做设计决策。层级存在是有原因的。

6. **不确定时运行 /help。** 它读取你的实际项目状态，并告诉你单一最重要的下一步。

7. **在设计移交给程序员前运行 `/design-review`。** 这尽早捕获不完整的规范，节省返工。

8. **在每个主要特性后运行 `/code-review`。** 在架构问题传播之前捕获它们。

9. **首先原型化危险机制。** 一天的原型可以节省一周的生产时间来处理一个不起作用的机制。

10. **保持冲刺计划诚实。** 定期使用 `/scope-check`。范围蔓延是独立游戏的第一杀手。

11. **用 ADR 记录决策。** 未来的你会感谢现在的你记录了为什么东西以某种方式构建。

12. **虔诚地遵循 story 生命周期。** 拾取前的 `/story-readiness`、完成后的 `/story-done`。这尽早捕获偏差并使管线保持诚实。

13. **尽早频繁写入文件。** 增量章节写入意味着你的设计决策在崩溃和压缩后仍然存在。文件是记忆，不是对话。
