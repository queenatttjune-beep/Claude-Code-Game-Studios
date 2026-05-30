# 主管关卡 — 共享审查模式

本文档定义了所有工作流阶段中所有主管和负责人审查的标准关卡提示。Skill 通过关卡 ID 引用本文档，而不是内嵌完整的提示文本 — 从而消除需要更新提示时的漂移。

**范围**：所有 7 个生产阶段（概念到发布）、所有 3 个第一层主管、所有关键第二层负责人。任何 Skill、团队编排器或工作流均可调用这些关卡。

---

## 如何使用本文档

在任何 Skill 中，将内联的主管提示替换为引用：

```
通过 Task 使用 .claude/docs/director-gates.md 中的关卡 **CD-PILLARS** 生成 `creative-director`。
```

传递该关卡**要传递的上下文**字段下列出的上下文，然后使用下面的**结论处理**规则处理结论。

---

## 审查模式

审查强度控制主管关卡是否运行。可以在全局设置（跨会话持久化）或按 skill 运行覆盖。

**全局配置**：`production/review-mode.txt` — 一个词：`full`、`lean` 或 `solo`。在 `/start` 期间设置一次。随时直接编辑文件以更改。

**按运行覆盖**：任何使用关卡的 skill 都接受 `--review [full|lean|solo]` 作为参数。这仅覆盖该次运行的全局配置。

示例：
```
/brainstorm space horror           → 使用全局模式
/brainstorm space horror --review full   → 此次运行强制 full 模式
/architecture-decision --review solo     → 此次运行跳过所有关卡
```

| 模式 | 运行内容 | 最佳适用场景 |
|------|-----------|----------|
| `full` | 所有关卡激活 — 每个工作流步骤都被审查 | 团队、学习用户，或当你希望在每一步都获得详尽的主管反馈时 |
| `lean` | 仅 PHASE-GATE（`/gate-check`）— 跳过每个 skill 的关卡 | **默认** — 独立开发者和小型团队；主管仅在里程碑时审查 |
| `solo` | 任何地方都不运行主管关卡 | 游戏 Jam、原型、最大速度 |

**检查模式 — 在每次生成关卡前应用：**

```
在生成关卡 [GATE-ID] 之前：
1. 如果 skill 以 --review [mode] 调用，使用该模式
2. 否则读取 production/review-mode.txt
3. 否则默认为 lean

应用解析后的模式：
- solo → 跳过所有关卡。注明："[GATE-ID] skipped — Solo mode"
- lean → 跳过，除非是 PHASE-GATE（CD-PHASE-GATE、TD-PHASE-GATE、PR-PHASE-GATE、AD-PHASE-GATE）
         注明："[GATE-ID] skipped — Lean mode"
- full → 正常生成
```

---

## 调用模式（复制到任何 Skill 中）

**必须：在每次生成关卡前解析审查模式。** 切勿不检查就生成关卡。解析后的模式在每个 skill 运行中确定一次：
1. 如果 skill 以 `--review [mode]` 调用，使用该模式
2. 否则读取 `production/review-mode.txt`
3. 否则默认为 `lean`

应用解析后的模式：
- `solo` → **跳过所有关卡**。在输出中注明：`[GATE-ID] skipped — Solo mode`
- `lean` → **跳过，除非是 PHASE-GATE**（CD-PHASE-GATE、TD-PHASE-GATE、PR-PHASE-GATE、AD-PHASE-GATE）。注明：`[GATE-ID] skipped — Lean mode`
- `full` → 正常生成

```
# 应用模式检查，然后：
通过 Task 生成 `[agent-name]`：
- 关卡：[GATE-ID]（见 .claude/docs/director-gates.md）
- 上下文：[该关卡下列出的字段]
- 等待结论后再继续。
```

对于并行生成（在同一关卡点有多个主管）：

```
# 首先对每个关卡应用模式检查，然后生成所有通过检查的关卡：
同时生成所有 [N] 个 Agent（通过 Task）— 在等待任何结果之前先发出所有 Task 调用。
在继续之前收集所有结论。
```

---

## 标准结论格式

