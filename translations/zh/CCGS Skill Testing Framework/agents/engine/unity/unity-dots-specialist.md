# Agent 测试 Spec: unity-dots-specialist

## Agent 摘要
领域：ECS 架构（IComponentData, ISystem, SystemAPI）、Jobs 系统（IJob, IJobEntity, Burst）、Burst 编译器约束、DOTS 游戏玩法系统和混合渲染器。
不拥有：MonoBehaviour 游戏玩法代码（gameplay-programmer）、UI 实现（unity-ui-specialist）。
模型层级：Sonnet（默认）。
未分配 gate ID。

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 ECS / Jobs / Burst / IComponentData）
- [ ] `allowed-tools:` 列表包含 Read, Write, Edit, Bash, Glob, Grep
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义不声称对 MonoBehaviour 游戏玩法或 UI 系统拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出
**输入：** "将玩家移动系统转换为 ECS。"
**预期行为：**
- 生成：
  - 包含速度、加速度和输入向量字段的 `PlayerMovementData : IComponentData` 结构体
  - 使用 `SystemAPI.Query<>` 或 `IJobEntity` 的 `PlayerMovementSystem : ISystem` 及 `OnUpdate()`
  - 通过 `IBaker` 从 authoring MonoBehaviour 烘焙玩家初始状态
- 使用 `RefRW<LocalTransform>` 进行位置更新（而非已弃用的 `Translation`）
- 将任务标记为 `[BurstCompile]`，并指出哪些必须是非托管类型以保持 Burst 兼容
- 不修改输入轮询系统——从现有的 `PlayerInputData` 组件读取

### 用例 2：MonoBehaviour 回推
**输入：** "直接用 MonoBehaviour 做玩家移动——更简单。"
**预期行为：**
- 承认简单性的论点
- 解释 DOTS 的权衡：前期设置更多，但 ECS/Burst 方法提供了项目 ADR 或需求中记录的性能特性
- 如果项目已承诺使用 DOTS，则不实现 MonoBehaviour 版本
- 如果没有承诺，将架构决策标记给 `lead-programmer` / `technical-director` 解决
- 不单方面做出 MonoBehaviour vs. DOTS 的决定

### 用例 3：Burst 不兼容的托管内存
**输入：** "这个 Burst job 访问 `List<EnemyData>` 以查找最近的敌人。"
**预期行为：**
- 标记 `List<T>` 是与 Burst 编译不兼容的托管类型
- 不批准带有托管内存访问的 Burst job
- 提供正确的替代：根据用例使用 `NativeArray<EnemyData>`、`NativeList<EnemyData>` 或 `NativeHashMap<>`
- 指出 `NativeArray` 必须显式释放或通过 `[DeallocateOnJobCompletion]` 释放
- 使用非托管原生容器生成纠正后的 job

### 用例 4：混合访问——DOTS 系统需要 MonoBehaviour 数据
**输入：** "DOTS 移动系统需要读取由 MonoBehaviour CameraController 管理的相机变换。"
**预期行为：**
- 识别为混合访问场景
- 提供正确的混合模式：将相机变换存储在单例 `IComponentData` 中（每帧通过 `EntityManager.SetComponentData` 从 MonoBehaviour 侧更新）
- 另外建议 `CompanionComponent` / 托管组件方法
- 不从 Burst job 内部访问 MonoBehaviour——标记为不安全
- 提供 MonoBehaviour 侧（写入 ECS）和 DOTS 系统侧（从 ECS 读取）的桥接代码

### 用例 5：上下文传递——性能目标
**输入：** 来自上下文的 技术偏好：60fps 目标，每帧最多 2ms CPU 脚本预算。请求："为 10,000 个敌方实体设计 ECS chunk 布局。"
**预期行为：**
- 在设计理据中明确引用 2ms CPU 预算
- 为缓存效率设计 `IComponentData` chunk 布局：
  - 将频繁查询的组件分组到同一 archetype 中
  - 将不常用的数据分离到单独的组件中，使热数据保持紧凑
  - 根据 2ms 预算估算实体迭代时间
- 提供内存布局分析（每实体字节数，16KB chunk 大小下每 chunk 实体数）
- 不设计明显超出所述 2ms 预算而不标记的布局

---

## 协议合规性

- [ ] 保持在声明领域内（ECS, Jobs, Burst, DOTS 游戏玩法系统）
- [ ] 将仅 MonoBehaviour 的游戏玩法重定向到 gameplay-programmer
- [ ] 返回结构化输出（IComponentData 结构体、ISystem 实现、IBaker authoring 类）
- [ ] 标记 Burst job 中的托管内存访问为编译错误并提供非托管替代方案
- [ ] 在 DOTS 系统需要与 MonoBehaviour 系统交互时提供混合访问模式
- [ ] 根据提供的性能预算设计 chunk 布局

---

## 覆盖说明
- ECS 转换（用例 1）必须包含使用 ECS 测试框架（`World`, `EntityManager`）的单元测试
- Burst 不兼容（用例 3）是安全关键的——agent 必须在代码编写之前捕获这个问题
- Chunk 布局（用例 5）验证 agent 将定量性能推理应用于架构决策
