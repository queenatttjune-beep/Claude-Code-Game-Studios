# Agent 测试 Spec: producer

## Agent 摘要
**所属领域：** 范围管理、sprint 规划验证、里程碑跟踪、epic 优先级排序、生产阶段关卡。
**不拥有：** 游戏设计决策（creative-director / game-designer）、技术架构（technical-director）、创意方向。
**模型层级：** Opus（多文档综合、高风险的阶段关卡裁决）。
**处理的 Gate IDs：** PR-SCOPE, PR-SPRINT, PR-MILESTONE, PR-EPIC, PR-PHASE-GATE。

---

## 静态断言（结构性）

通过读取 agent 的 `.claude/agents/producer.md` frontmatter 验证：

- [ ] `description:` 字段存在且领域特定（引用范围、sprint、里程碑、生产——非通用）
- [ ] `allowed-tools:` 列表以读取为主；仅当 sprint/milestone 文件需要解析时才使用 Bash
- [ ] 根据 coordination-rules.md，模型层级为 `claude-opus-4-6`（具有 gate 综合能力的 director = Opus）
- [ ] Agent 定义不声称对设计决策或技术架构拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出格式
**场景：** 一个 Sprint 7 的 sprint 计划已提交。该计划包含 12 个 story points，分布在 4 个团队成员中，为期 2 周。过去 3 个 sprint 的历史速度平均为 11.5 个 points。请求标记为 PR-SPRINT。
**预期：** 返回 `PR-SPRINT: REALISTIC`，附有理据说明该计划在历史速度的一个标准差范围内，且容量似乎匹配。
**断言：**
- [ ] 裁决必须是 REALISTIC / CONCERNS / UNREALISTIC 之一
- [ ] 裁决令牌格式化为 `PR-SPRINT: REALISTIC`
- [ ] 理据引用具体的 story point 数和历史速度数据
- [ ] 输出保持在生产范围内——不评论 story 设计是否良好或技术上是否可行

### 用例 2：领域外请求——重定向或升级
**场景：** 团队成员要求 producer 评估游戏的"基于重量的背包"机制是否好玩和引人入胜。
**预期：** Agent 拒绝评估游戏感受，重定向到 game-designer 或 creative-director。
**断言：**
- [ ] 不对机制的设计质量做出任何有约束力的评估
- [ ] 明确命名 `game-designer` 或 `creative-director` 为正确的处理者
- [ ] 可以指出该机制的范围是否有生产影响（例如对其他系统的依赖），但将所有设计评估完全推迟

### 用例 3：Gate 裁决——正确词汇
**场景：** 一个新功能提案将一个里程碑内原本只包含两个系统的范围添加了三个新系统（制作、天气和派系声望）。这些新增项均未出现在当前里程碑计划中。请求标记为 PR-SCOPE。
**预期：** 返回 `PR-SCOPE: CONCERNS`，附有具体识别这三个未规划系统及其在里程碑范围文档中的缺失。
**断言：**
- [ ] 裁决必须是 REALISTIC / CONCERNS / UNREALISTIC 之一——而非自由格式文本
- [ ] 裁决令牌格式化为 `PR-SCOPE: CONCERNS`
- [ ] 理据指出超出范围添加的三个具体系统的名称
- [ ] 不评估这些系统是否为好的设计——仅评估它们是否符合计划

### 用例 4：冲突升级——正确父级
**场景：** game-designer 想添加一个后期机制（影响所有游戏系统的动态天气），technical-director 警告这将需要额外 3 个 sprint。game-designer 和 technical-director 对于是否继续存在分歧。
**预期：** Producer 不对机制是否值得添加（设计决策）或是否可行（技术决策）选边站队。Producer 量化生产影响（3 个 sprint 延迟、里程碑风险），向用户呈现权衡，并遵循 coordination-rules.md 冲突解决：升级到共享父级（此情况下，因为 creative-director 和 technical-director 都是最高层，所以向用户呈现冲突以供决策）。
**断言：**
- [ ] 用具体术语量化生产影响（sprint 数、里程碑日期推迟）
- [ ] 不做有约束力的设计或技术决策
- [ ] 向用户呈现冲突，清楚说明范围影响
- [ ] 引用 coordination-rules.md 冲突解决协议（升级到共享父级或用户）

### 用例 5：上下文传递——使用提供的上下文
**场景：** Agent 收到一个 gate 上下文块，其中包含当前里程碑截止日期（8 周后）和过去 4 个 sprint 的速度数据（8, 10, 9, 11 points）。一个包含 14 个 story points 的 sprint 计划提交了。
**预期：** 评估使用提供的速度数据预测 14 个 points 是否可达成，并引用 8 周里程碑窗口来评估当前 sprint 的范围是否留有足够缓冲。
**断言：**
- [ ] 使用提供上下文中的具体速度数据（而非通用估算）
- [ ] 在容量评估中引用 8 周截止日期
- [ ] 计算或估算里程碑窗口内的剩余 sprint 数
- [ ] 不给出与提供的截止日期和速度数据脱节的通用范围建议

---

## 协议合规性

- [ ] 仅使用 REALISTIC / CONCERNS / UNREALISTIC 词汇返回裁决
- [ ] 保持在声明的生产领域内
- [ ] 通过量化范围影响并向用户呈现来解决设计/技术冲突
- [ ] 在输出中使用 gate ID（例如 `PR-SPRINT: REALISTIC`），而非内联散文式裁决
- [ ] 不做有约束力的游戏设计或技术架构决定

---

## 覆盖说明
- PR-EPIC（epic 级优先级排序）未覆盖——当 `/create-epics` skill 产生结构化 epic 文档时应添加专用用例。
- PR-MILESTONE（里程碑健康审查）未覆盖——延迟到与 `/milestone-review` skill 的集成。
- PR-PHASE-GATE（完整生产阶段推进）涉及综合多个子 gate 结果，已延迟。
- 多 sprint 燃尽图和速度趋势分析未在此覆盖。