所有关卡返回三个结论之一。Skill 必须处理所有三种：

| 结论 | 含义 | 默认操作 |
|---------|---------|----------------|
| **APPROVE / READY** | 无问题。继续。 | 继续工作流 |
| **CONCERNS [列表]** | 存在但不阻塞的问题。 | 通过 AskUserQuestion 展示给用户 — 选项：`修复标记项` / `接受并继续` / `进一步讨论` |
| **REJECT / NOT READY [阻塞项]** | 阻塞性问题。不要继续。 | 向用户展示阻塞项。在解决前不写入文件或不推进阶段。 |

**升级规则**：当多个主管并行生成时，应用最严格的结论 — 一个 NOT READY 覆盖所有 READY 结论。

---

## 记录关卡结果

关卡解决后，在相关文档的状态标头中记录结论：

```markdown
> **[主管] 审查（[GATE-ID]）**：已批准 [日期] / 有疑虑（已接受）[日期] / 已修订 [日期]
```

对于阶段关卡，记录在 `docs/architecture/architecture.md` 或 `production/session-state/active.md` 中的适当位置。

---

## 第一层 — 创意主管关卡

Agent：`creative-director` | 模型层级：Opus | 领域：愿景、支柱、玩家体验

---

### CD-PILLARS — 支柱压力测试

**触发时机**：在游戏支柱和反支柱定义之后（头脑风暴阶段 4，或任何修订支柱时）

**要传递的上下文**：
- 包含名称、定义和设计测试的完整支柱集
- 反支柱列表
- 核心幻想陈述
- 独特卖点（"Like X, AND ALSO Y"）

**提示**：
> "审查这些游戏支柱。它们是可证伪的吗 — 真实的设计决策能否确实未能通过这个支柱？它们之间是否创造了有意义的张力？它们是否将这款游戏与其最接近的可比作品区分开来？它们能否在实践中帮助解决设计分歧，还是太模糊而无法使用？为每个支柱返回具体反馈和总体结论：APPROVE（强）、CONCERNS [列表]（需要打磨）或 REJECT（弱 — 支柱没有支撑力）。"

**结论**：APPROVE / CONCERNS / REJECT

---

### CD-GDD-ALIGN — GDD 支柱对齐检查

**触发时机**：系统 GDD 编写完成后

**要传递的上下文**：
- GDD 文件路径
- 游戏支柱（来自 `design/gdd/game-concept.md` 或 `design/gdd/game-pillars.md`）
- 此游戏的 MDA 美学目标
- 系统的既定玩家幻想章节

**提示**：
> "审查此系统 GDD 的支柱对齐性。每个章节是否服务于既定支柱？是否存在与支柱矛盾或削弱支柱的机制或规则？玩家幻想章节是否与游戏的核心幻想匹配？返回 APPROVE、CONCERNS [具体有问题的章节] 或 REJECT [必须重新设计才能实现此系统的支柱违规]。"

**结论**：APPROVE / CONCERNS / REJECT

---

### CD-SYSTEMS — 系统分解愿景检查

**触发时机**：在 `/map-systems` 编写系统索引之后 — 在 GDD 编写开始之前验证完整的系统集

**要传递的上下文**：
- 系统索引路径（`design/gdd/systems-index.md`）
- 游戏支柱和核心幻想（来自 `design/gdd/game-concept.md`）
- 优先级层级分配（MVP / Vertical Slice / Alpha / Full Vision）
- 依赖映射中标记的任何高风险或瓶颈系统

**提示**：
> "根据游戏的设计支柱审查此系统分解。完整的 MVP 层级系统集是否共同传递核心幻想？是否存在机制不服务于任何既定支柱的系统 — 表明它们可能是范围蔓延？是否存在没有分配系统来传递的支柱关键玩家体验？核心循环所需的系统是否有缺失？返回 APPROVE（系统服务于愿景）、CONCERNS [具体的缺口或错位及其支柱影响] 或 REJECT [根本性缺口 — 分解遗漏了关键设计意图，必须在 GDD 编写开始前修订]。'"

**结论**：APPROVE / CONCERNS / REJECT

---

