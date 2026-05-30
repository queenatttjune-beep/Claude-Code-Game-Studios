# Unreal Engine 5.7 — 破坏性变更

**最后验证：** 2026-02-13

本文档跟踪 Unreal Engine 5.3（可能在模型训练中）和 Unreal Engine 5.7（当前版本）之间的破坏性 API 变更和行为差异。按风险级别组织。

## 高风险 — 将破坏现有代码

### Substrate 材质系统（5.7 中生产就绪）
**版本：** UE 5.5+（实验性）、5.7（生产就绪）

Substrate 用模块化、物理精确的框架取代了传统材质系统。

```cpp
// ❌ 旧：传统材质节点（仍可用但已弃用）
// 使用标准材质图：Base Color、Metallic、Roughness 等

// ✅ 新：Substrate 材质层
// 使用 Substrate 节点：Substrate Slab、Substrate Blend 等
// 模块化材质创作，真正的物理精度
```

**迁移：** 在 `Project Settings > Engine > Substrate` 中启用 Substrate，并使用 Substrate 节点重建材质。

---

### PCG（程序化内容生成）API 全面改造
**版本：** UE 5.7（生产就绪）

PCG 框架已达到生产就绪状态，并进行了主要的 API 变更。

```cpp
// ❌ 旧：实验性 PCG API（5.7 之前）
// 旧节点类型，不稳定的 API

// ✅ 新：生产 PCG API（5.7+）
// 使用 FPCGContext、IPCGElement、新节点类型
// 稳定的 API，生产就绪的工作流
```

**迁移：** 遵循 5.7 文档中的 PCG 迁移指南。实验性 PCG 代码需要重大重构。

---

### Megalights 渲染系统
**版本：** UE 5.5+

新的照明系统支持数百万动态光源。

```cpp
// ❌ 旧：有限的动态光源（集群前向着色）
// 性能下降前最多约 100-200 动态光源

// ✅ 新：Megalights（5.5+）
// 数百万动态光源，最小性能开销
// 启用：Project Settings > Engine > Rendering > Megalights
```

**迁移：** 无需代码更改，但光照行为可能不同。启用后测试场景。

---

## 中风险 — 行为变更

### Enhanced Input System（现在为默认）
**版本：** UE 5.1+（推荐）、5.7（默认）

Enhanced Input 现在是默认输入系统。

```cpp
// ❌ 旧：传统输入绑定（已弃用）
InputComponent->BindAction("Jump", IE_Pressed, this, &ACharacter::Jump);

// ✅ 新：Enhanced Input
SetupPlayerInputComponent(UInputComponent* PlayerInputComponent) {
    UEnhancedInputComponent* EIC = Cast<UEnhancedInputComponent>(PlayerInputComponent);
    EIC->BindAction(JumpAction, ETriggerEvent::Started, this, &ACharacter::Jump);
}
```

**迁移：** 用 Enhanced Input 动作替换传统输入绑定。

---

### Nanite 默认启用
**版本：** UE 5.0+（可选）、5.7（推荐）

Nanite 虚拟化几何体现在是静态网格的推荐工作流。

```cpp
// 在静态网格上启用 Nanite：
// Static Mesh Editor > Details > Nanite Settings > Enable Nanite Support
```

**迁移：** 将高多边形网格转换为 Nanite。在目标平台上测试性能。

---

## 低风险 — 弃用（仍可运行）

### 传统材质系统
**状态：** 已弃用但受支持
**替代品：** Substrate 材质系统

传统材质仍然可用，但新项目推荐 Substrate。

---

### 旧 World Partition（UE4 风格）
**状态：** 已弃用
**替代品：** World Partition（UE5+）

大型世界使用 UE5 的 World Partition 系统。

---

## 平台特定的破坏性变更

### Windows
- **UE 5.7**：DirectX 12 现在是默认（旧版本中是 DX11）
- 更新着色器以兼容 DX12

### macOS
- **UE 5.5+**：需要 Metal 3（最低 macOS 13）

### 移动端
- **UE 5.7**：最低 Android API 级别提升至 26（Android 8.0）
- 最低 iOS 部署目标提升至 iOS 14

---

## 迁移清单

从 UE 5.3 升级到 UE 5.7 时：

- [ ] 审查 Substrate 材质（如果准备好使用新系统则转换）
- [ ] 审计 PCG 使用情况（如果使用实验性 API，更新到生产 API）
- [ ] 测试 Megalights 性能（启用并基准测试）
- [ ] 将传统输入迁移到 Enhanced Input
- [ ] 将高多边形网格转换为 Nanite
- [ ] 更新着色器以兼容 DX12（Windows）或 Metal 3（macOS）
- [ ] 验证最低平台版本（Android 8.0、iOS 14）
- [ ] 在目标硬件上测试 Lumen 和 Nanite 性能

---

**来源：**
- https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5-7-release-notes
- https://dev.epicgames.com/documentation/en-us/unreal-engine/upgrading-projects-to-newer-versions-of-unreal-engine
