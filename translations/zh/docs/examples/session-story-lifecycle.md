# 示例会话：完整 Story 生命周期 — /story-readiness → 实现 → /story-done

**上下文：** 独立开发者，生产中期冲刺。移动系统 GDD 和架构已完成。一个关于移动核心实现的 story 已由 `/create-stories` 生成。

**起始状态：**
- `design/gdd/movement-system.md` — 完整，所有 8 个部分
- `docs/architecture/movement-adr-001.md` — 状态：已接受
- `production/sprints/sprint-03.md` — 在积压中列出 STORY-MOV-001
- `production/stories/STORY-MOV-001.md` — 存在但尚未验证
- 尚无实现代码

**会话时长：** ~50 分钟
**回合：** 13
**结果：** Story 经验证就绪、已实现、通过 `/story-done` 验证完成

---

## 阶段 1：Story 就绪检查

### 第 1 回合：用户运行就绪检查

**用户：**
> /story-readiness production/stories/STORY-MOV-001.md

### 第 2 回合：Agent 验证 Story

**Agent（通过 /story-readiness）：**
> 读取 `production/stories/STORY-MOV-001.md`...
>
> **STORY-MOV-001：实现带地形修正的 CharacterBody2D 移动**
>
> 运行 4 项验证检查：
>
> **1. 设计完整性**
> - 引用的 GDD：`design/gdd/movement-system.md` ✅
> - GDD 章节覆盖：详细规则 ✅、公式 ✅、边界情况 ✅
> - 嵌入的 TR-ID：TR-MOV-001 到 TR-MOV-008 ✅
>
> **2. 架构完整性**
> - 引用的 ADR：`docs/architecture/movement-adr-001.md` ✅
> - ADR 状态：**已接受** ✅ *（如果是 Proposed 将被阻止）*
> - Story 中的控制清单版本：`2026-03-10` — 当前清单为 `2026-03-10` ✅
>
> **3. 范围清晰度**
> - 验收标准：9 项，全部可衡量 ✅
> - 范围外项目已列出：游泳、攀爬 ✅
> - 发现的歧义：**1**
>   - ⚠️ Story 说"翻滚方向遵循最后的输入方向"但 GDD 说"在移动方向翻滚"——当玩家停止并立即翻滚时，这些有冲突。在拾取前澄清。
>
> **4. 完成定义**
> - 需要测试：公式函数的单元测试 ✅
> - 集成信号：`on_stamina_event` 必须正确触发 ✅
>
> ---
> **裁决：需要工作**
>
> 在实现开始前需要解决一个歧义。您现在想解决它，以便我将 story 标记为就绪吗？

### 第 3 回合：用户解决歧义

**用户：**
> 翻滚使用最后的移动方向。如果玩家站着不动，翻滚使用面朝方向。

### 第 4 回合：Agent 更新 Story

**Agent：**
> 更新 story 的详细规则部分：
>
> > 翻滚方向：最后的移动方向。如果在翻滚输入时速度为零，翻滚使用当前面朝方向。
>
> 我可以将其写入 `production/stories/STORY-MOV-001.md` 并将状态设置为 `ready-for-dev` 吗？

**用户：**
> 是的

**Agent 写入更新。Story 状态：`ready-for-dev`。**

> **裁决：就绪**
> Story 已准备好实现。准备好构建时拾取它。

---

## 阶段 2：实现

### 第 5 回合：用户开始实现

**用户：**
> 让我们实现 STORY-MOV-001。我准备好开始编码了。

### 第 6 回合：Agent 读取 Story 和 ADR

**Agent（gameplay-programmer）：**
> 读取 `production/stories/STORY-MOV-001.md`、`design/gdd/movement-system.md` 和 `docs/architecture/movement-adr-001.md`...
>
> **架构（来自 ADR-001）：**
> - `MovementComponent.gd` — 附加到玩家场景，不是单例
> - 通过 `TerrainLayer.get_modifier(tile_position)` 帮助器进行地形修正查找
> - 通过信号（非直接调用 StaminaSystem）处理耐力事件
>
> **我建议的文件结构：**
> ```
> src/gameplay/movement/
>   movement_component.gd       # CharacterBody2D 逻辑、状态机
>   terrain_modifier_table.gd   # 常量表：terrain_type -> {speed_mod, drain_mod}
> tests/unit/
>   test_movement_formulas.gd   # 速度和耐力计算的单元测试
> ```
>
> 我可以继续使用此结构吗？

**用户：**
> 是的

### 第 7 回合：Agent 实现并展示关键代码

**Agent：**
> 正在实现 `movement_component.gd`。在我写入文件前供您审查的关键摘录：
>
> ```gdscript
> class_name MovementComponent
> extends Node
>
> signal stamina_event(type: String, amount: float)
>
> @export var base_walk_speed: float = 120.0
> @export var run_multiplier: float = 1.7
> @export var stamina_drain_walk: float = 2.0
> @export var stamina_drain_run: float = 6.0
> @export var stamina_cost_roll: float = 15.0
>
> var _is_carrying_heavy: bool = false
>
> func _physics_process(delta: float) -> void:
>     var terrain_mod := _get_terrain_modifier()
>     var effective_speed := _calculate_speed(terrain_mod)
>     # ...
> ```
>
> 所有值都是 `@export` — 没有硬编码。这是您想要的方向吗？