### CD-NARRATIVE — 叙事一致性检查

**触发时机**：叙事 GDD、世界观文档、对话规格或世界构建文档编写完成后

**要传递的上下文**：
- 文档文件路径
- 游戏支柱
- 叙事方向简介或基调指南（如果存在于 `design/narrative/`）
- 新文档引用的任何现有世界观内容

**提示**：
> "根据游戏支柱和既定世界规则审查此叙事内容。基调是否与游戏的既定风格匹配？是否存在与现有世界观或世界构建的矛盾？内容是否服务于玩家体验支柱？返回 APPROVE、CONCERNS [具体不一致] 或 REJECT [破坏世界一致性的矛盾]。'"

**结论**：APPROVE / CONCERNS / REJECT

---

### CD-PLAYTEST — 玩家体验验证

**触发时机**：玩法测试报告生成后（`/playtest-report`），或任何产生玩家反馈的会话后

**要传递的上下文**：
- 玩法测试报告文件路径
- 游戏支柱和核心幻想陈述
- 正在测试的具体假设

**提示**：
> "根据游戏设计支柱和核心幻想审查此玩法测试报告。玩家体验是否与预期幻想匹配？是否存在代表支柱漂移的系统性问题 — 单独感觉良好但削弱预期体验的机制？返回 APPROVE（核心幻想正在落地）、CONCERNS [预期与实际体验之间的差距] 或 REJECT（核心幻想不存在 — 需要重新设计后再进行玩法测试）。'"

**结论**：APPROVE / CONCERNS / REJECT

---

### CD-PHASE-GATE — 阶段过渡的创意就绪性

**触发时机**：始终在 `/gate-check` 时 — 与 TD-PHASE-GATE 和 PR-PHASE-GATE 并行生成

**要传递的上下文**：
- 目标阶段名称
- 所有存在的制品列表（文件路径）
- 游戏支柱和核心幻想

**提示**：
> "从创意方向角度审查 [目标阶段] 阶段关卡的当前项目状态。游戏支柱是否在所有设计制品中得到忠实体现？当前状态是否保留了核心幻想？是否有任何跨 GDD 或架构的设计决策损害了预期玩家体验？返回 READY、CONCERNS [列表] 或 NOT READY [阻塞项]。'"

**结论**：READY / CONCERNS / NOT READY

---

## 第一层 — 技术主管关卡

Agent：`technical-director` | 模型层级：Opus | 领域：架构、引擎风险、性能

---

### TD-SYSTEM-BOUNDARY — 系统边界架构审查

**触发时机**：`/map-systems` 阶段 3 依赖映射达成一致后，GDD 编写开始前 — 在团队投入编写 GDD 之前验证系统结构在架构上是健全的

**要传递的上下文**：
- 系统索引路径（如果索引尚未编写，则为依赖映射摘要）
- 层级分配（Foundation / Core / Feature / Presentation / Polish）
- 完整依赖图（每个系统依赖什么）
- 任何标记的瓶颈系统（许多依赖者）
- 发现的任何循环依赖及其建议解决方案

**提示**：
> "在 GDD 编写开始前从架构角度审查此系统分解。系统边界是否清晰 — 每个系统是否拥有不同的关注点且重叠最小？是否存在上帝对象风险（系统做太多事情）？依赖排序是否创建了实现顺序问题？建议的边界中是否存在隐式共享状态问题，实现时会导致紧耦合？是否有任何 Foundation 层系统实际依赖 Feature 层系统（依赖倒置）？返回 APPROVE（边界架构健全 — 继续 GDD 编写）、CONCERNS [需要在 GDD 本身中解决的具体边界问题] 或 REJECT [根本性边界问题 — 系统结构将导致架构问题，必须在编写任何 GDD 之前重构]。'"

**结论**：APPROVE / CONCERNS / REJECT

---

### TD-FEASIBILITY — 技术可行性评估

**触发时机**：在范围/可行性评估期间识别出最大技术风险后

**要传递的上下文**：
- 概念的核心循环描述
- 平台目标
- 引擎选择（或"未决定"）
- 已识别的技术风险列表

