---
paths:
  - "assets/shaders/**"
---

# 着色器代码标准

`assets/shaders/` 中的所有着色器文件必须遵循这些标准，以保持视觉质量、性能和跨平台兼容性。

## 命名规范
- 文件命名：`[type]_[category]_[name].[ext]`
  - `spatial_env_water.gdshader`（Godot）
  - `SG_Env_Water`（Unity Shader Graph）
  - `M_Env_Water`（Unreal Material）
- 使用描述性名称，指示材质用途
- 使用着色器类型前缀：`spatial_`、`canvas_`、`particles_`、`post_`

## 代码质量
- 所有 uniform/参数必须有描述性名称和适当的提示
- 相关参数分组（Godot：`group_uniforms`、Unity：`[Header]`、Unreal：Category）
- 注释非显而易见的计算（尤其是数学密集的章节）
- 无魔数 — 使用命名常量或文档化的 uniform 值
- 在每个着色器文件顶部包含作者和用途注释

## 性能要求
- 记录每个着色器的目标平台和复杂度预算
- 在移动端使用适当的精度：`half`/`mediump`（当不需要完整精度时）
- 尽量减少片元着色器中的纹理采样
- 避免在片元着色器中使用动态分支 — 使用 `step()`、`mix()`、`smoothstep()`
- 循环中无纹理读取
- 模糊效果使用两遍方法（水平然后垂直）

## 跨平台
- 在最低规格目标硬件上测试着色器
- 为较低质量级别提供回退/简化版本
- 记录着色器目标渲染管线（Forward/Deferred、URP/HDRP、Forward+/Mobile/Compatibility）
- 不要在同一目录中混合不同渲染管线的着色器

## 变体管理
- 最小化着色器变体 — 每个变体是一个单独的编译着色器
- 记录所有关键字/变体及其用途
- 在可能的情况下使用功能剥离以减少构建大小
- 记录并监控每个着色器的总变体数量
