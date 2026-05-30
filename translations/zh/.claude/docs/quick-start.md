# 游戏工作室 Agent 架构 -- 快速入门指南

## 这是什么？

这是一个用于游戏开发的完整 Claude Code Agent 架构。它将 49 个专业化的 AI Agent 组织成一个工作室层级结构，模仿真实的游戏开发团队，具有明确的职责、委派规则和协调协议。它包括适用于 Godot、Unity 和 Unreal 的引擎专家 Agent — 每个引擎都有专门的子专家处理主要引擎子系统。所有设计 Agent 和模板都基于成熟的游戏设计理论（MDA 框架、自我决定理论、心流状态、Bartle 玩家类型）。使用与你项目匹配的引擎组。

## 使用方法

### 1. 理解层级结构

Agent 分为三个层级：

- **第一层（Opus）**：做出高层级决策的主管
  - `creative-director` -- 愿景和创意冲突解决
  - `technical-director` -- 架构和技术决策
  - `producer` -- 排期、协调和风险管理

- **第二层（Sonnet）**：拥有各自领域的部门主管
  - `game-designer`、`lead-programmer`、`art-director`、`audio-director`、`narrative-director`、`qa-lead`、`release-manager`、`localization-lead`

- **第三层（Sonnet/Haiku）**：在各自领域内执行的专业人员
  - 设计师、程序员、美术师、编剧、测试员、工程师

### 2. 选择合适的 Agent

问问自己："在真实的工作室中，哪个部门会处理这个？"

| 我需要... | 使用这个 Agent |
|-------------|---------------|
| 设计一个新机制 | `game-designer` |
| 编写战斗代码 | `gameplay-programmer` |
| 创建着色器 | `technical-artist` |
| 编写对话 | `writer` |
| 规划下一个 sprint | `producer` |
| 审查代码质量 | `lead-programmer` |
| 编写测试用例 | `qa-tester` |
| 设计关卡 | `level-designer` |
| 解决性能问题 | `performance-analyst` |
| 设置 CI/CD | `devops-engineer` |
| 设计掉落表 | `economy-designer` |
| 解决创意冲突 | `creative-director` |
| 做出架构决策 | `technical-director` |
| 管理发布 | `release-manager` |
| 准备翻译用字符串 | `localization-lead` |
| 快速测试机制想法 | `prototyper` |
| 审查安全问题 | `security-engineer` |
| 检查无障碍合规性 | `accessibility-specialist` |
| 获取 Unreal Engine 建议 | `unreal-specialist` |
| 获取 Unity 建议 | `unity-specialist` |
| 获取 Godot 建议 | `godot-specialist` |
| 设计 GAS 能力/效果 | `ue-gas-specialist` |
| 定义 BP/C++ 边界 | `ue-blueprint-specialist` |
| 实现 UE 复制 | `ue-replication-specialist` |
| 构建 UMG/CommonUI 控件 | `ue-umg-specialist` |
| 设计 DOTS/ECS 架构 | `unity-dots-specialist` |
| 编写 Unity 着色器/VFX | `unity-shader-specialist` |
| 管理 Addressable 资源 | `unity-addressables-specialist` |
| 构建 UI Toolkit/UGUI 界面 | `unity-ui-specialist` |
| 编写地道的 GDScript | `godot-gdscript-specialist` |
| 编写 Godot C# 代码 | `godot-csharp-specialist` |
| 创建 Godot 着色器 | `godot-shader-specialist` |
| 构建 GDExtension 模块 | `godot-gdextension-specialist` |
| 规划活动与赛季 | `live-ops-designer` |
| 为玩家编写补丁说明 | `community-manager` |
| 构思新游戏创意 | 使用 `/brainstorm` skill |

### 3. 使用斜杠命令处理常见任务