**提示**：
> "审查针对 [类型] 游戏、目标 [平台]、使用 [引擎或'未决定引擎'] 的技术风险。标记可能使所描述的概念无效的高风险项目、任何引擎特定且应影响引擎选择的风险，以及任何常被独立开发者低估的风险。返回 VIABLE（风险可控）、CONCERNS [列表及缓解建议] 或 HIGH RISK [需要概念或范围修订的阻塞项]。'"

**结论**：VIABLE / CONCERNS / HIGH RISK

---

### TD-ARCHITECTURE — 架构签收

**触发时机**：主架构文档起草后（`/create-architecture` 阶段 7），以及任何重大架构修订后

**要传递的上下文**：
- 架构文档路径（`docs/architecture/architecture.md`）
- 技术需求基线（TR-ID 及数量）
- 带有状态的 ADR 列表
- 引擎知识差距清单

**提示**：
> "审查此主架构文档的技术健全性。检查：（1）基线中的每项技术需求是否都有架构决策覆盖？（2）所有高风险引擎领域是否都明确处理或标记为开放问题？（3）API 边界是否清晰、最小化且可实现？（4）Foundation 层 ADR 差距是否在实现开始前解决？返回 APPROVE、CONCERNS [列表] 或 REJECT [必须在编码开始前解决的阻塞项]。'"

**结论**：APPROVE / CONCERNS / REJECT

---

### TD-ADR — 架构决策审查

**触发时机**：单个 ADR 编写完成后（`/architecture-decision`），在标记为已接受之前

**要传递的上下文**：
- ADR 文件路径
- 引擎版本及该领域的知识差距风险等级
- 相关 ADR（如有）

**提示**：
> "审查此架构决策记录。是否有清晰的问题陈述和理由？被拒绝的备选方案是否经过真正考虑？影响章节是否诚实地承认了权衡？引擎版本是否已标记？截止日期后 API 风险是否已标记？它是否链接了所覆盖的 GDD 需求？返回 APPROVE、CONCERNS [具体差距] 或 REJECT [决策描述不足或基于不健全的技术假设]。'"

**结论**：APPROVE / CONCERNS / REJECT

---

### TD-ENGINE-RISK — 引擎版本风险审查

**触发时机**：在做出涉及截止日期后引擎 API 的架构决策时，或在最终确定任何引擎特定实现方法之前

**要传递的上下文**：
- 所使用的具体 API 或功能
- 引擎版本和 LLM 知识截止日期（来自 `docs/engine-reference/[engine]/VERSION.md`）
- 破坏性变更或弃用 API 文档的相关摘录

**提示**：
> "根据版本参考审查此引擎 API 使用情况。此 API 是否存在于 [引擎版本] 中？自 LLM 知识截止日期以来，其签名、行为或命名空间是否已更改？是否存在已知的弃用或截止日期后替代方案？返回 APPROVE（按描述使用安全）、CONCERNS（实现前验证）或 REJECT（API 已更改 — 提供修正方法）。'"

**结论**：APPROVE / CONCERNS / REJECT

---

### TD-PHASE-GATE — 阶段过渡的技术就绪性

**触发时机**：始终在 `/gate-check` 时 — 与 CD-PHASE-GATE 和 PR-PHASE-GATE 并行生成

**要传递的上下文**：
- 目标阶段名称
- 架构文档路径（如果存在）
- 引擎参考路径
- ADR 列表

**提示**：
> "从技术方向角度审查 [目标阶段] 阶段关卡的当前项目状态。此阶段的架构是否健全？所有高风险引擎领域是否都已处理？性能预算是否现实且有文档记录？Foundation 层决策是否足以开始实现？返回 READY、CONCERNS [列表] 或 NOT READY [阻塞项]。'"

**结论**：READY / CONCERNS / NOT READY

---

## 第一层 — 制作人关卡

Agent：`producer` | 模型层级：Opus | 领域：范围、时间线、依赖关系、生产风险

---

### PR-SCOPE — 范围和时间线验证

**触发时机**：范围层级定义后

