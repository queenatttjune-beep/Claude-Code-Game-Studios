# Agent 测试 Spec: ue-umg-specialist

## Agent 摘要
- **领域**：UMG 部件层次结构设计、数据绑定模式、CommonUI 输入路由和操作标签、部件样式（WidgetStyle 资源）、UI 优化（部件池化、ListView、invalidation）
- **不拥有**：UX 流程和界面导航设计（ux-designer）、游戏逻辑（gameplay-programmer）、后端数据源（游戏代码）、服务器通信
- **模型层级**：Sonnet
- **Gate IDs**：无；UX 流程决策委托给 ux-designer

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 UMG、部件层次结构、CommonUI）
- [ ] `allowed-tools:` 列表匹配 agent 的角色（UI 资源和 Blueprint 文件的 Read/Write；无服务器或游戏玩法源工具）
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义不声称对 UX 流程、导航架构或游戏玩法数据逻辑拥有权威

---

## 测试用例

### 用例 1：领域内请求——带数据绑定的库存部件
**输入**："创建一个显示物品槽位网格的库存部件。每个槽位应显示物品图标、数量和稀有度颜色。库存变化时需要更新。"
**预期行为**：
- 生成 UMG 部件结构：包含 UniformGridPanel 或 TileView 的父级 WBP_Inventory，每个物品一个子级 WBP_InventorySlot 部件
- 描述数据绑定方法：库存组件上的 Event Dispatchers 触发刷新，或使用实现 IUserObjectListEntry 的 UObject 物品数据类的 ListView
- 指定稀有度颜色的驱动方式：WidgetStyle 资源或数据表查找，而非硬编码颜色值
- 输出包括部件层次结构、绑定模式和刷新触发机制

### 用例 2：领域外请求——UX 流程设计
**输入**："为我们的库存系统设计完整的导航流程——玩家如何打开它、过渡到角色统计、以及退出到暂停菜单。"
**预期行为**：
- 不生成导航流程或界面转换架构
- 明确说明："导航流程和界面转换设计由 ux-designer 负责；我可以在流程定义后实现 UMG 部件结构"
- 在没有 UX spec 的情况下，不做 UX 决策（返回按钮行为、过渡动画、模态 vs. 全屏）

### 用例 3：领域边界——CommonUI 输入操作不匹配
**输入**："我们的库存部件对控制器返回按钮无响应。我们使用 CommonUI。"
**预期行为**：
- 识别可能原因：部件的返回输入操作标签与项目注册的 CommonUI InputAction 数据资源不匹配
- 解释 CommonUI 输入路由模型：部件通过 `CommonUI_InputAction` 标签声明输入操作；CommonActivatableWidget 处理路由
- 提供修复方案：验证部件的返回操作标签与项目 CommonUI 输入操作数据表中注册的标签匹配
- 将其与硬件输入绑定问题（这属于 Enhanced Input 领域）区分开

### 用例 4：部件性能问题——每帧大量部件实例
**输入**："我们的排行榜部件一次性创建 500 个独立的 WBP_LeaderboardRow 实例。打开排行榜时游戏卡顿 300ms。"
**预期行为**：
- 识别根本原因：单帧内实例化 500 个部件导致构建卡顿
- 建议切换到 ListView 或 TileView 并使用虚拟化——仅构建可见行
- 解释 ListView 数据对象需要的 IUserObjectListEntry 接口要求
- 如果 ListView 不合适，建议池化：预实例化固定数量的行并用新数据重用它们
- 输出是具有要使用的具体 UMG 组件的建议，而非模糊的"优化它"

### 用例 5：上下文传递——CommonUI 已配置
**输入上下文**：项目使用 CommonUI，注册了以下 InputAction 标签：UI.Action.Confirm, UI.Action.Back, UI.Action.Pause, UI.Action.Secondary。
**输入**："向库存部件添加一个与 CommonUI 配合使用的'排序库存'按钮。"
**预期行为**：
- 使用 UI.Action.Secondary（或建议注册新标签如 UI.Action.Sort，如果 Secondary 已被分配）
- 不发明新的 InputAction 标签而不说明必须在 CommonUI 数据表中注册
- 当 CommonUI 是已建立的模式时，不使用非 CommonUI 输入绑定方法（例如事件图形中的原始按键）
- 在建议中明确引用提供的标签列表

---

## 协议合规性

- [ ] 保持在声明领域内（UMG 结构、数据绑定、CommonUI、部件性能）
- [ ] 将 UX 流程和导航设计请求重定向到 ux-designer
- [ ] 返回结构化发现（部件层次结构 + 绑定模式），而非自由形式意见
- [ ] 使用上下文中现有的 CommonUI InputAction 标签；不发明新标签而不标记注册要求
- [ ] 对于大型集合，在部件池化之前推荐虚拟化列表（ListView/TileView）

---

## 覆盖说明
- 用例 3（CommonUI 输入路由）要求项目已配置 CommonUI；如果项目不使用 CommonUI 则跳过测试
- 用例 4（性能）是高影响故障模式——300ms 卡顿是发布阻塞问题；优先考虑此测试用例
- 用例 5 是对 UI 管线一致性最重要的上下文感知测试
- 无自动运行器；通过 `/skill-test` 手动审查
