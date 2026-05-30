# Unreal Engine 5.7 — 动画模块参考

**最后验证：** 2026-02-13
**知识空白：** UE 5.7 动画创作改进、Control Rig 2.0

---

## 概述

UE 5.7 动画系统：
- **Animation Blueprint**：基于状态机的动画逻辑
- **Control Rig**：运行时过程动画（UE5 中生产就绪）
- **IK Rig + Retargeter**：现代重定向系统
- **Sequencer**：过场动画

---

## Animation Blueprint

### 创建 Animation Blueprint

1. Content Browser > Right Click > Animation > Animation Blueprint
2. 选择父类：`AnimInstance`
3. 选择骨骼

### 动画状态机

```cpp
// 在 Animation Blueprint Event Graph 中：
// - State Machine 驱动动画状态（Idle、Walk、Run、Jump）
// - Blend Space 用于方向移动

// 在 C++ 中访问：
UAnimInstance* AnimInstance = Mesh->GetAnimInstance();
AnimInstance->Montage_Play(AttackMontage);
```

---

## 播放动画蒙太奇

### Animation Montage

```cpp
// 播放蒙太奇
UAnimInstance* AnimInstance = GetMesh()->GetAnimInstance();
AnimInstance->Montage_Play(AttackMontage, 1.0f);

// 停止蒙太奇
AnimInstance->Montage_Stop(0.2f, AttackMontage);

// 检查蒙太奇是否在播放
bool bIsPlaying = AnimInstance->Montage_IsPlaying(AttackMontage);
```

### 蒙太奇通知事件

```cpp
// 在 Animation Montage 中添加通知事件（在时间轴上右键单击 > Add Notify > New Notify）
// 在 C++ 中实现：

UCLASS()
class UMyAnimInstance : public UAnimInstance {
    GENERATED_BODY()

public:
    UFUNCTION()
    void AnimNotify_AttackHit() {
        // 当通知到达时调用
        DealDamage();
    }
};
```

---

## Blend Spaces

### 1D Blend Space（速度混合）

```cpp
// 创建：Content Browser > Animation > Blend Space 1D
// 水平轴：速度（0 = 空闲、1 = 行走、2 = 奔跑）
// 在关键点添加动画

// 在 Anim Blueprint 中使用：
// - 从角色获取速度
// - 输入到 Blend Space
```

### 2D Blend Space（方向移动）

```cpp
// 创建：Content Browser > Animation > Blend Space
// 水平轴：方向 X（-1 到 1）
// 垂直轴：方向 Y（-1 到 1）
// 放置动画（前进、后退、左、右、斜向）
```

---

## Control Rig（过程动画）

### 创建 Control Rig

1. Content Browser > Animation > Control Rig
2. 选择骨骼
3. 构建 rig 层级结构（骨骼、控制、IK）

### 在 Animation Blueprint 中使用 Control Rig

```cpp
// 在 Anim Blueprint 中添加 "Control Rig" 节点
// 分配 Control Rig 资源
// 在运行时程序化修改骨骼
```

### Control Rig 在 C++ 中

```cpp
// 获取 control rig 组件
UControlRig* ControlRig = /* 从动画实例获取 */;

// 设置控制值
ControlRig->SetControlValue<FVector>(TEXT("IK_Hand_R"), TargetLocation);
```

---

## IK Rig & Retargeting（UE5）

### 创建 IK Rig

1. Content Browser > Animation > IK Rig
2. 选择骨骼
3. 添加 IK 目标（手、脚）
4. 设置求解器链

### 重定向动画

1. 为源骨骼创建 IK Rig
2. 为目标骨骼创建 IK Rig
3. 创建 IK Retargeter 资源
4. 分配源和目标 IK Rigs
5. 批量重定向动画

### 在 C++ 中重定向

```cpp
// 重定向主要是基于编辑器的
// 动画重定向一次，然后正常使用
```

---

## Animation Notify States