| 命令 | 功能 |
|---------|-------------|
| `/start` | 首次引导 — 询问你当前状态，引导到正确的工作流程 |
| `/help` | 上下文感知的"我下一步该做什么？" — 读取当前阶段和资源 |
| `/project-stage-detect` | 分析项目状态，检测阶段，识别差距 |
| `/setup-engine` | 配置引擎 + 版本，填充参考文档 |
| `/adopt` | 现有项目（Brownfield）审计和迁移计划 |
| `/brainstorm` | 从头开始引导式游戏概念构思 |
| `/map-systems` | 将概念分解为系统，映射依赖关系，引导逐个系统的 GDD |
| `/design-system` | 针对单个游戏系统的逐个章节引导式 GDD 编写 |
| `/quick-design` | 小型更改的轻量级规格 — 调优、微调、小功能添加 |
| `/review-all-gdds` | 跨 GDD 一致性和游戏设计理论审查 |
| `/propagate-design-change` | 查找受 GDD 变更影响的 ADR 和 story |
| `/art-bible` | 逐个章节引导式 Art Bible 编写 — 在资源生产前创建视觉标识规格 |
| `/asset-spec` | 从 GDD 或角色档案生成每个资源的视觉规格和 AI 生成提示 |
| `/ux-design` | 编写 UX 规格（界面/流程、HUD、交互模式） |
| `/ux-review` | 验证 UX 规格的无障碍性和 GDD 对齐 |
| `/create-architecture` | 游戏的主架构文档 |
| `/architecture-decision` | 创建 ADR |
| `/architecture-review` | 验证所有 ADR、依赖顺序、GDD 可追溯性 |
| `/create-control-manifest` | 根据已接受的 ADR 生成扁平化的程序员规则表 |
| `/create-epics` | 将 GDD + ADR 转换为史诗（每个架构模块一个） |
| `/create-stories` | 将单个史诗分解为可实现的 story 文件 |
| `/dev-story` | 读取并实现一个 story — 路由到正确的程序员 Agent |
| `/sprint-plan` | 创建或更新 sprint 计划 |
| `/sprint-status` | 快速的 30 行 sprint 快照 |
| `/story-readiness` | 验证 story 是否已准备好实现 |
| `/story-done` | Story 完成审查 — 验证验收标准 |
| `/estimate` | 生成结构化的工作量估算 |
| `/design-review` | 审查设计文档 |
| `/code-review` | 审查代码质量和架构 |
| `/balance-check` | 分析游戏平衡数据 |
| `/asset-audit` | 审计资源合规性 |
| `/content-audit` | GDD 指定内容与已实现内容对比 — 查找差距 |
| `/scope-check` | 检测范围蔓延 |
| `/perf-profile` | 性能分析和瓶颈识别 |
| `/tech-debt` | 扫描、跟踪和优先处理技术债务 |
| `/gate-check` | 验证阶段就绪性（PASS/CONCERNS/FAIL） |
| `/consistency-check` | 扫描所有 GDD 以发现跨文档不一致（冲突的数据、名称、规则） |
| `/security-audit` | 审计安全漏洞：存档篡改、作弊向量、网络攻击、数据泄露 |
| `/reverse-document` | 从现有代码生成设计/架构文档 |
| `/milestone-review` | 审查里程碑进度 |
| `/retrospective` | 运行 sprint/里程碑回顾 |
| `/bug-report` | 创建结构化的 Bug 报告 |
| `/playtest-report` | 创建或分析玩法测试反馈 |
| `/onboard` | 为某个角色生成入职文档 |
| `/release-checklist` | 验证发布前检查清单 |
| `/launch-checklist` | 完整的发布就绪验证 |
| `/changelog` | 从 git 历史生成更新日志 |
| `/patch-notes` | 生成面向玩家的补丁说明 |
| `/hotfix` | 带有审计跟踪的紧急修复 |
| `/day-one-patch` | 为 Gold Master 之后、公开上线之前发现的已知问题准备首日补丁 |
| `/prototype` | 概念原型 — 在编写 GDD 之前验证核心创意（阶段 1） |
| `/vertical-slice` | 生产质量的端到端构建 — 验证完整游戏循环（阶段 4） |
| `/localize` | 本地化扫描、提取、验证 |
| `/team-combat` | 编排完整的战斗团队流水线 |
| `/team-narrative` | 编排完整的叙事团队流水线 |
| `/team-ui` | 编排完整的 UI 团队流水线 |
| `/team-release` | 编排完整的发布团队流水线 |
| `/team-polish` | 编排完整的打磨团队流水线 |
| `/team-audio` | 编排完整的音频团队流水线 |
| `/team-level` | 编排完整的关卡创建流水线 |
| `/team-live-ops` | 编排持续运营团队处理赛季、活动和发布后内容 |
| `/team-qa` | 编排完整的 QA 团队周期 — 测试计划、测试用例、冒烟检查、签收 |
| `/qa-plan` | 为 sprint 或功能生成 QA 测试计划 |
| `/bug-triage` | 重新优先处理开放的 bug，分配到 sprint，发现系统性趋势 |
| `/smoke-check` | 在 QA 移交前运行关键路径冒烟测试关卡（PASS/FAIL） |
| `/soak-test` | 为长时间游戏会话生成浸泡测试协议 |
| `/regression-suite` | 将测试覆盖映射到 GDD 关键路径，标记差距，维护回归套件 |
| `/test-setup` | 为项目引擎搭建测试框架 + CI 流水线（运行一次） |
| `/test-helpers` | 生成引擎特定的测试辅助库和工厂函数 |
| `/test-flakiness` | 从 CI 历史检测不稳定测试，标记为隔离或修复 |
| `/test-evidence-review` | 测试文件和手动证据的质量审查 — ADEQUATE/INCOMPLETE/MISSING |
| `/skill-test` | 验证 skill 文件的合规性和正确性（静态/规格/审计） |
| `/skill-improve` | 使用测试-修复-重测循环改进 skill — 诊断、提出修复方案、重写、验证 |

