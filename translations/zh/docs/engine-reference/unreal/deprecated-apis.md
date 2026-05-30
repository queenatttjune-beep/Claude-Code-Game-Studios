# Unreal Engine 5.7 — 弃用的 API

**最后验证：** 2026-02-13

弃用 API 及其替代品的快速查找表。
格式：**不要用 X** → **改用 Y**

---

## 输入

| 弃用 | 替代品 | 备注 |
|------------|-------------|-------|
| `InputComponent->BindAction()` | Enhanced Input `BindAction()` | 新输入系统 |
| `InputComponent->BindAxis()` | Enhanced Input `BindAxis()` | 新输入系统 |
| `PlayerController->GetInputAxisValue()` | Enhanced Input Action Values | 新输入系统 |

**迁移：** 安装 Enhanced Input 插件，创建 Input Actions 和 Input Mapping Contexts。

---

## 渲染

| 弃用 | 替代品 | 备注 |
|------------|-------------|-------|
| 传统材质节点 | Substrate 材质节点 | Substrate 在 5.7 中生产就绪 |
| 前向着色（默认） | 延迟 + Lumen | Lumen 在 UE5 中是默认 |
| 旧光照工作流 | Lumen 全局光照 | 实时 GI |

---

## 世界构建

| 弃用 | 替代品 | 备注 |
|------------|-------------|-------|
| UE4 World Composition | World Partition（UE5） | 大型世界流式传输 |
| Level Streaming Volumes | World Partition Data Layers | 更好的关卡流式传输 |

---

## 动画

| 弃用 | 替代品 | 备注 |
|------------|-------------|-------|
| 旧动画重定向 | IK Rig + IK Retargeter | UE5 重定向系统 |
| 传统 Control Rig | Control Rig 2.0 | 生产就绪的骨骼绑定 |

---

## 游戏机制

| 弃用 | 替代品 | 备注 |
|------------|-------------|-------|
| `UGameplayStatics::LoadStreamLevel()` | World Partition 流式传输 | 使用 Data Layers |
| 硬编码输入绑定 | Enhanced Input 系统 | 可重新绑定、模块化输入 |

---

## Niagara（VFX）

| 弃用 | 替代品 | 备注 |
|------------|-------------|-------|
| Cascade 粒子系统 | Niagara | Cascade 已完全弃用 |

---

## 音频

| 弃用 | 替代品 | 备注 |
|------------|-------------|-------|
| 旧音频混合器 | MetaSounds | 程序化音频系统 |
| Sound Cue（用于复杂逻辑） | MetaSounds | 更强大、基于节点 |

---

## 网络

| 弃用 | 替代品 | 备注 |
|------------|-------------|-------|
| `DOREPLIFETIME()`（基本） | `DOREPLIFETIME_CONDITION()` | 条件复制用于优化 |

---

## C++ 脚本

| 弃用 | 替代品 | 备注 |
|------------|-------------|-------|
| `TSharedPtr<T>`（用于 UObject） | `TObjectPtr<T>` | UE5 类型安全指针 |
| 手动 RTTI 检查 | `Cast<T>()` / `IsA<T>()` | 类型安全转换 |

---

## 快速迁移模式

### 输入示例
```cpp
// ❌ 已弃用
void AMyCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent) {
    PlayerInputComponent->BindAction("Jump", IE_Pressed, this, &ACharacter::Jump);
}

// ✅ Enhanced Input
#include "EnhancedInputComponent.h"

void AMyCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent) {
    UEnhancedInputComponent* EIC = Cast<UEnhancedInputComponent>(PlayerInputComponent);
    if (EIC) {
        EIC->BindAction(JumpAction, ETriggerEvent::Started, this, &ACharacter::Jump);
    }
}
```

### 材质示例
```cpp
// ❌ 已弃用：传统材质
// 使用标准材质图（仍可用但不推荐）

// ✅ Substrate 材质
// 启用：Project Settings > Engine > Substrate > Enable Substrate
// 在材质编辑器中使用 Substrate 节点
```

### World Partition 示例
```cpp
// ❌ 已弃用：关卡流式传输体积
// 手动加载/卸载关卡

// ✅ World Partition
// 启用：World Settings > Enable World Partition
// 使用 Data Layers 进行流式传输
```

### 粒子系统示例
```cpp
// ❌ 已弃用：Cascade
UParticleSystemComponent* PSC = CreateDefaultSubobject<UParticleSystemComponent>(TEXT("Particles"));

// ✅ Niagara
UNiagaraComponent* NiagaraComp = CreateDefaultSubobject<UNiagaraComponent>(TEXT("Niagara"));
```

### 音频示例
```cpp
// ❌ 已弃用：基于复杂逻辑的 Sound Cue
// 使用 Sound Cue 编辑器节点

// ✅ MetaSounds
// 创建 MetaSound Source 资源，使用基于节点的音频
```

---

## 总结：UE 5.7 技术栈

| 特性 | 使用这个（2026） | 避免这个（传统） |
|---------|------------------|----------------------|
| **输入** | Enhanced Input | 传统输入绑定 |
| **材质** | Substrate | 传统材质系统 |
| **光照** | Lumen + Megalights | 光照贴图 + 有限光源 |
| **粒子** | Niagara | Cascade |
| **音频** | MetaSounds | Sound Cue（用于逻辑） |
| **世界流式传输** | World Partition | World Composition |
| **动画重定向** | IK Rig + Retargeter | 旧重定向 |
| **几何体** | Nanite（高多边形） | 标准静态网格 LOD |

---

**来源：**
- https://docs.unrealengine.com/5.7/en-US/deprecated-and-removed-features/
- https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5-7-release-notes
