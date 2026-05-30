# Agent 测试 Spec: unity-ui-specialist

## Agent 摘要
领域：Unity UI Toolkit（UXML/USS）、UGUI（Canvas）、数据绑定、运行时 UI 性能和 UI 输入事件处理。
不拥有：UX 流程设计（ux-designer）、视觉艺术风格（art-director）。
模型层级：Sonnet（默认）。
未分配 gate ID。

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 UI Toolkit / UGUI / Canvas / 数据绑定）
- [ ] `allowed-tools:` 列表包含 Read, Write, Edit, Bash, Glob, Grep
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义不声称对 UX 流程设计或视觉艺术方向拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出
**输入：** "使用 Unity UI Toolkit 实现一个背包 UI 界面。"
**预期行为：**
- 生成定义背包面板结构的 UXML 文档（ListView、物品模板、详情面板）
- 生成背包布局和物品状态（默认、悬停、选中）的 USS 样式
- 提供通过 `INotifyValueChanged` 或 `IBindable` 将背包数据模型绑定到 UI 的 C# 代码
- 使用带 `makeItem` / `bindItem` 回调的 `ListView` 实现可滚动物品列表
- 不生成 UX 流程设计——从提供的 spec 实现

### 用例 2：领域外重定向
**输入：** "设计背包的 UX 流程——当玩家装备 vs. 丢弃物品时会发生什么。"
**预期行为：**
- 不生成 UX 流程设计
- 明确指出交互流程设计属于 `ux-designer`
- 将请求重定向到 `ux-designer`
- 指出它将实现 ux-designer 指定的任何流程

### 用例 3：UI Toolkit 动态列表数据绑定
**输入：** "当物品从玩家背包中添加或移除时，背包列表需要实时更新。"
**预期行为：**
- 生成带绑定 `ObservableList<T>` 或事件驱动刷新方法的 `ListView` 模式
- 在后台集合变更事件上使用 `ListView.Rebuild()` 或 `ListView.RefreshItems()`
- 指出大型列表的性能考虑（通过 `makeItem`/`bindItem` 模式实现虚拟化）
- 不使用 `QuerySelector` 循环更新单个元素作为列表刷新策略——标记为性能反模式

### 用例 4：Canvas 性能——过度绘制
**输入：** "主菜单 Canvas 导致 GPU 过度绘制警告；有许多重叠的面板。"
**预期行为：**
- 识别过度绘制原因：多个堆叠的 Canvas、未在未激活时剔除的全屏覆盖面板
- 建议：
  - 为世界空间、屏幕空间叠加和屏幕空间相机层使用单独的 Canvas
  - 禁用/停用面板而非将 alpha 设为 0（不可见的 alpha-0 面板仍然绘制）
  - 使用 Canvas Group + alpha 进行淡入淡出效果，而非单个 Image 的 alpha
- 如果项目处于迁移位置，指出 UI Toolkit 替代方案

### 用例 5：上下文传递——Unity 版本
**输入：** 项目上下文：Unity 2022.3 LTS。请求："使用数据绑定实现设置面板。"
**预期行为：**
- 使用 2022.3 LTS 版本的运行时绑定系统的 UI Toolkit
- 指出 Unity 2022.3 引入了运行时数据绑定（与早期版本中仅编辑器的绑定相对）
- 如果 Unity 6 增强绑定 API 功能在 2022.3 中不可用，则不使用
- 生成与所述 Unity 版本兼容的代码，附带版本特定 API 说明

---

## 协议合规性

- [ ] 保持在声明领域内（UI Toolkit, UGUI, 数据绑定, UI 性能）
- [ ] 将 UX 流程设计重定向到 ux-designer
- [ ] 返回结构化输出（UXML, USS, C# 绑定代码）
- [ ] 为项目的 Unity 版本使用正确的 Unity UI 框架版本
- [ ] 标记 Canvas 过度绘制为性能反模式并提供具体的修复方案
- [ ] 不使用 alpha-0 作为显示/隐藏模式——使用 SetActive() 或 VisualElement.style.display

---

## 覆盖说明
- 背包 UI（用例 1）应在 `production/qa/evidence/` 中有手动操作文档
- 动态列表绑定（用例 3）应有集成测试或自动化交互测试
- Canvas 过度绘制（用例 4）验证 agent 了解正确的 Unity UI 性能模式
