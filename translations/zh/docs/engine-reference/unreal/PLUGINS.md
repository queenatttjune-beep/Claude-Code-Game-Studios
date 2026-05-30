# Unreal Engine 5.7 — 可选插件和系统

**最后验证：** 2026-02-13

本文档索引了 Unreal Engine 5.7 中可用的**可选插件和系统**。这些不属于核心引擎，但常用于特定游戏类型。

---

## 如何使用本指南

**✅ 提供详细文档** - 参见 `plugins/` 目录获取全面指南
**🟡 仅简要概述** - 链接到官方文档，使用 WebSearch 获取详情
**⚠️ 实验性** - 未来版本可能有破坏性变更
**📦 需要插件** - 必须在 `Edit > Plugins` 中启用

---

## 生产就绪系统（提供详细文档）

### ✅ Gameplay Ability System（GAS）
- **用途：** 模块化能力系统（能力、属性、效果、冷却、消耗）
- **使用场景：** RPG、MOBA、带能力的射击游戏、任何基于能力的游戏机制
- **知识空白：** GAS 自 UE4 稳定，UE5 截止日期后有改进
- **状态：** 生产就绪
- **插件：** `GameplayAbilities`（内置，在 Plugins 中启用）
- **详细文档：** [plugins/gameplay-ability-system.md](plugins/gameplay-ability-system.md)
- **官方：** https://docs.unrealengine.com/5.7/en-US/gameplay-ability-system-for-unreal-engine/

---

### ✅ CommonUI
- **用途：** 跨平台 UI 框架（自动手柄/鼠标/触摸输入路由）
- **使用场景：** 多平台游戏（主机 + PC）、输入无关的 UI
- **知识空白：** 在 UE5+ 中生产就绪，截止日期后有重大改进
- **状态：** 生产就绪
- **插件：** `CommonUI`（内置，在 Plugins 中启用）
- **详细文档：** [plugins/common-ui.md](plugins/common-ui.md)
- **官方：** https://docs.unrealengine.com/5.7/en-US/commonui-plugin-for-advanced-user-interfaces-in-unreal-engine/

---

### ✅ Gameplay Camera System
- **用途：** 模块化摄像机管理（摄像机模式、混合、上下文感知摄像机）
- **使用场景：** 需要动态摄像机行为的游戏（第三人称、瞄准、载具）
- **知识空白：** UE 5.5 中新增，完全在截止日期之后
- **状态：** ⚠️ 实验性（UE 5.5-5.7）
- **插件：** `GameplayCameras`（内置，在 Plugins 中启用）
- **详细文档：** [plugins/gameplay-camera-system.md](plugins/gameplay-camera-system.md)
- **官方：** https://docs.unrealengine.com/5.7/en-US/gameplay-cameras-in-unreal-engine/

---

### ✅ PCG（程序化内容生成）
- **用途：** 基于节点的程序化世界生成（植被、道具、地形细节）
- **使用场景：** 开放世界、程序化关卡、大规模环境填充
- **知识空白：** UE 5.0-5.6 中实验性，5.7 中生产就绪
- **状态：** 生产就绪（自 UE 5.7 起）
- **插件：** `PCG`（内置，在 Plugins 中启用）
- **详细文档：** [plugins/pcg.md](plugins/pcg.md)
- **官方：** https://docs.unrealengine.com/5.7/en-US/procedural-content-generation-in-unreal-engine/

---

## 其他生产就绪插件（简要概述）

### 🟡 Mass Entity
- **用途：** 大规模 AI/人群的高性能 ECS（10,000+ 实体）
- **使用场景：** RTS、城市模拟器、巨大人群、大规模 AI
- **状态：** 生产就绪（UE 5.1+）
- **插件：** `MassEntity`、`MassGameplay`、`MassCrowd`
- **官方：** https://docs.unrealengine.com/5.7/en-US/mass-entity-in-unreal-engine/

---

### 🟡 Niagara Fluids
- **用途：** GPU 流体模拟（烟雾、火焰、液体）
- **使用场景：** 逼真的火焰/烟雾效果、水模拟
- **状态：** 实验性 → 生产就绪（UE 5.4+）
- **插件：** `NiagaraFluids`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/niagara-fluids-in-unreal-engine/

---

