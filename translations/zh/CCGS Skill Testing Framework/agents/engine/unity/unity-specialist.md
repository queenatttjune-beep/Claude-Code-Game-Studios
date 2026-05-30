# Agent 测试 Spec: unity-specialist

## Agent 摘要
领域：Unity 特定架构模式、MonoBehaviour vs DOTS 决策和子系统选择（Addressables, New Input System, UI Toolkit, Cinemachine 等）。
不拥有：语言特定的深入探讨（委托给 unity-dots-specialist, unity-ui-specialist 等）。
模型层级：Sonnet（默认）。
未分配 gate ID。

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 Unity 模式 / MonoBehaviour / 子系统决策）
- [ ] `allowed-tools:` 列表包含 Read, Write, Edit, Bash, Glob, Grep
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义承认子 specialist 路由表（DOTS, UI, Shader, Addressables）

---

## 测试用例

### 用例 1：领域内请求——正确输出
**输入：** "我应该使用 MonoBehaviour 还是 ScriptableObject 来存储敌人配置数据？"
**预期行为：**
- 生成涵盖以下内容的模式决策树：
  - MonoBehaviour：用于运行时行为，需要附加到 GameObject，有 Update() 生命周期
  - ScriptableObject：用于纯数据/配置，作为资源存在，跨实例共享，无场景依赖
- 推荐将 ScriptableObject 用于敌人配置数据（无状态、可复用、方便设计师）
- 指出 MonoBehaviour 可以在运行时引用 ScriptableObject
- 提供 ScriptableObject 类定义外观的具体示例（不生成完整代码——建议由 engine-programmer 或 gameplay-programmer 实现）

### 用例 2：错误引擎重定向
**输入：** "为此敌人系统设置带有信号的 Node 场景树。"
**预期行为：**
- 不生成 Godot Node/信号代码
- 将其识别为 Godot 模式
- 说明在 Unity 中等效的是 GameObject 层级 + UnityEvent 或 C# 事件
- 映射概念：Godot Node → Unity MonoBehaviour, Godot Signal → C# event / UnityEvent
- 在继续前确认项目基于 Unity

### 用例 3：Unity 版本 API 标记
**输入：** "使用新的 Unity 6 GPU resident drawer 进行批处理渲染。"
**预期行为：**
- 识别 Unity 6 功能（GPU Resident Drawer）
- 标记此 API 在较早的 Unity 版本中可能不可用
- 在提供实现指导前询问或检查项目的 Unity 版本
- 指示对照官方 Unity 6 文档验证
- 在未确认的情况下不假定项目使用 Unity 6

### 用例 4：DOTS vs. MonoBehaviour 冲突
**输入：** "战斗系统使用 MonoBehaviour 进行状态管理，但我们想添加基于 DOTS 的弹丸系统。它们能共存吗？"
**预期行为：**
- 识别为混合架构场景
- 解释混合方法：MonoBehaviour 可以通过 SystemAPI、IComponentData 和托管组件与 DOTS 交互
- 指出混合两种模式的性能和复杂性权衡
- 建议将架构决策升级到 `lead-programmer` 或 `technical-director`
- 将 DOTS 侧的实现细节委托给 `unity-dots-specialist`

### 用例 5：上下文传递——Unity 版本
**输入：** 提供项目上下文：Unity 2023.3 LTS。请求："为此项目配置新输入系统。"
**预期行为：**
- 应用 Unity 2023.3 LTS 上下文：使用 New Input System（com.unity.inputsystem）包
- 不生成遗留 Input Manager 代码（`Input.GetKeyDown()`, `Input.GetAxis()`）
- 指出任何 2023.3 特定的输入系统行为或包版本约束
- 如果输入系统与 DOTS 交互，引用项目版本确认 Burst/Jobs 兼容性

---

## 协议合规性

- [ ] 保持在声明领域内（Unity 架构决策、模式选择、子系统路由）
- [ ] 将 Godot 模式重定向到相应的 Godot specialist 或标记为错误引擎
- [ ] 将 DOTS 实现重定向到 unity-dots-specialist
- [ ] 将 UI 实现重定向到 unity-ui-specialist
- [ ] 标记 Unity 版本门控 API，并建议前要求版本确认
- [ ] 返回结构化模式决策指南，而非自由形式意见

---

## 覆盖说明
- MonoBehaviour vs. ScriptableObject（用例 1）如果导致项目级决策，应记录为 ADR
- 版本标记（用例 3）确认 agent 在无上下文时不假定使用最新 Unity 版本
- DOTS 混合（用例 4）验证 agent 将架构冲突升级而非单方面解决