**要传递的上下文**：
- 完整愿景范围描述
- MVP 定义
- 时间线估算
- 团队规模（独立/小型团队等）
- 范围层级（如果时间不够时交付什么）

**提示**：
> "审查此范围估算。对于所述团队规模，在所声明的时间线内 MVP 是否可实现？范围层级是否按风险正确排序 — 如果工作在该处停止，每个层级是否交付可发运的产品？在时间压力下最可能的裁剪点是什么，它是优雅的降级还是有缺陷的产品？返回 REALISTIC（范围与能力匹配）、OPTIMISTIC [建议的具体调整] 或 UNREALISTIC [阻塞项 — 必须修订时间线或 MVP]。'"

**结论**：REALISTIC / OPTIMISTIC / UNREALISTIC

---

### PR-SPRINT — Sprint 可行性审查

**触发时机**：最终确定 sprint 计划前（`/sprint-plan`），以及 sprint 中任何范围变更后

**要传递的上下文**：
- 提议的 sprint story 列表（标题、估算、依赖关系）
- 团队产能（可用小时数）
- 当前 sprint 积压债务（如有）
- 里程碑约束

**提示**：
> "审查此 sprint 计划的可行性。story 负载对于可用产能是否现实？story 是否按依赖关系正确排序？story 之间是否存在可能阻塞 sprint 中段的隐藏依赖？是否有任何 story 因其技术复杂度而被低估？返回 REALISTIC（计划可实现）、CONCERNS [具体风险] 或 UNREALISTIC（sprint 必须缩减范围 — 确定哪些 story 应推迟）。'"

**结论**：REALISTIC / CONCERNS / UNREALISTIC

---

### PR-MILESTONE — 里程碑风险评估

**触发时机**：里程碑审查时（`/milestone-review`）、sprint 中期回顾时，或提出影响里程碑的范围变更时

**要传递的上下文**：
- 里程碑定义和目标日期
- 当前完成百分比
- 阻塞故事数量
- Sprint 速度数据（如有）

**提示**：
> "审查此里程碑状态。基于当前速度和阻塞故事数量，此里程碑能否达成目标日期？从现在到里程碑之间的前 3 大生产风险是什么？有哪些范围项应被裁剪以保护里程碑日期，哪些是不可协商的？返回 ON TRACK、AT RISK [具体缓解措施] 或 OFF TRACK（日期必须延后或范围必须裁剪 — 提供两种选项）。'"

**结论**：ON TRACK / AT RISK / OFF TRACK

---

### PR-EPIC — 史诗结构可行性审查

**触发时机**：`/create-epics` 定义史诗后，在分解出 story 之前 — 验证史诗结构可生产后再调用 `/create-stories`

**要传递的上下文**：
- 史诗定义文件路径（所有刚创建的史诗）
- 史诗索引路径（`production/epics/index.md`）
- 里程碑时间线和目标日期
- 团队产能（独立/小型团队/规模）
- 被史诗化的层级（Foundation / Core / Feature 等）

**提示**：
> "在 story 分解开始前审查此史诗结构的生产可行性。史诗边界范围是否适当 — 每个史诗能否在里程碑截止日期前现实地完成？史诗是否按系统依赖关系正确排序 — 是否有任何史诗需要另一个史诗的输出才能开始？是否有史诗范围不足（太小，应合并）或范围过大（太大，应拆分为 2-3 个聚焦的史诗）？Foundation 层史诗的范围是否允许 Core 层史诗在 Foundation 完成后的下一个 sprint 开始时启动？返回 REALISTIC（史诗结构可生产）、CONCERNS [在编写 story 前需要调整的具体结构问题] 或 UNREALISTIC（史诗必须拆分、合并或重新排序 — 在解决前不能开始 story 分解）。'"

**结论**：REALISTIC / CONCERNS / UNREALISTIC

---

### PR-PHASE-GATE — 阶段过渡的生产就绪性

**触发时机**：始终在 `/gate-check` 时 — 与 CD-PHASE-GATE 和 TD-PHASE-GATE 并行生成

**要传递的上下文**：
- 目标阶段名称
- 存在的 sprint 和里程碑制品
- 团队规模和产能
- 当前阻塞的 story 数量

