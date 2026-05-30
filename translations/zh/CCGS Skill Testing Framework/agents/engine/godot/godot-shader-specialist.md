# Agent 测试 Spec: godot-shader-specialist

## Agent 摘要
领域：Godot 着色语言（GLSL 衍生）、可视化着色器（VisualShader 图形）、材质设置、粒子着色器和后处理效果。
不拥有：游戏玩法代码、艺术风格方向。
模型层级：Sonnet（默认）。
未分配 gate ID。

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 Godot 着色语言 / 材质 / 后处理）
- [ ] `allowed-tools:` 列表包含 Read, Write, Edit, Glob, Grep
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义引用 `docs/engine-reference/godot/VERSION.md` 作为 Godot 着色器 API 变更的权威来源

---

## 测试用例

### 用例 1：领域内请求——正确输出
**输入：** "在 Godot 中为敌人死亡编写溶解效果着色器。"
**预期行为：**
- 生成有效的 Godot 着色语言代码（不是 HLSL，不是直接 GLSL）
- 适当使用 `shader_type spatial;` 或 `canvas_item`
- 定义 `uniform float dissolve_amount : hint_range(0.0, 1.0);`
- 采样噪点纹理以确定逐像素溶解阈值
- 对低于阈值的像素使用 `discard;`
- 可选择在溶解边界附近使用 emission 添加边缘辉光
- 代码在 Godot 着色语言的语法上正确

### 用例 2：HLSL 重定向
**输入：** "为此溶解效果编写 HLSL compute shader。"
**预期行为：**
- 不生成 HLSL 代码
- 明确说明："Godot 不直接使用 HLSL；它使用自己的着色语言（GLSL 衍生）"
- 将 HLSL 意图转换为等效的 Godot 着色器方法
- 指出 RenderingDevice compute shader 在 Godot 4 中可用但属于低级 API，并相应标记如果那是意图

### 用例 3：后 cutoff API 变更——纹理采样（Godot 4.4）
**输入：** "在着色器中使用 `texture()` 和 sampler2D 采样噪点纹理。"
**预期行为：**
- 检查版本引用：Godot 4.4 更改了纹理采样器类型声明
- 标记潜在的 API 变更：`sampler2D` 语法和 `texture()` 调用行为可能与 pre-4.4 不同
- 为项目的固定版本（4.6）提供正确的语法，如迁移说明中所述
- 不在不标记版本风险的情况下使用 pre-4.4 纹理采样语法

### 用例 4：片段着色器 LOD 策略
**输入：** "水面的片段着色器有 8 个纹理采样，在中端硬件上导致 GPU 瓶颈。"
**预期行为：**
- 识别每片段纹理采样数为主要成本驱动因素
- 提出 LOD 策略：
  - 减少距离上的采样数（基于距离的着色器变体或 LOD 级别）
  - 离线预烘焙一些纹理组合
  - 对远距离采样使用较低分辨率的噪点纹理
- 提供实现 LOD 方法的着色器代码修改
- 不改变水系统的游戏玩法行为

### 用例 5：上下文传递——Godot 4.6 辉光重做
**输入：** 引擎版本上下文：Godot 4.6。请求："向场景添加 bloom/glow 后处理效果。"
**预期行为：**
- 引用 VERSION.md 说明：Godot 4.6 包括辉光重做
- 使用 4.6 WorldEnvironment 方法生成辉光配置指导，而非 pre-4.6 API
- 明确指出 4.6 辉光重做中哪些属性或参数发生了变化
- 标记 LLM 训练数据可能因后 cutoff 时间而具有错误信息的任何属性

---

## 协议合规性

- [ ] 保持在声明领域内（Godot 着色语言、材质、VFX 着色器、后处理）
- [ ] 将游戏玩法代码请求重定向到 gameplay-programmer
- [ ] 生成有效的 Godot 着色语言——从不使用没有 Godot 包装器的 HLSL 或原始 GLSL
- [ ] 检查引擎版本引用以了解后 cutoff 着色器 API 变更（4.4 纹理类型、4.6 辉光重做）
- [ ] 返回结构化输出（带有文档化 uniform 的着色器代码、带有性能理据的 LOD 策略）
- [ ] 标记任何后 cutoff API 用法为需要验证

---

## 覆盖说明
- 溶解着色器（用例 1）应配合 `production/qa/evidence/` 中的视觉测试截图
- 纹理 API 标记（用例 3）确认 agent 在使用 post-4.3 变更的 API 前检查 VERSION.md
- 辉光重做（用例 5）是 Godot 4.6 特定的测试——验证 agent 应用最新的迁移说明
