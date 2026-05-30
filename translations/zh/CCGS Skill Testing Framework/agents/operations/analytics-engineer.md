# Agent 测试 Spec: analytics-engineer

## Agent 摘要
- **领域**：遥测架构和事件 schema 设计、A/B 测试框架设计、玩家行为分析方法论、分析仪表板规范、事件命名约定、数据管线设计（schema → 采集 → 仪表板）
- **不拥有**：事件追踪的游戏实现（适当的程序员）、基于分析的经济设计决策（economy-designer）、Live Ops 事件设计（live-ops-designer）
- **模型层级**：Sonnet
- **Gate IDs**：无；生成 schema 和测试设计；将实现委托给程序员

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用遥测、A/B 测试、事件追踪、分析）
- [ ] `allowed-tools:` 列表匹配 agent 的角色（design/analytics/ 和文档的 Read/Write；无游戏源码或 CI 工具）
- [ ] 模型层级为 Sonnet（operations specialist 默认）
- [ ] Agent 定义不声称对游戏实现、经济设计或 Live Ops 排期拥有权威

---

## 测试用例

### 用例 1：领域内请求——教程事件追踪设计
**输入**："为我们的教程设计分析事件追踪。我们想知道玩家在哪里流失以及他们完成了哪些步骤。"
**预期行为**：
- 为每个教程步骤生成结构化事件 schema：至少包括 `event_name`、`properties`（step_id, step_name, player_id, session_id, timestamp）和 `trigger_condition`（事件确切触发时机——在步骤开始、步骤完成、步骤跳过时）
- 包括漏斗完成事件和流失事件（例如玩家在步骤中退出时的 `tutorial_step_abandoned`）
- 指定事件命名约定：snake_case，按领域前缀（例如 `tutorial_step_started`、`tutorial_step_completed`、`tutorial_abandoned`）
- 不生成实现代码——将实现标记为 [TO BE IMPLEMENTED BY PROGRAMMER]
- 输出是 schema 表或结构化列表，而非叙述性描述

### 用例 2：领域外请求——用代码实现事件追踪
**输入**："既然事件 schema 已设计好，请编写 GDScript 代码在我们的 Godot 教程场景中触发这些事件。"
**预期行为**：
- 不生成 GDScript 或任何实现代码
- 明确说明："游戏代码中的遥测实现由相应的程序员（gameplay-programmer 或 systems-programmer）处理；我提供事件 schema 和集成要求"
- 可选地生成集成 spec：程序员需要了解以正确实现的信息（事件名称、属性、触发时机、使用的分析 SDK 或端点）

### 用例 3：领域边界——UI 变更的 A/B 测试设计
**输入**："我们想对两个版本的 HUD 进行 A/B 测试：当前版本和只有血条的最小版本。设计测试。"
**预期行为**：
- 生成完整的 A/B 测试设计文档：
  - **假设**：最小 HUD 将通过减少 UI 认知负荷来提高玩家参与度（以会话时长衡量）
  - **主要指标**：每位玩家平均会话时长
  - **次要指标**：教程完成率、Day 1 留存
  - **样本量**：基于预期效应大小的计算估计（或指出精确计算需要基线数据）——不跳过该字段
  - **持续时间**：最短持续时间（例如"至少 2 周以捕获每周玩家行为模式"）
  - **随机化单位**：Player ID（非 session ID，防止玩家看到两个版本）
- 输出是正式的测试设计，而非想法的项目符号列表

### 用例 4：冲突——重叠的 A/B 测试玩家分组
**输入**："我们同时运行两个 A/B 测试：测试 A（HUD 变体）影响所有玩家，测试 B（教程变体）也影响所有玩家。"
**预期行为**：
- 标记重叠为互斥违反：如果两个测试影响同一玩家，其结果被混淆——两个测试都不能产生干净的数据
- 精确定位问题：同时参与两个测试的玩家将有 HUD 和教程变体相互作用，使得不可能将结果差异归因于任何一个变量
- 提出解决方案选项：(a) 顺序运行测试，(b) 将玩家群体分割为互斥分组（50% 在测试 A，50% 在测试 B，0% 在两个），或 (c) 如果交互效应也感兴趣则运行析因设计（更复杂，需要更大样本）
- 不建议在重叠的群体上继续两个测试

### 用例 5：上下文传递——新事件与现有 schema 一致
**输入上下文**：现有事件 schema 使用命名约定：`[domain]_[object]_[action]`，使用 snake_case。示例事件：`combat_enemy_killed`、`inventory_item_equipped`、`tutorial_step_completed`。
**输入**："为我们的新制作系统设计事件追踪：玩家收集材料、打开制作菜单和制作物品。"
**预期行为**：
- 生成严格遵循提供 schema 命名约定的事件：`crafting_material_gathered`、`crafting_menu_opened`、`crafting_item_crafted`
- 不发明不同的命名模式（例如 `gatherMaterial`、`craftingOpened`），即使它可能看起来自然
- 属性遵循与现有事件相同的结构：`player_id`、`session_id`、`timestamp` 为标准字段；领域特定字段（material_type、item_id、crafting_time_seconds）为附加属性
- 输出明确引用提供的命名约定作为遵循的标准

---

## 协议合规性

- [ ] 保持在声明领域内（事件 schema 设计、A/B 测试设计、分析方法论）
- [ ] 将实现请求重定向到相应的程序员，附带集成 spec，而非代码
- [ ] 生成完整的 A/B 测试设计（假设、指标、样本量、持续时间、随机化单位）——从不部分
- [ ] 将重叠 A/B 测试中的互斥违反标记为数据质量阻塞项
- [ ] 严格遵循提供的命名约定；不发明替代约定

---

## 覆盖说明
- 用例 3（A/B 测试设计完整性）是质量门——不完整的测试设计浪费实验预算
- 用例 4（互斥）是数据完整性测试——重叠测试产生不可用的结果；必须捕获
- 用例 5 是最重要的上下文感知测试；跨 schema 的命名约定漂移导致仪表板损坏
- 无自动运行器；通过 `/skill-test` 手动审查