**提示**：
> "从生产角度审查 [目标阶段] 阶段关卡的当前项目状态。范围对于所述时间线和团队规模是否现实？依赖关系是否适当排序，以便团队能够按顺序实际执行？是否存在可能在前两个 sprint 内就使阶段偏离轨道的里程碑或 sprint 风险？返回 READY、CONCERNS [列表] 或 NOT READY [阻塞项]。'"

**结论**：READY / CONCERNS / NOT READY

---

## 第一层 — 美术主管关卡

Agent：`art-director` | 模型层级：Sonnet | 领域：视觉标识、艺术圣经、视觉生产就绪性

---

### AD-CONCEPT-VISUAL — 视觉标识锚点

**触发时机**：游戏支柱锁定后（头脑风暴阶段 4），与 CD-PILLARS 并行

**要传递的上下文**：
- 游戏概念（电梯演讲、核心幻想、独特卖点）
- 包含名称、定义和设计测试的完整支柱集
- 目标平台（如果已知）
- 用户提到的任何参考游戏或视觉试金石

**提示**：
> "基于这些游戏支柱和核心概念，提出 2-3 个不同的视觉标识方向。为每个方向提供：（1）一条可指导所有视觉决策的视觉规则（例如'万物必须运动'、'美在衰败中'），（2）情绪和氛围目标，（3）形状语言（锐利/圆润/有机/几何重点），（4）色彩理念（调色板方向，颜色在这个世界中的含义）。要具体 — 避免通用描述。一个方向应直接服务于主要设计支柱。命名每个方向。推荐最能服务既定支柱的方向并解释原因。'"

**结论**：CONCEPTS（多个有效选项 — 用户选择）/ STRONG（一个方向明显占优）/ CONCERNS（支柱未提供足够方向来区分视觉标识）

---

### AD-ART-BIBLE — 艺术圣经签收

**触发时机**：艺术圣经起草后（`/art-bible`），在资源生产开始前

**要传递的上下文**：
- 艺术圣经路径（`design/art/art-bible.md`）
- 游戏支柱和核心幻想
- 平台和性能约束（如果已配置，来自 `.claude/docs/technical-preferences.md`）
- 头脑风暴期间选择的视觉标识锚点（来自 `design/gdd/game-concept.md`）

**提示**：
> "审查此艺术圣经的完整性和内部一致性。颜色系统是否与情绪目标匹配？形状语言是否遵循视觉标识陈述？资源标准在平台约束下是否可实现？角色设计方向是否给予美术师足够的创作空间而未过度指定？章节之间是否存在矛盾？外包团队能否从这份文档生产资源而无需额外简报？返回 APPROVE（艺术圣经已准备好生产）、CONCERNS [需要澄清的具体章节] 或 REJECT [必须在资源生产开始前解决的根本性不一致]。'"

**结论**：APPROVE / CONCERNS / REJECT

---

### AD-PHASE-GATE — 阶段过渡的视觉就绪性

**触发时机**：始终在 `/gate-check` 时 — 与 CD-PHASE-GATE、TD-PHASE-GATE 和 PR-PHASE-GATE 并行生成

**要传递的上下文**：
- 目标阶段名称
- 所有存在的艺术/视觉制品列表（文件路径）
- 来自 `design/gdd/game-concept.md` 的视觉标识锚点（如果存在）
- 艺术圣经路径（如果存在，`design/art/art-bible.md`）

**提示**：
> "从视觉方向角度审查 [目标阶段] 阶段关卡的当前项目状态。视觉标识是否已建立并以此阶段所需的级别记录？正确的视觉制品是否到位？视觉团队能否在没有视觉方向差距（会导致代价高昂的后期返工）的情况下开始工作？是否有视觉决策被推迟到其最晚负责时刻之后？返回 READY、CONCERNS [可能导致生产返工的具体视觉方向差距] 或 NOT READY [在此阶段成功之前必须存在的视觉阻塞项 — 具体说明缺少什么制品以及它在此阶段为何重要]。'"

**结论**：READY / CONCERNS / NOT READY