### 🟡 Water Plugin
- **用途：** 海洋、河流、湖泊渲染，带浮力
- **使用场景：** 有水域、船只、游泳的游戏
- **状态：** 生产就绪（UE 5.0+）
- **插件：** `Water`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/water-system-in-unreal-engine/

---

### 🟡 Landmass Plugin
- **用途：** 地形雕刻和景观编辑
- **使用场景：** 大规模地形修改、程序化景观
- **状态：** 生产就绪
- **插件：** `Landmass`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/landmass-plugin-in-unreal-engine/

---

### 🟡 Chaos Destruction
- **用途：** 实时断裂和破坏
- **使用场景：** 可破坏环境（墙壁、建筑、物体）
- **状态：** 生产就绪（UE 5.0+）
- **插件：** `ChaosDestruction`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/destruction-in-unreal-engine/

---

### 🟡 Chaos Vehicle
- **用途：** 高级车辆物理（轮式车辆、悬挂）
- **使用场景：** 赛车游戏、大量载具的游戏机制
- **状态：** 生产就绪（替代 PhysX Vehicles）
- **插件：** `ChaosVehicles`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/chaos-vehicles-overview-in-unreal-engine/

---

### 🟡 Geometry Scripting
- **用途：** 运行时程序化网格生成和编辑
- **使用场景：** 动态网格创建、程序化建模
- **状态：** 生产就绪（UE 5.1+）
- **插件：** `GeometryScripting`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/geometry-scripting-in-unreal-engine/

---

### 🟡 Motion Design Tools
- **用途：** 动态图形、程序化动画、关键帧动画
- **使用场景：** UI 动画、程序化运动、关键帧序列
- **状态：** 实验性 → 生产就绪（UE 5.4+）
- **插件：** `MotionDesign`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/motion-design-mode-in-unreal-engine/

---

## 实验性插件（谨慎使用）

### ⚠️ AI Assistant（UE 5.7+）
- **用途：** 编辑器内 AI 指导和帮助
- **状态：** 实验性
- **插件：** 在 UE 5.7 设置中启用
- **官方：** UE 5.7 发布中宣布

---

### ⚠️ OpenXR（VR/AR）
- **用途：** 跨平台 VR/AR 支持
- **使用场景：** VR/AR 游戏
- **状态：** VR 生产就绪，AR 实验性
- **插件：** `OpenXR`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/openxr-in-unreal-engine/

---

### ⚠️ Online Subsystem（EOS、Steam 等）
- **用途：** 平台无关的在线服务（匹配、好友、成就）
- **使用场景：** 带在线功能的多人游戏
- **状态：** 生产就绪
- **插件：** `OnlineSubsystem`、`OnlineSubsystemEOS`、`OnlineSubsystemSteam`
- **官方：** https://docs.unrealengine.com/5.7/en-US/online-subsystem-in-unreal-engine/

---

## 已弃用的插件（新项目避免使用）

### ❌ PhysX Vehicles
- **已弃用：** 改用 Chaos Vehicles
- **状态：** 传统，不推荐

---

### ❌ 旧 Replication Graph
- **已弃用：** 被 Iris 替代（UE 5.1+）
- **状态：** 使用 Iris 进行现代网络

---

## 按需 WebSearch 策略

对于上述未列出的插件，当用户询问时使用以下方法：

1. **WebSearch** 查找最新文档：`"Unreal Engine 5.7 [插件名称]"`
2. 验证插件是否：
   - 在截止日期之后（超出 2025 年 5 月训练数据）
   - 实验性 vs 生产就绪
   - 在 UE 5.7 中仍受支持
3. 可选择将结果缓存在 `plugins/[plugin-name].md` 以供将来参考

---

## 快速决策指南

**我需要能力/技能/增益** → **Gameplay Ability System（GAS）**
**我需要跨平台 UI（主机 + PC）** → **CommonUI**
**我需要动态摄像机** → **Gameplay Camera System**
**我需要程序化世界** → **PCG**
**我需要大规模人群（数千 AI）** → **Mass Entity**
**我需要可破坏环境** → **Chaos Destruction**
**我需要载具** → **Chaos Vehicles**
**我需要水/海洋** → **Water Plugin**
**我需要 VR/AR** → **OpenXR**

---

**最后更新：** 2026-02-13
**引擎版本：** Unreal Engine 5.7
**LLM 知识截止日期：** 2025 年 5 月