### 自定义通知状态（基于持续事件的事件）

```cpp
UCLASS()
class UAnimNotifyState_Invulnerable : public UAnimNotifyState {
    GENERATED_BODY()

public:
    virtual void NotifyBegin(USkeletalMeshComponent* MeshComp, UAnimSequenceBase* Animation, float TotalDuration, const FAnimNotifyEventReference& EventReference) override {
        // 开始无敌
        AMyCharacter* Character = Cast<AMyCharacter>(MeshComp->GetOwner());
        Character->bIsInvulnerable = true;
    }

    virtual void NotifyEnd(USkeletalMeshComponent* MeshComp, UAnimSequenceBase* Animation, const FAnimNotifyEventReference& EventReference) override {
        // 结束无敌
        AMyCharacter* Character = Cast<AMyCharacter>(MeshComp->GetOwner());
        Character->bIsInvulnerable = false;
    }
};
```

---

## 骨骼网格与插槽

### 将对象附加到插槽

```cpp
// 在 Skeletal Mesh Editor 中创建插槽（Skeleton Tree > Add Socket）

// 将组件附加到插槽
UStaticMeshComponent* Weapon = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Weapon"));
Weapon->SetupAttachment(GetMesh(), TEXT("hand_r_socket"));
```

---

## 动画曲线

### 使用动画曲线

```cpp
// 为动画添加曲线：
// Animation Editor > Curves > Add Curve

// 在 Anim Blueprint 或 C++ 中读取曲线值：
UAnimInstance* AnimInstance = GetMesh()->GetAnimInstance();
float CurveValue = AnimInstance->GetCurveValue(TEXT("MyCurve"));
```

---

## 根运动

### 启用根运动

```cpp
// 在 Animation Sequence 中：Asset Details > Root Motion > Enable Root Motion

// 在 Character 类中：
GetCharacterMovement()->bAllowPhysicsRotationDuringAnimRootMotion = true;
```

---

## 动画层（链接动画图）

### 使用链接动画层

```cpp
// 为层创建单独的 Anim Blueprint（例如上半身、下半身）
// 在主 Anim Blueprint 中链接：添加 "Linked Anim Graph" 节点

// 动态切换层：
UAnimInstance* AnimInstance = GetMesh()->GetAnimInstance();
AnimInstance->LinkAnimClassLayers(NewLayerClass);
```

---

## Sequencer（过场动画）

### 创建序列

1. Content Browser > Cinematics > Level Sequence
2. 添加轨道：Camera、Character、Animation 等

### 从 C++ 播放序列

```cpp
#include "LevelSequenceActor.h"
#include "LevelSequencePlayer.h"

ALevelSequenceActor* SequenceActor = /* 在关卡中生成或查找 */;
SequenceActor->GetSequencePlayer()->Play();
```

---

## 性能提示

### 动画优化

```cpp
// LOD（细节级别）用于骨骼网格
// 减少远处角色的骨骼数量

// Anim Blueprint 优化：
// - 使用 "Anim Node Relevancy"（不可见时跳过更新）
// - 屏幕外时禁用更新：
GetMesh()->VisibilityBasedAnimTickOption = EVisibilityBasedAnimTickOption::OnlyTickPoseWhenRendered;
```

---

## 调试

### 动画调试可视化

```cpp
// 控制台命令：
// showdebug animation - 显示动画状态信息
// a.VisualizeSkeletalMeshBones 1 - 显示骨骼

// 绘制调试骨骼：
DrawDebugCoordinateSystem(GetWorld(), BoneLocation, BoneRotation, 50.0f, false, -1.0f, 0, 2.0f);
```

---

## 来源
- https://docs.unrealengine.com/5.7/en-US/animation-in-unreal-engine/
- https://docs.unrealengine.com/5.7/en-US/control-rig-in-unreal-engine/
- https://docs.unrealengine.com/5.7/en-US/ik-rig-in-unreal-engine/
