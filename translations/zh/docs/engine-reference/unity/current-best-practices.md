# Unity 6.3 LTS — 当前最佳实践

**最后验证：** 2026-02-13

可能不在 LLM 训练数据中的现代 Unity 6 模式。这些是截至 Unity 6.3 LTS 的生产就绪推荐。

---

## 项目设置

### 生产使用 Unity 6.3 LTS
- **Tech Stream**（6.4+）：最新功能，较不稳定
- **LTS**（6.3）：生产就绪，2 年支持（至 2027 年 12 月）

### 选择合适的渲染管线
- **URP（通用）**：移动、跨平台、性能好 ✅ 推荐用于大多数游戏
- **HDRP（高清）**：高端 PC/主机、照片级真实感
- **内置**：已弃用，新项目避免使用

---

## 脚本

### 使用 C# 9+ 特性（Unity 6 支持 C# 9）

```csharp
// ✅ 记录类型用于数据
public record PlayerData(string Name, int Level, float Health);

// ✅ Init-only 属性
public class Config {
    public string GameMode { get; init; }
}

// ✅ 模式匹配
var result = enemy switch {
    Boss boss => boss.Enrage(),
    Minion minion => minion.Flee(),
    _ => null
};
```

### 异步/等待用于资源加载

```csharp
// ✅ 现代异步模式
public async Task<GameObject> LoadEnemyAsync(string key) {
    var handle = Addressables.LoadAssetAsync<GameObject>(key);
    return await handle.Task;
}
```

### 使用 Source Generators 进行序列化（Unity 6+）

```csharp
// ✅ 源码生成序列化（更快，更少反射）
[GenerateSerializer]
public partial struct PlayerStats : IComponentData {
    public int Health;
    public int Mana;
}
```

---

## DOTS/ECS（Unity 6.3 LTS 中生产就绪）

### 使用 ISystem（不是 ComponentSystem）

```csharp
// ✅ 现代非托管 ISystem（Burst 兼容）
public partial struct MovementSystem : ISystem {
    public void OnCreate(ref SystemState state) { }

    public void OnUpdate(ref SystemState state) {
        foreach (var (transform, speed) in
            SystemAPI.Query<RefRW<LocalTransform>, RefRO<MoveSpeed>>()) {
            transform.ValueRW.Position += speed.ValueRO.Value * SystemAPI.Time.DeltaTime;
        }
    }
}
```

### 使用 IJobEntity 进行并行任务

```csharp
// ✅ IJobEntity（替代 IJobForEach）
[BurstCompile]
public partial struct DamageJob : IJobEntity {
    public float DeltaTime;

    void Execute(ref Health health, in DamageOverTime dot) {
        health.Value -= dot.DamagePerSecond * DeltaTime;
    }
}

// 调度它
var job = new DamageJob { DeltaTime = SystemAPI.Time.DeltaTime };
job.ScheduleParallel();
```

---

## 输入

### 使用 Input System 包（不是传统输入）

```csharp
// ✅ Input Actions（可重新绑定、跨平台）
using UnityEngine.InputSystem;

public class PlayerInput : MonoBehaviour {
    private PlayerControls controls;

    void Awake() {
        controls = new PlayerControls();
        controls.Gameplay.Jump.performed += ctx => Jump();
    }

    void OnEnable() => controls.Enable();
    void OnDisable() => controls.Disable();
}
```

在编辑器中创建 Input Actions 资源，通过检查器生成 C# 类。

---

## UI

### 对运行时 UI 使用 UI Toolkit（Unity 6 中生产就绪）

```csharp
// ✅ UI Toolkit（新项目替代 UGUI）
using UnityEngine.UIElements;

public class MainMenu : MonoBehaviour {
    void OnEnable() {
        var root = GetComponent<UIDocument>().rootVisualElement;

        var playButton = root.Q<Button>("play-button");
        playButton.clicked += StartGame;

        var scoreLabel = root.Q<Label>("score");
        scoreLabel.text = $"高分: {PlayerPrefs.GetInt("HighScore")}";
    }
}
```

**UXML**（UI 结构）+ **USS**（样式）= HTML/CSS 式工作流。

---

## 资产管理

### 使用 Addressables（不是 Resources）

```csharp
// ✅ Addressables（异步、内存高效）
using UnityEngine.AddressableAssets;

public async Task SpawnEnemyAsync(string enemyKey) {
    var handle = Addressables.InstantiateAsync(enemyKey);
    var enemy = await handle.Task;

    // 清理：销毁时释放
    Addressables.ReleaseInstance(enemy);
}
```

