# Agent 测试 Spec: ui-programmer

## Agent 摘要
领域：菜单界面、HUD、背包界面、对话框、UI 框架代码和数据绑定。
不拥有：UX 流程设计（ux-designer）、视觉风格方向（art-director / technical-artist）。
模型层级：Sonnet（默认）。
未分配 gate ID。

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用菜单 / HUD / UI 框架 / 数据绑定）
- [ ] `allowed-tools:` 列表包含 Read, Write, Edit, Bash, Glob, Grep
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义不声称对 UX 流程设计或视觉艺术方向拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出
**输入：** "根据 `design/ux/inventory-flow.md` 中的 UX spec 实现背包界面。"
**预期行为：**
- 在生成任何代码前读取 UX spec
- 使用项目配置的 UI 框架（UI Toolkit, UGUI, UMG 或 Godot Control 节点）生成实现
- 实现 spec 中定义的所有状态（默认、悬停、选中、空槽位、锁定槽位）
- 通过项目的数据模型将背包数据绑定到 UI 元素，而非硬编码值
- 根据编码标准在公共 UI API 上包含文档注释

### 用例 2：领域外请求——正确重定向
**输入：** "设计背包交互流程——当玩家装备、丢弃或组合物品时会发生什么。"
**预期行为：**
- 不生成交互流程设计或用户流程图
- 明确指出 UX 流程设计属于 `ux-designer`
- 将请求重定向到 `ux-designer`
- 指出一旦流程 spec 准备好，它可以实现它

### 用例 3：自定义动画协调
**输入：** "背包中的物品选择在选中时需要一个自定义弹跳动画。"
**预期行为：**
- 识别定义动画曲线和感觉在 technical-artist 的领域内
- 在没有 spec 的情况下不发明动画参数（定时、缓动）
- 与 `technical-artist` 协调获取动画 spec（持续时间、缓动曲线、过冲量）
- 一旦提供 spec，生成将动画绑定到选择状态的实现

### 用例 4：模糊的 UX spec——标记回馈
**输入：** UX spec 指出"选中时显示物品详情"，但未定义选中空槽位时会发生什么。
**预期行为：**
- 识别 spec 中的模糊性（空槽位选中状态未定义）
- 不对未定义状态做出任意实现决策
- 向 `ux-designer` 标记模糊性，附带具体问题："当选中空的背包槽位时，详情面板应显示什么？"
- 可以提出两个常见选项（隐藏面板 / 显示占位符）以帮助 ux-designer 快速决策

### 用例 5：上下文传递——引擎 UI 工具包
**输入：** 提供的引擎上下文：项目使用 Godot 4.6 和 Control 节点 UI。请求："为背包实现可滚动物品列表。"
**预期行为：**
- 使用 Godot 的 `ScrollContainer` + `VBoxContainer` + `ItemList`（或等效）模式，而非 Canvas 或 UGUI
- 不为 Godot 项目生成 Unity UGUI 或 Unreal UMG 代码
- 在使用特定 API 前检查引擎版本引用（4.6）了解 4.4/4.5 以来的任何 Control 节点 API 变更
- 生成与项目配置语言一致的 GDScript 或 C# 代码

---

## 协议合规性

- [ ] 保持在声明领域内（菜单、HUD、UI 框架、数据绑定）
- [ ] 将 UX 流程设计重定向到 ux-designer
- [ ] 在实现动画前与 technical-artist 协调动画 specs
- [ ] 将模糊的 UX specs 标记回 ux-designer，而非做出任意实现决策
- [ ] 返回结构化输出（实现代码、数据绑定模式、UI 状态的状态机）
- [ ] 为项目使用正确的引擎 UI 工具包——绝不跨引擎代码

---

## 覆盖说明
- 背包实现（用例 1）应在 `production/qa/evidence/` 中有 UI 交互测试或手动操作文档
- 动画协调（用例 3）确认 agent 在没有 spec 的情况下不发明感觉参数
- 模糊 spec（用例 4）验证 agent 将 spec 缺口路由回创作 agent 而非猜测
