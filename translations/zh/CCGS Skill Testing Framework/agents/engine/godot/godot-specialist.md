# Agent 测试 Spec: godot-specialist

## Agent 摘要
领域：Godot 特定模式、节点/场景架构、信号、资源和 GDScript vs C# vs GDExtension 决策。
不拥有：特定语言的实际代码编写（委托给语言子 specialist）。
模型层级：Sonnet（默认）。
未分配 gate ID。

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 Godot 架构 / 节点模式 / 引擎决策）
- [ ] `allowed-tools:` 列表包含 Read, Write, Edit, Bash, Glob, Grep
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义引用 `docs/engine-reference/godot/VERSION.md` 作为权威 API 来源

---

## 测试用例

### 用例 1：领域内请求——正确输出
**输入：** "在 Godot 中何时应该使用信号 vs. 直接方法调用？"
**预期行为：**
- 生成带理据的模式决策指南：
  - 信号：解耦通信、父对子无知、事件驱动 UI 更新、一对多通知
  - 直接调用：调用者需要返回值的紧耦合系统，或性能关键的热路径
- 在项目上下文中提供每个模式的具体示例
- 不生成两种模式的原始代码——建议由 gdscript-specialist 或 csharp-specialist 实现
- 指出"不向上发信号"的约定（子类不直接调用父类方法——改用信号）

### 用例 2：错误引擎重定向
**输入：** "编写一个在 Start() 上运行并订阅 UnityEvent 的 MonoBehaviour。"
**预期行为：**
- 不生成 Unity MonoBehaviour 代码
- 清楚识别这是 Unity 模式，而非 Godot 模式
- 提供 Godot 等效：使用 `_ready()` 替代 `Start()` 的 Node 脚本，以及 Godot 信号替代 UnityEvent
- 确认项目基于 Godot，并重定向概念映射

### 用例 3：后 cutoff API 风险
**输入：** "使用新的 Godot 4.5 @abstract 注解定义抽象基类。"
**预期行为：**
- 识别 `@abstract` 是后 cutoff 功能（在 Godot 4.5 中引入，在 LLM 知识 cutoff 之后）
- 标记版本风险：LLM 对此注解的知识可能不完整或不正确
- 指示用户根据 `docs/engine-reference/godot/VERSION.md` 和官方 4.5 迁移指南进行验证
- 基于版本引用中的迁移说明提供尽力而为的指导，同时清楚标记为未经验证

### 用例 4：热路径的语言选择
**输入：** "物理查询循环为 500 个对象每帧运行。我们应该为此使用 GDScript 还是 C#？"
**预期行为：**
- 提供平衡的分析：
  - GDScript：更简单、团队熟悉，但对紧循环较慢
  - C#：对 CPU 密集型循环更快，需要 .NET 运行时，团队需要 C# 知识
- 不单方面做最终决定
- 将决定推迟给 `lead-programmer`，以分析作为输入
- 指出 GDExtension（C++）是极端性能情况的第三个选项，并建议在 C# 不够时升级

### 用例 5：上下文传递——引擎版本 4.6
**输入：** 提供引擎版本上下文：Godot 4.6，Jolt 作为默认物理引擎。请求："为玩家角色设置 RigidBody3D。"
**预期行为：**
- 读取 4.6 上下文并应用 Jolt 默认知识（来自 VERSION.md 迁移说明）
- 推荐与 Jolt 兼容的 RigidBody3D 配置选择（例如，指出任何在 Jolt 下表现不同的 GodotPhysics 特定设置）
- 引用 4.6 迁移说明中关于 Jolt 成为默认的内容，而非仅依赖 LLM 训练数据
- 标记任何在 GodotPhysics 和 Jolt 之间行为发生变化的 RigidBody3D 属性

---

## 协议合规性

- [ ] 保持在声明领域内（Godot 架构决策、节点/场景模式、语言选择）
- [ ] 将特定语言实现重定向到 godot-gdscript-specialist 或 godot-csharp-specialist
- [ ] 返回结构化发现（决策树、带理据的模式建议）
- [ ] 将 `docs/engine-reference/godot/VERSION.md` 视为高于 LLM 训练数据的权威
- [ ] 标记后 cutoff API 用法（4.4, 4.5, 4.6）并附带验证要求
- [ ] 在存在权衡时将语言选择决定推迟给 lead-programmer

---

## 覆盖说明
- 信号 vs. 直接调用指南（用例 1）应作为可重用模式文档写入 `docs/architecture/`
- 后 cutoff 标记（用例 3）确认 agent 不会自信地使用其无法验证的 API
- 引擎版本用例（用例 5）验证 agent 从版本引用中应用迁移说明，而非假设