### 4. 使用模板创建新文档

模板位于 `.claude/docs/templates/`：

- `game-design-document.md` -- 用于新机制和系统
- `architecture-decision-record.md` -- 用于技术决策
- `architecture-traceability.md` -- 将 GDD 需求映射到 ADR，再映射到 story ID
- `risk-register-entry.md` -- 用于新风险
- `narrative-character-sheet.md` -- 用于新角色
- `test-plan.md` -- 用于功能测试计划
- `sprint-plan.md` -- 用于 sprint 规划
- `milestone-definition.md` -- 用于新里程碑
- `level-design-document.md` -- 用于新关卡
- `game-pillars.md` -- 用于核心设计支柱
- `art-bible.md` -- 用于视觉风格参考
- `technical-design-document.md` -- 用于每个系统的技术设计
- `post-mortem.md` -- 用于项目/里程碑回顾
- `sound-bible.md` -- 用于音频风格参考
- `release-checklist-template.md` -- 用于平台发布检查清单
- `changelog-template.md` -- 用于面向玩家的补丁说明
- `release-notes.md` -- 用于面向玩家的发布说明
- `incident-response.md` -- 用于在线事件响应预案
- `game-concept.md` -- 用于初始游戏概念（MDA、SDT、Flow、Bartle）
- `pitch-document.md` -- 用于向利益相关者推销游戏
- `economy-model.md` -- 用于虚拟经济设计（sink/faucet 模型）
- `faction-design.md` -- 用于阵营身份、世界观和玩法角色
- `systems-index.md` -- 用于系统分解和依赖映射
- `project-stage-report.md` -- 用于项目阶段检测输出
- `design-doc-from-implementation.md` -- 用于将现有代码反向文档化为 GDD
- `architecture-doc-from-code.md` -- 用于将代码反向文档化为架构文档
- `concept-doc-from-prototype.md` -- 用于将原型反向文档化为概念文档
- `ux-spec.md` -- 用于逐个界面的 UX 规格（布局区域、状态、事件）
- `hud-design.md` -- 用于全游戏 HUD 理念、区域和元素规格
- `accessibility-requirements.md` -- 用于项目范围的无障碍层级和功能矩阵
- `interaction-pattern-library.md` -- 用于标准 UI 控件和游戏特定模式
- `player-journey.md` -- 用于 6 阶段情感弧线和按时间尺度的留存钩子
- `difficulty-curve.md` -- 用于难度轴、新手引导坡道和跨系统交互
- `test-evidence.md` -- 用于记录手动测试证据的模板（截图、操作记录笔记）

位于 `.claude/docs/templates/collaborative-protocols/`（由 Agent 使用，通常不直接编辑）：

- `design-agent-protocol.md` -- 设计 Agent 的"问题-选项-草稿-批准"循环
- `implementation-agent-protocol.md` -- 编程 Agent 的 story 接取到 /story-done 循环
- `leadership-agent-protocol.md` -- 主管级 Agent 的跨部门委派和升级机制

### 5. 遵循协调规则

1. 工作沿层级向下流动：主管 -> 主管 -> 专家
2. 冲突沿层级向上升级
3. 跨部门工作由 `producer` 协调
4. Agent 未经委派不得修改其领域之外的文件
5. 所有决策均有文档记录

## 新项目的第一步

