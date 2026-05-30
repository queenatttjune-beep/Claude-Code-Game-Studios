# Agent 测试 Spec: godot-gdscript-specialist

## Agent 摘要
领域：GDScript 静态类型、GDScript 设计模式、信号架构、协程/await 模式和 GDScript 性能。
不拥有：着色器代码（godot-shader-specialist）、GDExtension 绑定（godot-gdextension-specialist）。
模型层级：Sonnet（默认）。
未分配 gate ID。

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 GDScript / 静态类型 / 信号 / 协程）
- [ ] `allowed-tools:` 列表包含 Read, Write, Edit, Bash, Glob, Grep
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义不声称对着色器代码或 GDExtension 拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出
**输入：** "审查此 GDScript 文件的类型注解覆盖情况。"
**预期行为：**
- 读取提供的 GDScript 文件
- 标记每个缺少静态类型注解的变量、参数和返回类型
- 生成逐行发现列表：`var speed = 5.0` → `var speed: float = 5.0`
- 指出 Godot 4 中静态类型的性能和工具优势
- 不在未提示的情况下重写整个文件——生成发现列表供开发者应用

### 用例 2：领域外请求——正确重定向
**输入：** "编写一个在世界空间中扭曲网格的顶点着色器。"
**预期行为：**
- 不以 GDScript 或 Godot 着色语言生成着色器代码
- 明确指出着色器编写属于 `godot-shader-specialist`
- 将请求重定向到 `godot-shader-specialist`
- 可以指出 GDScript 方面（向着色器传递 uniform、设置着色器参数）在其领域内

### 用例 3：使用协程的异步加载
**输入：** "异步加载场景并等待其完成后再生成它。"
**预期行为：**
- 为 Godot 4 生成 `await` + `ResourceLoader.load_threaded_request` 模式
- 全程使用静态类型（`var scene: PackedScene`）
- 使用 `ResourceLoader.load_threaded_get_status()` 处理完成检查
- 说明加载失败的错误处理
- 不使用已弃用的 Godot 3 `yield()` 语法

### 用例 4：性能问题——类型化数组建议
**输入：** "实体更新循环很慢；它每帧迭代一个包含 1000 个节点的未类型化 Array。"
**预期行为：**
- 识别未类型化 `Array` 放弃了 GDScript 中的编译器优化
- 建议转换为类型化数组（`Array[Node]` 或特定类型）以启用 JIT 提示
- 指出如果这仍然不够，则将热路径提升到 C# 作为建议
- 将类型化数组重构作为即时修复方案
- 在没有性能分析证据的情况下，不建议将整个代码库迁移到 C#

### 用例 5：上下文传递——Godot 4.6 后 cutoff 功能
**输入：** 提供引擎版本上下文：Godot 4.6。请求："使用 @abstract 为所有敌人类型创建抽象基类。"
**预期行为：**
- 识别 `@abstract` 为 Godot 4.5+ 功能（后 cutoff）
- 在输出中指出：功能在 4.5 中引入，已针对 VERSION.md 迁移说明验证
- 使用 `@abstract` 生成 GDScript 类，语法正确，如迁移说明中所述
- 将输出标记为需要根据官方 4.5 发布说明进行验证，因为已过 cutoff
- 在抽象类的所有方法签名中使用静态类型

---

## 协议合规性

- [ ] 保持在声明领域内（GDScript——类型、模式、信号、协程、性能）
- [ ] 将着色器请求重定向到 godot-shader-specialist
- [ ] 将 GDExtension 请求重定向到 godot-gdextension-specialist
- [ ] 返回带有完整静态类型的结构化 GDScript 输出
- [ ] 仅使用 Godot 4 API——无已弃用的 Godot 3 模式（yield、使用字符串的 connect 等）
- [ ] 标记后 cutoff 功能（4.4, 4.5, 4.6）并将其标记为需要文档验证

---

## 覆盖说明
- 类型注解审查（用例 1）的输出适合作为代码审查清单
- 异步加载（用例 3）应生成可测试的代码，可通过 `tests/unit/` 中的单元测试验证
- 后 cutoff @abstract（用例 5）确认 agent 标记版本不确定性，而非静默使用未经验证的 API
