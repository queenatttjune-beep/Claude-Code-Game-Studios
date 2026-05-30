# Godot 渲染 — 快速参考

最后验证：2026-02-12 | 引擎：Godot 4.6

## 自 ~4.3（LLM 截止日期）以来的变化

### 4.6 变化
- **D3D12 是 Windows 上的默认渲染后端**（以前是 Vulkan）
- **发光在色调映射之前处理**（以前是在之后）— 使用屏幕混合模式
- **AgX 色调映射器**：新的白点和对比度控制
- **SSR 全面改造**：更好的真实感、视觉稳定性和性能

### 4.5 变化
- **着色器烘焙器**：预编译着色器以减少启动时间
- **SMAA 1x**：新的抗锯齿选项（比 FXAA 更锐利，比 TAA 更便宜）
- **模板缓冲支持**：支持选择性几何体遮罩/传送门效果
- **弯曲法线贴图**：法线贴图纹理中编码的方向性遮挡
- **镜面反射遮挡**：环境遮挡现在正确影响反射

### 4.4 变化
- **`RenderingDevice.draw_list_begin`**：移除了许多参数；添加了可选的 `breadcrumb`
- **着色器纹理类型**：从 `Texture2D` 更改为 `Texture` 基类型
- **粒子 `.restart()`**：添加了可选的 `keep_seed` 参数

### 4.3 变化（在训练数据中）
- **Compositor 节点**：用于后处理链的 `Compositor` + `CompositorEffect`

## 当前 API 模式

### 后处理（4.3+）
```gdscript
# 使用 Compositor 节点 — 而不是手动视口着色器链
# 将 Compositor 添加为 WorldEnvironment 或 Camera3D 的子节点
# 为每个后处理步骤创建 CompositorEffect 资源
```

### 抗锯齿选项（4.6）
```
项目设置 → 渲染 → 抗锯齿：
- MSAA 2D/3D：硬件 MSAA（画质好但开销大）
- 屏幕空间 AA：FXAA（快速但模糊）或 SMAA（锐利、中等开销）  # SMAA 在 4.5 中新增
- TAA：时间抗锯齿（画质最好，快速运动时有鬼影）
```

### 渲染后端选择（4.6）
```
项目设置 → 渲染 → 渲染器：
- Forward+（默认）：功能完整，面向桌面
- Mobile：针对移动/低端设备优化，功能有限
- Compatibility：OpenGL 3.3 / WebGL 2，硬件支持最广泛

Windows 默认后端：D3D12（4.6 之前是 Vulkan）
```

## 常见错误
- 假设 Vulkan 是 Windows 上的默认后端（自 4.6 起为 D3D12）
- 使用手动视口链而不是 Compositor 进行后处理
- 在着色器统一类型中使用 `Texture2D`（自 4.4 起使用 `Texture`）
- 对于有许多着色器变体的项目未使用 Shader Baker