**不知道从哪里开始？** 运行 `/start`。它会询问你当前状态并路由到正确的工作流程。不会对你的游戏、引擎或经验水平做任何假设。

如果你已经知道需要什么，直接跳转到相关路径：

### 路径 A："我不知道要做什么游戏"

1. **运行 `/start`**（或 `/brainstorm open`） — 引导式创意探索：什么让你兴奋、你玩过什么、你的限制条件
   - 生成 3 个概念，帮你选择一个，定义核心循环和支柱
   - 生成游戏概念文档并推荐引擎
2. **设置引擎** — 运行 `/setup-engine`（使用 brainstorm 推荐）
   - 配置 CLAUDE.md，检测知识差距，填充参考文档
   - 创建 `.claude/docs/technical-preferences.md`，包含命名规范、性能预算和引擎特定默认值
   - 如果引擎版本晚于 LLM 的训练数据，从网络获取当前文档，以便 Agent 建议正确的 API
3. **验证概念** — 运行 `/design-review design/gdd/game-concept.md`
4. **分解为系统** — 运行 `/map-systems` 映射所有系统和依赖关系
5. **设计每个系统** — 按依赖顺序运行 `/design-system [system-name]` 编写 GDD
6. **原型机制** — 运行 `/prototype [core-mechanic]`（1-3 天 — 在编写 GDD 之前）
7. **设计每个系统** — 运行 `/design-system [system-name]` 编写 GDD，参考原型发现
8. **规划第一个 sprint** — 架构和 `/vertical-slice` 之后，运行 `/sprint-plan new`
9. 开始构建

### 路径 B："我知道要做什么游戏"

如果你已经有游戏概念和引擎选择：

1. **设置引擎** — 运行 `/setup-engine [engine] [version]`（例如 `/setup-engine godot 4.6`）— 同时也创建技术偏好
2. **编写游戏支柱** — 委派给 `creative-director`
3. **分解为系统** — 运行 `/map-systems` 枚举系统和依赖关系
4. **设计每个系统** — 按依赖顺序为 GDD 运行 `/design-system [system-name]`
5. **创建初始 ADR** — 运行 `/architecture-decision`
6. **创建第一个里程碑** 在 `production/milestones/` 中
7. **规划第一个 sprint** — 运行 `/sprint-plan new`
8. 开始构建

### 路径 C："我了解游戏但不了解引擎"

如果你有概念但不知道哪个引擎适合：

1. **运行 `/setup-engine`** 不带参数 — 它会询问你的游戏需求（2D/3D、平台、团队规模、语言偏好）并根据你的回答推荐引擎
2. 从路径 B 的第 2 步开始

### 路径 D："我有现有项目"

如果你已经有设计文档、原型或代码：

1. **运行 `/start`**（或 `/project-stage-detect`）— 分析现有内容，识别差距，推荐后续步骤
2. **运行 `/adopt`** 如果你有现有的 GDD、ADR 或 story — 审计内部格式合规性，构建带编号的迁移计划来填补差距而不覆盖你现有的工作
3. **根据需要配置引擎** — 如果尚未配置，运行 `/setup-engine`
4. **验证阶段就绪性** — 运行 `/gate-check` 查看当前状态
5. **规划下一个 sprint** — 运行 `/sprint-plan new`

## 文件结构参考

```
CLAUDE.md                          -- 主配置（先读这个，约 60 行）
.claude/
  settings.json                    -- Claude Code hook 和项目设置
  agents/                          -- 49 个 Agent 定义（YAML frontmatter）
  skills/                          -- 73 个斜杠命令定义（YAML frontmatter）
  hooks/                           -- 12 个 hook 脚本（.sh），由 settings.json 配置
  rules/                           -- 11 个路径特定规则文件
  docs/
    quick-start.md                 -- 本文件
    technical-preferences.md       -- 项目特定标准（由 /setup-engine 填充）
    coding-standards.md            -- 编码和设计文档标准
    coordination-rules.md          -- Agent 协调规则
    context-management.md          -- 上下文预算和压缩说明
    directory-structure.md         -- 项目目录布局
    workflow-catalog.yaml          -- 7 阶段流水线定义（由 /help 读取）
    setup-requirements.md          -- 系统先决条件（Git Bash、jq、Python）
    settings-local-template.md     -- 个人 settings.local.json 指南
    templates/                     -- 41 个文档模板
```
