# Agent 协调规则

1. **垂直委派**：领导层 Agent 委派给部门主管，部门主管再委派给专家。复杂决策不得跳过层级。
2. **水平咨询**：同层级的 Agent 可以相互咨询，但不得在其领域之外做出具有约束力的决定。
3. **冲突解决**：当两个 Agent 意见不一致时，上报给共同的上级。如果没有共同上级，设计冲突上报给 `creative-director`，技术冲突上报给 `technical-director`。
4. **变更传播**：当设计变更影响多个领域时，由 `producer` Agent 协调传播。
5. **不得单方面进行跨域更改**：未经明确委派，Agent 不得修改其指定目录之外的文件。

## 模型层级分配

Skill 和 Agent 根据任务复杂度分配到模型层级：

| 层级 | 模型 | 使用时机 |
|------|------|----------|
| **Haiku** | `claude-haiku-4-5-20251001` | 只读状态检查、格式化、简单查询 — 无需创造性判断 |
| **Sonnet** | `claude-sonnet-4-6` | 实现、设计创作、单个系统分析 — 大多数工作的默认选择 |
| **Opus** | `claude-opus-4-6` | 多文档综合、高风险阶段关卡裁决、跨系统整体审查 |

`model: haiku` 的 Skill：`/help`、`/sprint-status`、`/story-readiness`、`/scope-check`、`/project-stage-detect`、`/changelog`、`/patch-notes`、`/onboard`

`model: opus` 的 Skill：`/review-all-gdds`、`/architecture-review`、`/gate-check`

所有其他 Skill 默认为 Sonnet。创建新 Skill 时，如果该 Skill 只读取和格式化则分配 Haiku；如果它需要综合 5 个以上文档且输出具有高风险则分配 Opus；否则保持未设置（Sonnet）。

## 子 Agent 与 Agent 团队

本项目使用两种不同的多 Agent 模式：

### 子 Agent（当前模式，始终有效）
通过单个 Claude Code 会话中的 `Task` 生成。所有 `team-*` skill 和编排 skill 使用。子 Agent 共享会话的权限上下文，在会话内顺序或并行运行，并将结果返回给父级。

**何时并行生成**：如果两个子 Agent 的输入是独立的（彼此不需要对方的输出才能开始），同时生成两个 Task 调用，而不是等待。示例：`/review-all-gdds` 阶段 1（一致性）和阶段 2（设计理论）是独立的 — 同时生成两者。

### Agent 团队（实验性 — 选择加入）
多个独立的 Claude Code *会话*同时运行，通过共享任务列表协调。每个会话有自己的上下文窗口和 token 预算。需要设置 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 环境变量。

**使用 Agent 团队当**：
- 工作跨越多个不会触及相同文件的子系统
- 每个工作流需要 30 分钟以上且受益于真正的并行处理
- 高级 Agent（technical-director、producer）需要协调 3 个以上同时处理不同史诗的专家会话

**不要使用 Agent 团队当**：
- 一个会话的输出需要作为另一个会话的输入（使用顺序子 Agent）
- 任务适合单个会话的上下文（改用子 Agent）
- 成本是考量因素 — 每个团队成员独立消耗 token

**当前状态**：通过 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 选择加入。采用时在此记录首次使用情况。

## 并行任务协议

当编排 skill 生成多个独立 Agent 时：

1. 在等待任何结果之前，先发出所有独立的 Task 调用
2. 收集所有结果后，再进入依赖阶段
3. 如果有任何 Agent 被阻塞，立即汇报 — 不要静默跳过
4. 如果部分 Agent 完成而其他 Agent 阻塞，始终生成部分报告
