# Agent 测试 Spec: lead-programmer

## Agent 摘要
**所属领域：** 代码架构决策、LP-FEASIBILITY gate、LP-CODE-REVIEW gate、编码标准执行、已批准引擎内的技术栈决策。
**不拥有：** 游戏设计决策（game-designer）、创意方向（creative-director）、生产排期（producer）、视觉艺术方向（art-director）。
**模型层级：** Sonnet（单个系统的实现级分析）。
**处理的 Gate IDs：** LP-FEASIBILITY, LP-CODE-REVIEW。

---

## 静态断言（结构性）

通过读取 agent 的 `.claude/agents/lead-programmer.md` frontmatter 验证：

- [ ] `description:` 字段存在且领域特定（引用代码架构、可行性、代码审查、编码标准——非通用）
- [ ] `allowed-tools:` 列表包含用于源文件的 Read；Bash 可包含用于静态分析或测试运行；未经明确委派，不对 `src/` 之外具有写权限
- [ ] 根据 coordination-rules.md，模型层级为 `claude-sonnet-4-6`
- [ ] Agent 定义不声称对游戏设计、创意方向或生产排期拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出格式
**场景：** 一个新的 `CombatSystem` 实现提交进行代码审查。该系统对所有外部引用使用依赖注入，所有公共 API 有文档注释，遵循项目命名约定，并为所有公共方法包含单元测试。请求标记为 LP-CODE-REVIEW。
**预期：** 返回 `LP-CODE-REVIEW: APPROVED`，附有理据确认依赖注入使用、文档注释覆盖、命名约定合规性和测试覆盖。
**断言：**
- [ ] 裁决必须是 APPROVED / NEEDS CHANGES 之一
- [ ] 裁决令牌格式化为 `LP-CODE-REVIEW: APPROVED`
- [ ] 理据引用特定的编码标准标准（DI、文档注释、命名、测试）
- [ ] 输出保持在代码质量范围内——不评论机制是否有趣或是否符合创意愿景

### 用例 2：领域外请求——重定向或升级
**场景：** 团队成员要求 lead-programmer 审查并批准玩家伤害随等级提升的平衡公式，检查数字是否"感觉对"。
**预期：** Agent 拒绝评估设计平衡并重定向到 systems-designer。
**断言：**
- [ ] 不对公式平衡或游戏感受做出任何有约束力的评估
- [ ] 明确命名 `systems-designer` 为正确的处理者
- [ ] 可以指出关于公式的代码实现关切（例如最高等级时的整数溢出风险），但将所有平衡评估委托给 systems-designer

### 用例 3：Gate 裁决——正确词汇
**场景：** 针对敌方 AI 的提议寻路方法使用暴力最近邻搜索每帧对所有其他实体进行搜索。在预期 200+ 敌方数量下，这在 60fps 下是每帧 O(n²)。请求标记为 LP-FEASIBILITY。
**预期：** 返回 `LP-FEASIBILITY: INFEASIBLE`，附有 O(n²) 复杂度、实体数量阈值以及由此产生的每帧成本与目标帧预算相比的具体引用。
**断言：**
- [ ] 裁决必须是 FEASIBLE / CONCERNS / INFEASIBLE 之一——而非自由格式文本
- [ ] 裁决令牌格式化为 `LP-FEASIBILITY: INFEASIBLE`
- [ ] 理据包括具体的算法复杂度和实体数量数字
- [ ] 建议至少一种替代方法（例如空间哈希、KD-tree），但不强制选择

### 用例 4：冲突升级——正确父级
**场景：** game-designer 想要一个机制，其中每个 NPC 维持需求、日程和记忆的完整模拟（类似于完整的生命模拟 AI）。lead-programmer 计算这将在目标 NPC 数量下超出帧预算 3 倍。game-designer 坚持该机制是游戏愿景的核心。
**预期：** lead-programmer 用数字陈述具体的帧预算违规，提出替代方法（例如基于 LOD 的模拟、简化需求模型），但明确将"这是否值得成本，还是设计应该改变"的决策委托给 creative-director 作为创意仲裁者。
**断言：**
- [ ] 陈述具体的帧预算违规（例如 N 个实体时超出预算 3 倍）
- [ ] 提出至少一个技术上可行的替代方案
- [ ] 明确将设计优先级决策委托给 `creative-director`
- [ ] 不单方面削减或修改机制设计

### 用例 5：上下文传递——使用提供的上下文
**场景：** Agent 收到一个 gate 上下文块，其中包含项目的帧预算：每帧总 16.67ms，其中 4ms 分配给 AI 系统。一个根据性能分析估计在正常条件下每帧消耗 7ms 的新 AI 行为系统提交了。
**预期：** 评估引用上下文中的特定帧预算分配（4ms AI 预算），识别 7ms 估计超出分配 3ms，并以引用的这些特定数字返回 CONCERNS 或 INFEASIBLE。
**断言：**
- [ ] 引用提供上下文中的特定帧预算数字（总 16.67ms, 4ms AI 分配）
- [ ] 在比较中使用提交中的特定 7ms 估计
- [ ] 不给出通用的"这可能很慢"的建议——引用具体数字
- [ ] 裁决理据可追溯到提供的预算约束

---

## 协议合规性

- [ ] 仅使用 APPROVED / NEEDS CHANGES 词汇返回 LP-CODE-REVIEW 裁决
- [ ] 仅使用 FEASIBLE / CONCERNS / INFEASIBLE 词汇返回 LP-FEASIBILITY 裁决
- [ ] 保持在声明的代码架构领域内
- [ ] 将设计优先级冲突委托给 creative-director
- [ ] 在输出中使用 gate ID（例如 `LP-FEASIBILITY: INFEASIBLE`），而非内联散文式裁决
- [ ] 不做有约束力的游戏设计或创意方向决定

---

## 覆盖说明
- 跨越多个相互依赖系统的多文件代码审查未覆盖——延迟到集成测试。
- 技术债务评估和优先级排序未在此覆盖——延迟到 /tech-debt skill 集成。
- 编码标准文档更新（添加新的禁止模式）未覆盖。
- 与 qa-lead 关于什么是可测试单元的交互（LP vs QL 边界）未覆盖。
