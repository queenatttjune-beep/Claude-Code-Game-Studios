# Agent 测试 Spec: ue-blueprint-specialist

## Agent 摘要
- **领域**：Blueprint 架构、Blueprint/C++ 边界、Blueprint 图形质量、Blueprint 性能优化、Blueprint Function Library 设计
- **不拥有**：C++ 实现（engine-programmer 或 gameplay-programmer）、美术资源或着色器、UI/UX 流程设计（ux-designer）
- **模型层级**：Sonnet
- **Gate IDs**：无；跨领域裁决委托给 unreal-specialist 或 lead-programmer

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 Blueprint 架构和优化）
- [ ] `allowed-tools:` 列表匹配 agent 的角色（Blueprint 项目文件的 Read；无服务器或部署工具）
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义不声称对 C++ 实现决策拥有权威

---

## 测试用例

### 用例 1：领域内请求——Blueprint 图形性能审查
**输入**："审查我们的 AI 行为 Blueprint。它有基于 tick 的逻辑每帧运行，同时对 30 个 NPC 进行视线检测。"
**预期行为**：
- 将 tick 密集型逻辑识别为性能问题
- 建议从 EventTick 切换到事件驱动模式（感知系统事件、定时器或降低频率的轮询）
- 标记同时进行视线检测的每个 NPC 成本
- 建议替代方案：AIPerception 组件事件、交错 tick 组，或者如果 Blueprint 开销经测量显著，则将系统移至 C++
- 输出结构清晰：识别问题、评估影响、列出替代方案

### 用例 2：领域外请求——C++ 实现
**输入**："为这个技能冷却系统编写 C++ 实现。"
**预期行为**：
- 不生成 C++ 实现代码
- 提供冷却逻辑的 Blueprint 等效（例如，如果使用 GAS，使用 Timeline 或 GameplayEffect）
- 明确说明："C++ 实现由 engine-programmer 或 gameplay-programmer 处理；我可以展示 Blueprint 方法或描述 Blueprint 调用 C++ 的边界"
- 可选地指出冷却复杂度何时需要 C++ 后端

### 用例 3：领域边界——Blueprint 中不安全的原始指针访问
**输入**："我们的 Blueprint 调用 GetOwner()，然后立即访问结果上的组件而不检查其是否有效。"
**预期行为**：
- 标记为运行时崩溃风险：GetOwner() 在某些生命周期状态下可能返回 null
- 提供正确的 Blueprint 模式：在任何属性/组件访问前使用 IsValid() 节点
- 指出 Blueprint 的空检查在 Actor 派生引用上不是可选的
- 不静默修复代码而不解释原始代码为何不安全

### 用例 4：Blueprint 图形复杂度——为 Function Library 重构做准备
**输入**："我们的主 GameMode Blueprint 在单个图形中有 600 多个节点，并且在 8 个地方有重复的伤害计算逻辑。"
**预期行为**：
- 诊断这是一个可维护性和可测试性问题
- 建议将重复逻辑提取到 Blueprint Function Library（BFL）
- 描述如何构建 BFL：用于计算的纯函数，从任何 Blueprint 进行的静态调用
- 指出如果伤害逻辑对性能敏感或与 C++ 共享，可能是迁移到 unreal-specialist 审查的候选
- 输出是一个具体的重构计划，而非模糊建议

### 用例 5：上下文传递——Blueprint 复杂度预算
**输入上下文**：项目约定指定每个 Blueprint 事件图形最多 100 个节点，超时必须进行 Function Library 提取。
**输入**："这里是我们 150 个节点的库存 Blueprint 图形。可以发货了吗？"
**预期行为**：
- 引用所述 150 个节点数对比项目约定的 100 节点预算
- 标记图形超出复杂度阈值
- 不按原样批准
- 生成候选子图形列表，用于 Function Library 提取以将主图形控制在预算内

---

## 协议合规性

- [ ] 保持在声明领域内（Blueprint 架构、性能、图形质量）
- [ ] 将 C++ 实现请求重定向到 engine-programmer 或 gameplay-programmer
- [ ] 返回结构化发现（问题/影响/替代方案格式），而非自由形式意见
- [ ] 主动执行 Blueprint 安全模式（空检查、IsValid）
- [ ] 在评估图形复杂度时引用项目约定

---

## 覆盖说明
- 用例 3（空指针安全）是安全关键的测试——这是发布崩溃的常见来源
- 用例 5 要求项目约定包含声明的节点预算；如果未配置，agent 应指出缺失并建议设置一个
- 无自动运行器；通过 `/skill-test` 手动审查
