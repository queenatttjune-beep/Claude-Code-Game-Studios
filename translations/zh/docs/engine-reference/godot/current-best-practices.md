# Godot — 当前最佳实践

最后验证：2026-02-12 | 引擎：Godot 4.6

自模型训练数据（~4.3）以来**新增或更改**的实践。
这补充（而非替换）了 agent 的内置知识。

## GDScript（4.5+）

- **可变参数**：函数可以接受任意数量的参数
  ```gdscript
  func log_values(prefix: String, values: Variant...) -> void:
      for v in values:
          print(prefix, ": ", v)
  ```

- **抽象类和方法**：使用 `@abstract` 强制执行继承
  ```gdscript
  @abstract
  class_name BaseEnemy extends CharacterBody3D

  @abstract
  func get_attack_pattern() -> Array[Attack]:
      pass  # 子类必须重写
  ```

- **脚本回溯**：即使在 Release 构建中也可获得详细调用栈

## 物理（4.6）

- **Jolt Physics 是新项目的默认 3D 引擎**
  - 比 GodotPhysics3D 更好的确定性和稳定性
  - 某些 HingeJoint3D 属性（`damp`）仅适用于 GodotPhysics
  - 切换：项目设置 → 物理 → 3D → 物理引擎
  - 2D 物理不变（仍是 Godot Physics 2D）

## 渲染（4.6）

- **D3D12 是 Windows 上的默认后端**（以前是 Vulkan）— 为了更好的驱动程序兼容性
- **发光现在在色调映射之前处理**，使用屏幕混合模式 — 现有发光设置可能看起来不同
- **SSR 全面改造** — 在真实感、稳定性和性能方面有显著改进
- **AgX 色调映射器** — 新的白点和对比度控制

## 渲染（4.5）

- **着色器烘焙器**：预编译着色器以消除启动卡顿
- **SMAA 1x**：新的抗锯齿选项 — 比 FXAA 更锐利，比 TAA 更便宜
- **模板缓冲**：可用于高级遮罩/传送门效果
- **弯曲法线贴图**：法线贴图纹理中的方向性遮挡
- **镜面反射遮挡**：环境遮挡现在影响反射

## 无障碍（4.5+）

- **屏幕阅读器支持**：控制节点通过 AccessKit 与无障碍工具集成
- **实时翻译预览**：直接在编辑器中测试不同语言的 GUI 布局
- **FoldableContainer**：用于可折叠部分的新手风琴式 UI 节点
- **递归 Control 禁用**：通过单个属性禁用整个节点层次的鼠标/焦点交互

## 动画（4.5+）

- **BoneConstraint3D**：使用修改器将骨骼绑定到其他骨骼
  - AimModifier3D、CopyTransformModifier3D、ConvertTransformModifier3D

## 动画（4.6）

- **IK 系统完全恢复**：为 3D 重新引入完整的逆向运动学
  - 可用修改器：CCDIK、FABRIK、Jacobian IK、Spline IK、TwoBoneIK
  - 通过 `SkeletonModifier3D` 节点应用

## 资源（4.5+）

- **`duplicate_deep()`**：用于嵌套资源树的显式深度复制
  - 旧的 `duplicate()` 行为保留以确保向后兼容
  - 当需要嵌套资源的每实例副本时使用 `duplicate_deep()`

## 导航（4.5+）

- **专用 2D 导航服务器**：不再通过 3D NavigationServer 代理
  - 减少纯 2D 游戏的导出二进制体积

## UI（4.6）

- **双焦点系统**：鼠标/触摸焦点现在与键盘/手柄焦点分离
  - 根据输入方法显示不同的视觉反馈
  - 在设计自定义焦点行为时需考虑此变化

## 编辑器工作流（4.6）

- 灵活的停靠面板拖放，带蓝色轮廓预览（包括底部面板）
- 大多数面板支持浮动窗口（调试器除外）
- 新键盘快捷键：Alt+O（输出）、Alt+S（着色器）
- 导出变量自动生成：从文件系统将资源拖入脚本编辑器
- 快速打开对话框中启用 "Live Preview" 时的实时预览
- 新的 "选择模式"（v 键）防止意外变换；旧模式重命名为 "变换模式"（q 键）

## 工具

- **ripgrep 没有 `gdscript` 类型**：`*.gd` 注册在 `gap`（GAP 编程语言）下。
  `rg --type gdscript` 是硬错误 — 搜索永远不会执行。
  始终使用 `rg --glob "*.gd"`（shell）或 `glob: "*.gd"`（Grep 工具）来过滤 GDScript 文件。

## 平台（4.5+）

- **visionOS 导出**：自开源以来第一个新平台（窗口化应用模式）
- **SDL3 手柄驱动**：更好的跨平台手柄支持
- **Android**：全屏显示、相机流访问、16KB 页面支持（Android 15+）
- **Linux**：用于多窗口功能的 Wayland 子窗口支持