**优点：** 异步加载、远程内容交付、更好的内存控制。

---

## 渲染

### 对自定义通道使用 RenderGraph API（URP/HDRP）

```csharp
// ✅ RenderGraph API（Unity 6+）
public override void RecordRenderGraph(RenderGraph renderGraph, ContextContainer frameData) {
    using (var builder = renderGraph.AddRasterRenderPass<PassData>("My Pass", out var passData)) {
        // 设置通道
        builder.SetRenderFunc((PassData data, RasterGraphContext context) => {
            // 执行命令
        });
    }
}
```

**替代：** 旧的 `CommandBuffer.Execute()` 模式。

---

## 性能

### 使用 Burst Compiler + Jobs System

```csharp
// ✅ Burst 编译的任务（大幅性能提升）
[BurstCompile]
struct ParticleUpdateJob : IJobParallelFor {
    public NativeArray<float3> Positions;
    public NativeArray<float3> Velocities;
    public float DeltaTime;

    public void Execute(int index) {
        Positions[index] += Velocities[index] * DeltaTime;
    }
}

// 调度
var job = new ParticleUpdateJob {
    Positions = positions,
    Velocities = velocities,
    DeltaTime = Time.deltaTime
};
job.Schedule(positions.Length, 64).Complete();
```

比等效 C# 代码**快 20-100 倍**。

---

### 对重复对象使用 GPU Instancing

```csharp
// ✅ GPU Instancing（数千对象，最少绘制调用）
Graphics.RenderMeshInstanced(
    new RenderParams(material),
    mesh,
    0,
    matrices // NativeArray<Matrix4x4>
);
```

---

## 内存管理

### 使用 NativeContainers（任务中不是托管数组）

```csharp
// ✅ NativeArray（无 GC，Burst 兼容）
NativeArray<int> data = new NativeArray<int>(1000, Allocator.TempJob);
// ... 在任务中使用
data.Dispose(); // 需要手动清理

// ✅ 或使用 using 语句
using var data = new NativeArray<int>(1000, Allocator.TempJob);
// 自动释放
```

---

## 多人游戏

### 使用 Netcode for GameObjects（官方）

```csharp
// ✅ Unity 官方网络代码
using Unity.Netcode;

public class Player : NetworkBehaviour {
    private NetworkVariable<int> health = new NetworkVariable<int>(100);

    [ServerRpc]
    public void TakeDamageServerRpc(int damage) {
        health.Value -= damage;
    }
}
```

**替代：** UNet（已弃用）、MLAPI（已重命名为 Netcode for GameObjects）。

---

## 测试

### 使用 Unity Test Framework（基于 NUnit）

```csharp
// ✅ Play Mode 测试
[UnityTest]
public IEnumerator Player_TakesDamage_HealthDecreases() {
    var player = new GameObject().AddComponent<Player>();
    player.Health = 100;

    player.TakeDamage(25);
    yield return null; // 等待一帧

    Assert.AreEqual(75, player.Health);
}
```

---

## 调试

### 使用日志记录最佳实践

```csharp
// ✅ 结构化日志（Unity 6+）
using UnityEngine;

Debug.Log($"玩家 {playerName} 获得 {score} 分");

// ✅ 条件编译用于调试代码
#if UNITY_EDITOR || DEVELOPMENT_BUILD
    Debug.DrawRay(transform.position, direction, Color.red);
#endif
```

---

## 总结：Unity 6 技术栈

| 特性 | 使用这个（2026） | 避免这个（传统） |
|---------|------------------|----------------------|
| **输入** | Input System 包 | `Input` 类 |
| **UI** | UI Toolkit | UGUI（Canvas） |
| **ECS** | ISystem + IJobEntity | ComponentSystem |
| **渲染** | URP + RenderGraph | 内置管线 |
| **资源** | Addressables | Resources |
| **任务** | Burst + IJobParallelFor | 繁重工作的协程 |
| **多人游戏** | Netcode for GameObjects | UNet |

---

**来源：**
- https://docs.unity3d.com/6000.0/Documentation/Manual/BestPracticeGuides.html
- https://docs.unity3d.com/Packages/com.unity.entities@1.3/manual/index.html
- https://docs.unity3d.com/Packages/com.unity.inputsystem@1.11/manual/index.html
