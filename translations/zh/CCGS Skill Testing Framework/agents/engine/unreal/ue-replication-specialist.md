# Agent 测试 Spec: ue-replication-specialist

## Agent 摘要
- **领域**：属性复制（UPROPERTY Replicated/ReplicatedUsing）、RPC（Server/Client/NetMulticast）、客户端预测与协调、网络相关性和始终相关设置、网络序列化（FArchive/NetSerialize）、带宽优化和复制频率调优
- **不拥有**：被复制的游戏逻辑（gameplay-programmer）、服务器基础设施和托管（devops-engineer）、GAS 特定预测（ue-gas-specialist 处理 GAS 网络预测）
- **模型层级**：Sonnet
- **Gate IDs**：无；将安全相关的复制关切升级到 lead-programmer

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用复制、RPC、客户端预测、带宽）
- [ ] `allowed-tools:` 列表匹配 agent 的角色（C++ 和 Blueprint 源文件的 Read/Write；无基础设施或部署工具）
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义不声称对服务器基础设施、游戏服务器架构或游戏逻辑正确性拥有权威

---

## 测试用例

### 用例 1：领域内请求——带客户端预测的复制玩家生命值
**输入**："设置复制玩家生命值，客户端可以本地预测（例如受到自我伤害时）并由服务器纠正。"
**预期行为**：
- 在适当的 Character 或 AttributeSet 类中生成 `UPROPERTY(ReplicatedUsing=OnRep_Health)` 声明
- 描述 OnRep_Health 函数：应用视觉/音频反馈，协调预测值与服务器权威值
- 解释客户端预测模式：本地客户端立即应用暂定伤害，服务器权威值通过 OnRep 到达并纠正任何差异
- 指出如果使用 GAS，内置 GAS 预测会处理这个问题——建议与 ue-gas-specialist 协调
- 输出是具体的代码结构（属性声明 + OnRep 大纲），而非仅概念性描述

### 用例 2：领域外请求——游戏服务器架构
**输入**："设计我们的游戏服务器基础设施——需要多少台专用服务器、区域部署和匹配架构。"
**预期行为**：
- 不生成服务器基础设施架构、托管建议或匹配设计
- 明确说明："服务器基础设施和部署架构由 devops-engineer 负责；我处理运行游戏会话中的 Unreal 复制层"
- 不混淆游戏内复制与服务器托管关切

### 用例 3：领域边界——无服务器权威验证的 RPC
**输入**："我们有一个名为 ServerSpendCurrency 的 Server RPC，用于扣除游戏内货币。客户端调用它，服务器直接扣除而不检查任何内容。"
**预期行为**：
- 标记为严重安全漏洞：未经验证的服务器 RPC 可被作弊者利用发送任意 RPC 调用
- 提供必需的修复：在扣除之前在服务器端验证——检查玩家是否确实有该货币，验证交易是否有效，否则拒绝并记录
- 使用模式：`if (!HasAuthority()) return;` 守卫加上显式状态验证再变更
- 指出鉴于经济影响，这应由 lead-programmer 审查
- 不生成"修复后"代码而不解释原始代码为什么危险

### 用例 4：带宽优化——高频移动复制
**输入**："我们的玩家移动使用 Vector3 位置每 tick 复制。32 名玩家时，我们超出了带宽预算。"
**预期行为**：
- 将 tick 速率全精度 Vector3 复制识别为带宽昂贵
- 提出量化复制：使用 FVector_NetQuantize 或 FVector_NetQuantize100 替代原始 FVector 以减少每次更新的字节数
- 建议通过 SetNetUpdateFrequency() 降低非所有者客户端的复制频率
- 指出 Unreal 内置的 Character Movement Component 已有优化的移动复制——建议使用或扩展它，而非从头开发自定义系统
- 如有可能生成具体的带宽估算对比，或解释权衡

### 用例 5：上下文传递——在网络预算内设计
**输入上下文**：项目网络预算为每玩家 64 KB/s，32 名玩家 = 总服务器出站 2 MB/s。当前移动复制已使用每玩家 40 KB/s。
**输入**："我们想添加实时库存复制，以便所有客户端能立即看到其他玩家的装备变化。"
**预期行为**：
- 承认现有 40 KB/s 移动成本，每位玩家只剩 24 KB/s 用于其他一切
- 不设计天真的全库存复制方法（会超出预算）
- 推荐仅增量或事件驱动的方法：仅复制变化的槽位而非完整库存数组
- 使用带 ReplicatedUsing 的 FGameplayItemSlot 或等效结构触发定向更新
- 明确说明所述方法的带宽估算与剩余 24 KB/s 预算的关系

---

## 协议合规性

- [ ] 保持在声明领域内（属性复制、RPC、客户端预测、带宽）
- [ ] 将服务器基础设施请求重定向到 devops-engineer，不生成基础设施设计
- [ ] 标记未经验证的服务器 RPC 为安全问题并建议 lead-programmer 审查
- [ ] 返回结构化发现（属性声明、带宽估算、优化选项），而非自由形式建议
- [ ] 在评估复制设计选择时使用项目提供的带宽预算数字

---

## 覆盖说明
- 用例 3（RPC 安全）是发布关键测试——未经验证的 RPC 是十大多人游戏利用向量
- 用例 5 是最重要的上下文感知测试；agent 必须使用实际预算数字，而非通用建议
- 用例 1 GAS 分支：如果配置了 GAS，agent 应检测并委托给 ue-gas-specialist 处理 GAS 管理的属性
- 无自动运行器；通过 `/skill-test` 手动审查