---

## 第二层 — 负责人关卡

编排 skill 和高级 skill 在需要领域专家的可行性签收时调用这些关卡。第二层负责人使用 Sonnet（默认）。

---

### LP-FEASIBILITY — 主程序员实现可行性

**触发时机**：主架构文档编写后（`/create-architecture` 阶段 7b），或提出新的架构模式时

**要传递的上下文**：
- 架构文档路径
- 技术需求基线摘要
- 带有状态的 ADR 列表

**提示**：
> "审查此架构的实现可行性。标记：（a）任何使用所声明引擎和语言难以或不可能实现的决策，（b）任何程序员需要自行发明的缺失接口定义，（c）任何会创建可避免的技术债务或与标准 [引擎] 习惯用法相矛盾的模式。返回 FEASIBLE、CONCERNS [列表] 或 INFEASIBLE [使此架构按所写无法实现的阻塞项]。'"

**结论**：FEASIBLE / CONCERNS / INFEASIBLE

---

### LP-CODE-REVIEW — 主程序员代码审查

**触发时机**：dev story 实现后（`/dev-story`、`/story-done`），或作为 `/code-review` 的一部分

**要传递的上下文**：
- 实现文件路径
- Story 文件路径（用于验收标准）
- 相关的 GDD 章节
- 管理此系统的 ADR

**提示**：
> "根据 story 验收标准和管理 ADR 审查此实现。代码是否与架构边界定义匹配？是否存在违反编码标准或禁止模式的情况？公共 API 是否可测试并有文档记录？是否存在与 GDD 规则相关的正确性问题？返回 APPROVE、CONCERNS [具体问题] 或 REJECT（合并前必须修订）。'"

**结论**：APPROVE / CONCERNS / REJECT

---

### QL-STORY-READY — QA 负责人 Story 就绪性检查

**触发时机**：在 story 被接受进入 sprint 前 — 由 `/create-stories`、`/story-readiness` 和 `/sprint-plan` 在 story 选择期间调用

**要传递的上下文**：
- Story 文件路径
- Story 类型（Logic / Integration / Visual/Feel / UI / Config/Data）
- 验收标准列表（直接来自 story）
- 该 story 覆盖的 GDD 需求（TR-ID 及文本）

**提示**：
> "在 story 进入 sprint 前审查其验收标准的可测试性。所有标准是否足够具体，开发者能够明确知道何时完成？对于 Logic 类型的 story：每个标准是否可以通过自动化测试验证？对于 Integration story：每个标准是否在受控测试环境中可观察？标记过于模糊而无法实现的标准，以及需要完整游戏构建才能测试的标准（将这些标记为 DEFERRED，而非 BLOCKED）。返回 ADEQUATE（标准按所写可实现）、GAPS [需要优化的具体标准] 或 INADEQUATE（标准太模糊 — story 在纳入 sprint 前必须修订）。'"

**结论**：ADEQUATE / GAPS / INADEQUATE

---

### QL-TEST-COVERAGE — QA 负责人测试覆盖审查

**触发时机**：实现 story 完成后，在标记史诗完成前，或在 `/gate-check` 的 Production 到 Polish 阶段

**要传递的上下文**：
- 已实现的 story 列表及 story 类型（Logic / Integration / Visual / UI / Config）
- `tests/` 中的测试文件路径
- 系统的 GDD 验收标准

**提示**：
> "审查这些实现 story 的测试覆盖。所有 Logic story 是否已通过单元测试覆盖？Integration story 是否已通过集成测试或记录的玩法测试覆盖？GDD 验收标准是否每个都映射到至少一个测试？GDD 边界情况章节中是否有未测试的边界情况？返回 ADEQUATE（覆盖符合标准）、GAPS [具体缺失的测试] 或 INADEQUATE（关键逻辑未经测试 — 不要推进）。'"

**结论**：ADEQUATE / GAPS / INADEQUATE

---

### ND-CONSISTENCY — 叙事主管一致性检查

**触发时机**：编剧交付物（对话、世界观、物品描述）完成后，或设计决策有叙事影响时

