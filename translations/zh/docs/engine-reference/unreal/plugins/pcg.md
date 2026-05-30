# Unreal Engine 5.7 — PCG（程序化内容生成）

**最后验证：** 2026-02-13
**状态：** 生产就绪（自 UE 5.7 起）
**插件：** `PCG`（内置，在 Plugins 中启用）

---

## 概述

**程序化内容生成（PCG）** 是 Unreal 基于节点的框架，用于大规模生成程序化内容。专为用植被、岩石、道具、建筑和其他环境细节填充大型开放世界而设计。

**使用 PCG 的场景：**
- 程序化植被放置（树木、草地、岩石）
- 基于生物群系的环境生成
- 道路/路径生成
- 建筑/结构放置
- 世界细节填充（道具、杂物）

**不要使用 PCG 的场景：**
- 游戏逻辑（使用蓝图/C++）
- 一次性手动放置（使用编辑器工具）

**⚠️ 注意：** PCG 在 UE 5.0-5.6 中是实验性的，在 UE 5.7 中成为生产就绪。

---

## 核心概念

### 1. **PCG Graph**
- 基于节点的图（类似于 Material Editor）
- 定义生成规则

### 2. **PCG Component**
- 放置在关卡中，执行 PCG Graph
- 在定义的体积内生成内容

### 3. **PCG Data**
- 点数据（位置、旋转、缩放）
- 样条线数据（路径、道路、河流）
- 体积数据（密度、生物群系遮罩）

### 4. **Nodes**
- **Samplers**：生成点（Grid、Poisson、Surface）
- **Filters**：基于规则移除点（Density、Tag、Bounds）
- **Modifiers**：变换点（Offset、Rotate、Scale）
- **Spawners**：在点处实例化网格/actor

---

## 设置

### 1. 启用插件

`Edit > Plugins > PCG > Enabled > Restart`

### 2. 创建 PCG Volume

1. Place Actors > Volumes > PCG Volume
2. 缩放体积到所需的生成区域

### 3. 创建 PCG Graph

1. Content Browser > PCG > PCG Graph
2. 打开 PCG Graph Editor

---

## 基本工作流

### 示例：森林生成

#### 1. 创建 PCG Graph

**节点设置：**
```
Input（体积）
  ↓
Surface Sampler（采样体积表面，每 m² 点数：0.5）
  ↓
Density Filter（使用纹理遮罩或噪声）
  ↓
Static Mesh Spawner（树木网格）
  ↓
Output
```

#### 2. 将图分配给体积

1. 选择 PCG Volume
2. Details Panel > PCG Component > Graph = 你的 PCG Graph
3. 点击 "Generate" 按钮

---

## 关键节点类型

### Samplers（点生成）

#### Grid Sampler
- 点的规则网格
- 配置：
  - **Grid Size**：点之间的距离
  - **Offset**：每点的随机偏移

#### Poisson Disk Sampler
- 具有最小距离的随机点
- 配置：
  - **Points Per m²**：密度
  - **Min Distance**：点之间的间距

#### Surface Sampler
- 在网格表面或地形上的点
- 配置：
  - **Points Per m²**：密度
  - **Surface Only**：仅表面，非体积

---

### Filters（点移除）

#### Density Filter
- 基于密度值移除点
- 输入：纹理或噪声
- 用于：生物群系遮罩、空地、路径

#### Tag Filter
- 按标签过滤点
- 用于：条件生成

#### Bounds Filter
- 仅保留边界内的点
- 用于：限制生成到特定区域

---

### Modifiers（点变换）

#### Rotate
- 随机化点旋转
- 配置：
  - **Min/Max Rotation**：每轴的旋转范围

#### Scale
- 随机化点缩放
- 配置：
  - **Min/Max Scale**：缩放范围

#### Project to Ground
- 将点对齐到地形表面

---

### Spawners（网格/Actor 实例化）

#### Static Mesh Spawner
- 在点处生成静态网格
- 配置：
  - **Mesh List**：网格数组（随机选择）
  - **Culling Distance**：LOD/裁切设置