**用户：**
> 看起来不错。写入。

### 第 8-9 回合：Agent 写入文件和测试

**Agent 写入：**
- `src/gameplay/movement/movement_component.gd`
- `src/gameplay/movement/terrain_modifier_table.gd`
- `tests/unit/test_movement_formulas.gd`

**Agent：**
> 文件已写入。运行可用的 lint 检查...
>
> 无错误。测试文件已创建，包含 6 个单元测试，涵盖：
> - 行走速度公式（3 种地形类型）
> - 奔跑速度公式
> - 耐力消耗计算
> - 翻滚耐力消耗（带和不带零速度边界情况）
>
> 需要手动测试的内容：无敌帧、坡度阻挡、地形集成（需要运行场景）。

---

## 阶段 3：Story 完成

### 第 10 回合：用户手动测试并运行 /story-done

**用户：**
> 我在场景中测试了——一切正常。/story-done production/stories/STORY-MOV-001.md

### 第 11 回合：Agent 运行完成审查

**Agent（通过 /story-done）：**
> 读取 story、GDD、ADR...
>
> **阶段 1：定位 Story** ✅ `production/stories/STORY-MOV-001.md`
>
> **阶段 2：验收标准验证**
>
> | # | 标准 | 验证 | 状态 |
> |---|-----------|--------------|--------|
> | 1 | 行走速度在正常地面上正确 | 自动：公式单元测试通过 | ✅ 自动 |
> | 2 | 奔跑速度正确 | 自动：公式单元测试通过 | ✅ 自动 |
> | 3 | 耐力为 0 时奔跑取消 | 手动：用户确认 | ✅ 手动 |
> | 4 | 翻滚无敌帧工作 | 手动：用户确认 | ✅ 手动 |
> | 5 | 地形修正正确应用 | 手动：用户确认 | ✅ 手动 |
> | 6 | 重物携带禁用奔跑 | 自动：尚不可测试（库存未集成） | ⏳ 已延迟 |
> | 7 | 耐力信号正确触发 | 自动：集成测试待定 | ⏳ 已延迟 |
> | 8 | 无硬编码值 | 自动：搜索 magic numbers → 发现 0 | ✅ 自动 |
> | 9 | 坡度 >45° 阻挡移动 | 手动：用户确认 | ✅ 手动 |
>
> **阶段 3：GDD/ADR 偏差**
>
> 比对 TR-ID 扫描实现...
>
> - TR-MOV-001 到 TR-MOV-007：✅ 按指定实现
> - TR-MOV-008（重物携带信号）：⚠️ 建议性——延迟到库存集成故事。不是偏差，只是现在不可验证。
>
> **阶段 4：清单过时检查**
>
> Story 控制清单版本 `2026-03-10` 匹配当前清单 `2026-03-10` ✅
>
> **阶段 5：代码审查提示**
>
> 建议在标记完成前运行 `/code-review src/gameplay/movement/`。不阻止。
>
> ---
> **完成报告：已完成带备注**
>
> Story 已完成。两个标准延迟到库存集成——记录为延迟，不是阻止。
>
> 我可以将 story 状态更新为 `Status: Complete` 并添加完成备注，并更新 `production/sprint-status.yaml` 吗？

**用户：**
> 是的

### 第 12 回合：Agent 关闭 Story

**Agent 写入：**
- Story `Status: Complete` + 完成备注（记录了延迟标准）
- `production/sprint-status.yaml` — STORY-MOV-001 设置为 `done`

**Agent：**
> STORY-MOV-001 已关闭。
>
> **已记录的延迟标准**（将在库存集成故事中自动浮现）：
> - 重物携带禁用奔跑（需要库存信号）
> - 耐力信号集成测试（需要耐力系统）
>
> **冲刺中下一个就绪的 story：**
> - STORY-MOV-002：耐力系统——这是逻辑上的下一个故事（下游依赖现在已解除阻塞）
> - STORY-HUD-001：移动 HUD 指示器——也已解除阻塞
>
> 运行 `/story-readiness` 用于您接下来拾取的任何故事。

---

## 此示例展示了什么

- **就绪关卡捕获了真正的问题**：翻滚方向歧义如果没有 `/story-readiness` 会导致后期实现决策
- **ADR 状态是硬关卡**：如果 `movement-adr-001.md` 是 `Proposed` 而不是 `Accepted`，story 将被 `BLOCKED`，实现不会开始
- **延迟标准**：并非每个验收标准都能在 story 关闭时验证——`/story-done` 跟踪延迟项而非阻止完成
- **TR-ID 引用**：story 存储 `TR-MOV-001` ID，而非引用的 GDD 文本——避免当 GDD 措辞稍后被清理时的错误偏差标志
- **sprint-status.yaml**：冲刺状态的单一事实来源，由 `/story-done` 更新，由 `/sprint-status` 读取
- **清单版本检查**：确保 story 的实现指导没有偏离当前的架构决策