**要传递的上下文**：
- 文档或内容文件路径
- 叙事圣经或基调指南路径（如果存在）
- 相关的世界构建规则
- 受影响的角色或阵营档案

**提示**：
> "审查此叙事内容的内部一致性和对既定世界规则的遵循。角色语气是否与既定档案一致？世界观是否与既定事实矛盾？基调是否与游戏的叙事方向一致？返回 APPROVE、CONCERNS [需要修复的具体不一致] 或 REJECT [破坏叙事基础的矛盾]。'"

**结论**：APPROVE / CONCERNS / REJECT

---

### AD-VISUAL — 美术主管视觉一致性审查

**触发时机**：美术方向决策做出后、引入新资源类型时，或技术美术决策影响视觉风格时

**要传递的上下文**：
- 艺术圣经路径（如果存在于 `design/art/art-bible.md`）
- 被审查的具体资源类型、风格决策或视觉方向
- 参考图像或风格描述
- 平台和性能约束

**提示**：
> "根据既定美术风格和生产约束审查此视觉方向决策。它是否与艺术圣经匹配？在平台的性能预算内是否可实现？是否存在产生技术风险的资源流水线影响？返回 APPROVE、CONCERNS [具体调整] 或 REJECT [必须先解决的风格违反或生产风险]。'"

**结论**：APPROVE / CONCERNS / REJECT

---

## 并行关卡协议

当工作流在同一检查点需要多个主管时（最常见于 `/gate-check`），同时生成所有 Agent：

```
并行生成（在等待任何结果之前先发出所有 Task 调用）：
1. creative-director  → 关卡 CD-PHASE-GATE
2. technical-director → 关卡 TD-PHASE-GATE
3. producer           → 关卡 PR-PHASE-GATE
4. art-director       → 关卡 AD-PHASE-GATE

收集所有四个结论，然后应用升级规则：
- 任何 NOT READY / REJECT → 总体结论最低为 FAIL
- 任何 CONCERNS       → 总体结论最低为 CONCERNS
- 所有 READY / APPROVE → 符合 PASS 条件（仍需进行制品检查）
```

---

## 添加新关卡

当新 Skill 或工作流需要新关卡时：

1. 分配关卡 ID：`[DIRECTOR-PREFIX]-[DESCRIPTIVE-SLUG]`
   - 前缀：`CD-` `TD-` `PR-` `LP-` `QL-` `ND-` `AD-`
   - 为新 Agent 添加新前缀：`audio-director` → `AU-`、`ux-designer` → `UX-`
2. 在适当的负责人章节下添加关卡，包含所有五个字段：触发时机、要传递的上下文、提示、结论和任何特殊处理说明
3. 在 skill 中仅通过 ID 引用 — 绝不将提示文本复制到 skill 中

---

## 按阶段划分的关卡覆盖

| 阶段 | 必需关卡 | 可选关卡 |
|-------|---------------|----------------|
| **概念** | CD-PILLARS、AD-CONCEPT-VISUAL | TD-FEASIBILITY、PR-SCOPE |
| **系统设计** | TD-SYSTEM-BOUNDARY、CD-SYSTEMS、PR-SCOPE、CD-GDD-ALIGN（每个 GDD） | ND-CONSISTENCY、AD-VISUAL |
| **技术设置** | TD-ARCHITECTURE、TD-ADR（每个 ADR）、LP-FEASIBILITY、AD-ART-BIBLE | TD-ENGINE-RISK |
| **预制作** | PR-EPIC、QL-STORY-READY（每个 story）、PR-SPRINT、所有四个 PHASE-GATE（通过 gate-check） | CD-PLAYTEST |
| **制作** | LP-CODE-REVIEW（每个 story）、QL-STORY-READY、PR-SPRINT（每个 sprint）、QL-TEST-COVERAGE（每个 sprint 结账） | PR-MILESTONE、AD-VISUAL |
| **打磨** | QL-TEST-COVERAGE、CD-PLAYTEST、PR-MILESTONE | AD-VISUAL |
| **发布** | 所有四个 PHASE-GATE（通过 gate-check） | QL-TEST-COVERAGE |
