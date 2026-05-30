# Agent 测试 Spec: unity-shader-specialist

## Agent 摘要
领域：Unity Shader Graph、自定义 HLSL、VFX Graph、URP/HDRP 管线定制和后处理效果。
不拥有：游戏玩法代码、艺术风格方向。
模型层级：Sonnet（默认）。
未分配 gate ID。

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 Shader Graph / HLSL / VFX Graph / URP / HDRP）
- [ ] `allowed-tools:` 列表包含 Read, Write, Edit, Glob, Grep
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义不声称对游戏玩法代码或艺术方向拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出
**输入：** "在 URP 中使用 Shader Graph 为角色创建描边效果。"
**预期行为：**
- 生成 Shader Graph 节点设置描述：
  - 反向外壳法：缩放法线 → 顶点阶段的顶点偏移，Cull Front
  - 或基于深度/法线边缘检测的屏幕空间后处理描边
- 根据 URP 能力推荐合适的方法（URP 兼容的反向外壳法，HDRP 的后处理方法）
- 指出 URP 限制：不支持几何着色器（排除了几何着色器描边方法）
- 在未确认渲染管线的情况下，不生成 HDRP 特定节点

### 用例 2：领域外重定向
**输入：** "用代码实现角色生命条 UI。"
**预期行为：**
- 不生成 UI 实现代码
- 明确指出 UI 实现属于 `ui-programmer`（或 `unity-ui-specialist`）
- 适当重定向请求
- 可以指出基于着色器的生命条填充效果（例如溶解/填充渐变）在其领域内（如果视觉效果本身是由着色器驱动的）

### 用例 3：用于描边的 HDRP 自定义 Pass
**输入：** "我们使用 HDRP，想要将描边作为后处理效果。"
**预期行为：**
- 生成 HDRP `CustomPassVolume` 模式：
  - 继承 `CustomPass` 的 C# 类
  - 使用 `CoreUtils.SetRenderTarget()` 和全屏着色器 blit 的 `Execute()` 方法
  - 用于边缘检测的深度/法线缓冲区采样
- 指出 CustomPass 需要 HDRP 包，在 URP 中不工作
- 在提供 HDRP 特定代码前确认项目使用 HDRP

### 用例 4：VFX Graph 性能——GPU 事件批处理
**输入：** "爆炸 VFX Graph 每个事件有 10,000 个粒子，同时生成 20 个爆炸导致 GPU 帧尖峰。"
**预期行为：**
- 识别 GPU 粒子生成为成本驱动因素（200,000 个同时粒子）
- 提出 GPU 事件批处理：将生成事件分散到多帧，错开初始化
- 建议每个活跃爆炸的粒子预算上限（例如每个爆炸 3,000 个，超额排队）
- 指出 VFX Graph Event Batcher 模式和用于跨帧分布的 Output Event API
- 不改变游戏玩法事件系统——提出 VFX 侧的预算解决方案

### 用例 5：上下文传递——渲染管线（URP 或 HDRP）
**输入：** 项目上下文：URP 渲染管线，Unity 2022.3。请求："添加景深后处理。"
**预期行为：**
- 使用 URP Volume 框架：`DepthOfField` Volume Override 组件
- 不使用 HDRP Volume 组件（例如具有不同参数名称的 HDRP 的 `DepthOfField`）
- 指出 URP 特定的 DOF 限制与 HDRP 的对比（例如 Bokeh 质量差异）
- 生成与 Unity 2022.3 URP 包版本兼容的 C# Volume profile 设置代码

---

## 协议合规性

- [ ] 保持在声明领域内（Shader Graph, HLSL, VFX Graph, URP/HDRP 定制）
- [ ] 将游戏玩法和 UI 代码重定向到相应的 agent
- [ ] 返回结构化输出（节点图描述、HLSL 代码、CustomPass 模式）
- [ ] 区分 URP 和 HDRP 方法——从不混用管线特定 API
- [ ] 在相关时将几何着色器方法标记为 URP 不兼容
- [ ] 生成不改变游戏玩法行为的 VFX 优化

---

## 覆盖说明
- 描边效果（用例 1）应配合 `production/qa/evidence/` 中的视觉截图测试
- HDRP CustomPass（用例 3）确认 agent 生成正确的 Unity 模式，而非通用后处理方法
- 管线分离（用例 5）验证 agent 在无上下文时从不假设渲染管线
