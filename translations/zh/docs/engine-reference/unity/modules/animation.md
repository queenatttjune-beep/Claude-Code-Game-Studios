# Unity 6.3 — 动画模块参考

**最后验证：** 2026-02-13
**知识空白：** Unity 6 动画改进、Timeline 增强

---

## 概述

Unity 6.3 动画系统：
- **Animator Controller（Mecanim）**：基于状态机（推荐）
- **Timeline**：过场动画序列、过场动画
- **Animation Rigging**：过程运行时动画
- **传统动画**：已弃用，避免使用

---

## 从 2022 LTS 的关键变更

### Animation Rigging 包（Unity 6 中生产就绪）

```csharp
// 安装：Package Manager > Animation Rigging
// 运行时 IK、瞄准约束、过程动画
```

### Timeline 改进
- 更好的性能
- 更多轨道类型
- 改进的信号系统

---

## Animator Controller（Mecanim）

### 基本设置

```csharp
// 创建：Assets > Create > Animator Controller
// 添加到 GameObject：Add Component > Animator
// 分配 Controller：Animator > Controller = YourAnimatorController
```

### 状态转换

```csharp
Animator animator = GetComponent<Animator>();

// ✅ 触发转换
animator.SetTrigger("Jump");

// ✅ 布尔参数
animator.SetBool("IsRunning", true);

// ✅ 浮点参数（混合树）
animator.SetFloat("Speed", currentSpeed);

// ✅ 整数参数
animator.SetInteger("WeaponType", 2);
```

### 动画层
- **基础层**：默认动画（移动）
- **覆盖层**：替换基础层（如武器切换）
- **叠加层**：在基础层之上叠加（如呼吸、瞄准偏移）

```csharp
// 设置层权重（0-1）
animator.SetLayerWeight(1, 0.5f); // 50% 混合
```

---

## 混合树

### 1D 混合树（速度混合）

```csharp
// 空闲（速度 = 0）→ 行走（速度 = 0.5）→ 奔跑（速度 = 1.0）
animator.SetFloat("Speed", moveSpeed);
```

### 2D 混合树（方向移动）

```csharp
// X 轴：横向移动（-1 到 1）
// Y 轴：前进/后退（-1 到 1）
animator.SetFloat("MoveX", input.x);
animator.SetFloat("MoveY", input.y);
```

---

## 动画事件

### 从动画剪辑触发事件

```csharp
// 在动画窗口中添加：右键单击时间轴 > 添加动画事件
// GameObject 上必须有匹配的方法：

public void OnFootstep() {
    // 播放脚步声
    AudioSource.PlayClipAtPoint(footstepClip, transform.position);
}

public void OnAttackHit() {
    // 造成伤害
    DealDamageInFrontOfPlayer();
}
```

---

## 根运动

### 通过动画驱动角色移动

```csharp
Animator animator = GetComponent<Animator>();
animator.applyRootMotion = true; // 基于动画移动角色

void OnAnimatorMove() {
    // 自定义根运动处理
    transform.position += animator.deltaPosition;
    transform.rotation *= animator.deltaRotation;
}
```

---

## Animation Rigging（Unity 6+）

### IK（逆向运动学）

```csharp
// 安装：Package Manager > Animation Rigging
// 添加：Rig Builder 组件 + Rig GameObject

// 双骨骼 IK（手臂/腿）
// - 添加 Two Bone IK Constraint
// - 分配 Tip（手/脚）、Mid（肘/膝）、Root（肩/髋）
// - 设置 Target（手/脚应到达的位置）

// 运行时控制：
TwoBoneIKConstraint ikConstraint = rig.GetComponentInChildren<TwoBoneIKConstraint>();
ikConstraint.data.target = targetTransform;
ikConstraint.weight = 1f; // 0-1 混合
```

### 瞄准约束（注视）

```csharp
// 角色注视目标
MultiAimConstraint aimConstraint = rig.GetComponentInChildren<MultiAimConstraint>();
aimConstraint.data.sourceObjects[0] = new WeightedTransform(targetTransform, 1f);
```

---

## Timeline（过场动画）

### 基本 Timeline 设置

```csharp
// 创建：Assets > Create > Timeline
// 添加到 GameObject：Add Component > Playable Director
// 分配 Timeline：Playable Director > Playable = YourTimeline

// 从脚本播放：
PlayableDirector director = GetComponent<PlayableDirector>();
director.Play();
```

### Timeline 轨道
- **激活轨道**：启用/禁用 GameObject
- **动画轨道**：在 Animator 上播放动画
- **音频轨道**：同步音频播放
- **Cinemachine 轨道**：摄像机移动
- **信号轨道**：在特定时间触发事件

### 信号系统（事件）

```csharp
// 创建信号资源：Assets > Create > Signals > Signal
// 将 Signal Emitter 添加到 Timeline 轨道
// 将 Signal Receiver 组件添加到 GameObject

public class CutsceneEvents : MonoBehaviour {
    public void OnDialogueStart() {
        // 由信号触发
    }
}
```

---

## 动画播放控制

### 直接播放动画（无状态机）

```csharp
// ✅ CrossFade（平滑过渡）
animator.CrossFade("Attack", 0.2f); // 0.2 秒过渡

// ✅ Play（立即）
animator.Play("Idle");

// ❌ 避免：传统 Animation 组件
Animation anim = GetComponent<Animation>(); // 已弃用
```

---

## 动画曲线

### 自定义属性动画

```csharp
// 在动画窗口中：添加属性 > 自定义组件 > 你的脚本 > 你的浮点数

public class WeaponTrail : MonoBehaviour {
    public float trailIntensity; // 由动画剪辑驱动

    void Update() {
        // 强度由动画曲线控制
        trailRenderer.startWidth = trailIntensity;
    }
}
```

---

## 性能优化

### 裁剪
- `Animator > Culling Mode`：
  - **Always Animate**：始终更新（开销大）
  - **Cull Update Transforms**：屏幕外时停止更新骨骼（推荐）
  - **Cull Completely**：屏幕外时停止所有动画

### LOD（细节级别）
- 远处角色使用更简单的动画
- 减少 LOD 网格的骨骼数

---

## 常见模式

### 检查动画是否完成

```csharp
AnimatorStateInfo stateInfo = animator.GetCurrentAnimatorStateInfo(0);
if (stateInfo.IsName("Attack") && stateInfo.normalizedTime >= 1.0f) {
    // 攻击动画完成
}
```

### 覆盖动画速度

```csharp
animator.speed = 1.5f; // 150% 速度
```

### 获取当前动画名称

```csharp
AnimatorClipInfo[] clipInfo = animator.GetCurrentAnimatorClipInfo(0);
string currentClip = clipInfo[0].clip.name;
```

---

## 调试

### Animator 窗口
- `Window > Animation > Animator`
- 可视化状态机，查看活动状态

### Animation 窗口
- `Window > Animation > Animation`
- 编辑动画剪辑，添加事件

---

## 来源
- https://docs.unity3d.com/6000.0/Documentation/Manual/AnimationOverview.html
- https://docs.unity3d.com/Packages/com.unity.animation.rigging@1.3/manual/index.html
- https://docs.unity3d.com/Packages/com.unity.timeline@1.8/manual/index.html
