# Agent 测试 Spec: ue-gas-specialist

## Agent 摘要
- **领域**：Gameplay Ability System (GAS) — 技能（UGameplayAbility）、游戏效果（UGameplayEffect）、属性集（UAttributeSet）、游戏标签、技能任务（UAbilityTask）、技能 spec（FGameplayAbilitySpec）、GAS 预测和延迟补偿
- **不拥有**：技能状态的 UI 显示（ue-umg-specialist）、GAS 数据在 GAS 内置预测之外的网络复制（ue-replication-specialist）、技能反馈的美术或 VFX（vfx-artist）
- **模型层级**：Sonnet
- **Gate IDs**：无；跨领域调用委托给相应的 specialist

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 GAS、技能、GameplayEffects、AttributeSets）
- [ ] `allowed-tools:` 列表匹配 agent 的角色（GAS 源文件的 Read/Write；无部署或服务器工具）
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义不声称对 UI 实现或底层网络序列化拥有权威

---

## 测试用例

### 用例 1：领域内请求——带冷却的冲刺技能
**输入**："实现一个将玩家向前移动 500 单位并具有 1.5 秒冷却的冲刺技能。"
**预期行为**：
- 生成 GAS AbilitySpec 结构或大纲：继承 UGameplayAbility 的子类，包含 ActivateAbility 逻辑、用于移动的 AbilityTask（例如 AbilityTask_ApplyRootMotionMoveToForce 或自定义根运动），以及用于冷却的 UGameplayEffect
- 冷却 GameplayEffect 使用 Duration 策略，持续时间 1.5 秒，并使用 GameplayTag 阻止重新激活
- 标签按照层级约定清晰命名（例如 Ability.Dash, Cooldown.Ability.Dash）
- 输出包括技能类大纲和 GameplayEffect 定义

### 用例 2：领域外请求——GAS 状态复制
**输入**："如何将玩家的技能冷却状态复制到所有客户端以便 UI 正确更新？"
**预期行为**：
- 澄清 GAS 通过 AbilitySystemComponent 的复制模式对 AbilitySpecs 和 GameplayEffects 具有内置复制
- 解释三种 ASC 复制模式（Full, Mixed, Minimal）以及何时使用每种模式
- 对于超出 GAS 内置功能的自定义复制需求，明确说明："对于 GAS 数据的自定义网络序列化，请与 ue-replication-specialist 协调"
- 在未标记领域边界的情况下，不尝试编写 GAS 自身系统之外的自定义复制代码

### 用例 3：领域边界——错误的 GameplayTag 层级
**输入**："我们有一个技能应用一个名为 'Stunned' 的标签，另一个技能检查 'Status.Stunned'。它们不匹配。"
**预期行为**：
- 识别根本原因：标签名称必须精确匹配或通过 TagContainer 查询使用层级匹配
- 标记命名不一致：'Stunned' 是根级标签；'Status.Stunned' 是 'Status' 下的子标签——它们是不同的标签
- 推荐项目标签命名约定：所有状态效果放在 Status.* 下，所有技能放在 Ability.* 下
- 提供修复方案：要么将应用的标签重命名为 'Status.Stunned'，要么更新查询以匹配 'Stunned'
- 指出标签定义应存放在何处（DefaultGameplayTags.ini 或 DataTable）

### 用例 4：冲突——两个技能之间的属性集冲突
**输入**："我们的护盾技能和护甲技能都修改 'DefenseValue' 属性。它们以非预期的方式叠加——两者都激活后，防御远超最大值。"
**预期行为**：
- 识别为 GameplayEffect 叠加和幅度计算问题
- 提出使用 Execution Calculations（UGameplayEffectExecutionCalculation）或 Modifier Aggregators 限制组合结果的解决方案
- 或者建议使用 Gameplay Effect Stacking 策略（Aggregate, None）防止非预期的加法叠加
- 生成具体的解决方案：Execution Calculation 类大纲或将 Modifier Op 改为 Override 替代 Additive 以实现上限
- 不提议移除其中一个技能作为解决方案

### 用例 5：上下文传递——针对现有属性集进行设计
**输入上下文**：项目有一个现有 AttributeSet，属性包括：Health, MaxHealth, Stamina, MaxStamina, Defense, AttackPower。
**输入**："设计一个狂战士技能，当 Health 降至 30% 以下时将 AttackPower 提高 50%。"
**预期行为**：
- 使用现有的 Health、MaxHealth 和 AttackPower 属性——不发明新属性
- 设计一个在 Health 变化时触发的被动 GameplayAbility（或触发型 Effect），通过 GameplayEffectExecutionCalculation 或基于属性的幅度检查 Health/MaxHealth 比率
- 使用 Gameplay Cue 或 GameplayTag 跟踪狂战士激活状态
- 引用提供 AttributeSet 中的实际属性名称（AttackPower，而非 "Damage" 或 "Strength"）

---

## 协议合规性

- [ ] 保持在声明领域内（GAS：技能、效果、属性、标签、技能任务）
- [ ] 将自定义复制请求重定向到 ue-replication-specialist，清晰解释边界
- [ ] 返回结构化发现（技能大纲 + GameplayEffect 定义），而非模糊描述
- [ ] 主动执行标签层级命名约定
- [ ] 仅使用提供上下文中存在的属性和标签；不发明新属性而不加说明

---

## 覆盖说明
- 用例 3（标签层级）是细微 bug 的常见来源；每当标签命名约定变化时进行测试
- 用例 4 需要了解 GAS 叠加策略——如果 GAS 集成深度变化则验证此用例
- 用例 5 是最重要的上下文感知测试；失败意味着 agent 忽略项目状态
- 无自动运行器；通过 `/skill-test` 手动审查