#### Actor Spawner
- 在点处生成蓝图 actor
- 用于：游戏 actor、交互对象

---

## 数据源

### Landscape
- 使用地形作为采样输入
- 自动投影到地形高度

### Splines
- 沿样条线生成内容（道路、河流、路径）
- 示例：沿路径的树木

### Textures
- 使用纹理作为密度遮罩
- 绘制生物群系、空地、区域

---

## 生物群系示例（混合森林）

### 图设置

```
Input（地形）
  ↓
Surface Sampler（密度：1.0）
  ↓
┌─────────────────┬─────────────────┐
│ Tree Biome      │ Rock Biome      │
│（密度 > 0.5）    │（密度 < 0.5）    │
├─────────────────┼─────────────────┤
│ Tree Spawner    │ Rock Spawner    │
└─────────────────┴─────────────────┘
  ↓
Merge
  ↓
Output
```

---

## 基于样条线的生成（带树木的道路）

### 1. 创建 PCG Graph

```
Spline Input
  ↓
Spline Sampler（沿样条线采样）
  ↓
Offset（从样条线路径偏移）
  ↓
Tree Spawner
  ↓
Output
```

### 2. 向 PCG Volume 添加 Spline 组件

1. PCG Volume > Add Component > Spline
2. 绘制样条线路径
3. PCG Graph 读取样条线数据

---

## 运行时生成

### 从 C++ 触发生成

```cpp
#include "PCGComponent.h"

UPCGComponent* PCGComp = /* 获取 PCG 组件 */;
PCGComp->Generate(); // 执行 PCG 图
```

### 流式传输生成（大型世界）

- PCG 自动与 World Partition 一起流式传输
- 仅在已加载的单元中生成内容

---

## 性能

### 优化提示

- 在生成的网格上使用**裁切距离**（LOD）
- 限制**密度**（更少的点 = 更好的性能）
- 对重复网格使用**分层实例化静态网格（HISM）**
- 为大型世界启用**流式传输**

### 调试性能

```cpp
// 控制台命令：
// pcg.graph.debug 1 - 显示 PCG 调试信息
// stat pcg - 显示 PCG 性能统计信息
```

---

## 常见模式

### 带空地的森林

```
Surface Sampler
  ↓
Density Filter（带空地的噪声纹理）
  ↓
Tree Spawner（松树、橡树、桦树）
```

### 陡坡上的岩石

```
Landscape Input
  ↓
Surface Sampler
  ↓
Slope Filter（角度 > 30°）
  ↓
Rock Spawner
```

### 道路旁的道具

```
Spline Input（道路样条线）
  ↓
Spline Sampler
  ↓
Offset（道路旁边）
  ↓
Street Light Spawner
```

---

## 调试

### PCG 调试可视化

```cpp
// 控制台命令：
// pcg.debug.display 1 - 显示点和生成边界
// pcg.debug.colormode points - 颜色编码点
```

### 图调试

- PCG Graph Editor > Debug > Show Debug Points
- 可视化图中每个节点的点

---

## 从 UE 5.6（实验性）迁移到 5.7（生产）

### API 变更

```cpp
// ❌ 旧（5.6 实验性 API）：
// 某些节点重命名，API 不稳定

// ✅ 新（5.7 生产 API）：
// 稳定的节点类型，文档齐全的 API
```

**迁移：** 使用稳定的 5.7 节点重建 PCG 图。彻底测试。

---

## 限制

- **不用于游戏逻辑**：游戏规则使用蓝图/C++
- **大型图可能较慢**：通过过滤器和密度降低优化
- **运行时生成开销**：尽可能预生成

---

## 来源
- https://docs.unrealengine.com/5.7/en-US/procedural-content-generation-in-unreal-engine/
- https://docs.unrealengine.com/5.7/en-US/pcg-quick-start-in-unreal-engine/
- UE 5.7 发布说明（PCG 生产就绪公告）
