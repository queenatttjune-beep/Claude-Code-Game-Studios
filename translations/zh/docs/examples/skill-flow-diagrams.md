# Skill 流程图

展示 Skill 如何在 7 个开发阶段中串联的可视化地图。显示每个 Skill 之前和之后运行什么，以及它们之间流动的工件。

---

## 完整管线概览（从零到发布）

```
阶段 1：概念
  /start ──────────────────────────────────────────────────────► 路由到 A/B/C/D
  /brainstorm ──────────────────────────────────────────────────► design/gdd/game-concept.md
  /setup-engine ────────────────────────────────────────────────► CLAUDE.md + technical-preferences.md
  /prototype [核心机制] ───────────────────────────────────────► prototypes/[名称]-concept/REPORT.md
        │ 继续                                                    （在编写 GDD 前验证想法）
        ▼
  /design-review [game-concept.md] ────────────────────────────► 概念已验证
  /gate-check ─────────────────────────────────────────────────► 通过 → 进入系统设计
        │
        ▼
阶段 2：系统设计
  /map-systems ────────────────────────────────────────────────► design/gdd/systems-index.md
        │
        ▼（对每个系统，按依赖顺序）
  /design-system [名称] ──────────────────────────────────────► design/gdd/[系统].md
  /design-review [系统].md ───────────────────────────────────► 每 GDD 审查评论
        │
        ▼（所有 MVP GDD 完成后）
  /review-all-gdds ────────────────────────────────────────────► design/gdd/gdd-cross-review-[日期].md
  /gate-check ─────────────────────────────────────────────────► 通过 → 进入技术设置
        │
        ▼
阶段 3：技术设置
  /create-architecture ────────────────────────────────────────► docs/architecture/master.md
  /architecture-decision（×N）─────────────────────────────────► docs/architecture/[adr-nnn].md
  /architecture-review ────────────────────────────────────────► 审查报告 + docs/architecture/tr-registry.yaml
  /create-control-manifest ────────────────────────────────────► docs/architecture/control-manifest.md
  /gate-check ─────────────────────────────────────────────────► 通过 → 进入预生产
        │
        ▼
阶段 4：预生产
  [UX — 在 epic 之前，以便编写 story 时有规范存在]
  /ux-design [屏幕/hud/模式] ────────────────────────────────► design/ux/*.md
  /ux-review ──────────────────────────────────────────────────► UX 规范已批准（/team-ui 的硬关卡）

  [测试基础设施 — 在 story 引用测试之前搭建脚手架]
  /test-setup ─────────────────────────────────────────────────► 测试框架 + CI/CD 管线
  /test-helpers ───────────────────────────────────────────────► tests/helpers/[引擎特定].gd

  [垂直切片 — 在 epic 之前，验证完整的游戏循环]
  /vertical-slice ─────────────────────────────────────────────► prototypes/[名称]-vertical-slice/REPORT.md
  /playtest-report ────────────────────────────────────────────► production/playtests/

  [Story + 冲刺计划 — 仅在垂直切片通过后]
  /create-epics [层] ────────────────────────────────────────► production/epics/*/EPIC.md
  /create-stories [epic-slug] ─────────────────────────────────► production/epics/*/story-*.md
  /sprint-plan new ────────────────────────────────────────────► production/sprints/sprint-01.md
  /gate-check ─────────────────────────────────────────────────► 通过 → 进入生产
        │
        ▼
阶段 5：生产（重复的冲刺循环）
  /sprint-status ──────────────────────────────────────────────► 冲刺快照
  /story-readiness [story] ────────────────────────────────────► story 已验证就绪
        │
        ▼（拾取并实现）
  /dev-story [story] ──────────────────────────────────────────► 路由到正确的程序员 agent
        │
        ▼（实现期间，根据需要）
  /code-review ────────────────────────────────────────────────► 代码审查报告
  /scope-check ────────────────────────────────────────────────► 范围蔓延已检测 / 未检测
  /content-audit ──────────────────────────────────────────────► GDD 内容缺口已识别
  /bug-report ─────────────────────────────────────────────────► production/qa/bugs/bug-NNN.md
  /bug-triage ─────────────────────────────────────────────────► 错误重新确定优先级 + 分配

  [特性领域的团队 skill — 处理完整特性时生成]
  /team-combat / /team-narrative / /team-ui / /team-level / /team-audio

  [每冲刺 QA 周期]
  /qa-plan ────────────────────────────────────────────────────► production/qa/qa-plan-sprint-NN.md
  /smoke-check ────────────────────────────────────────────────► 冒烟测试关卡（通过/失败）
  /regression-suite ───────────────────────────────────────────► 覆盖缺口 + 缺少回归测试的已修复错误
  /test-evidence-review ───────────────────────────────────────► 证据质量报告
  /test-flakiness ─────────────────────────────────────────────► 不稳定测试报告
        │
        ▼
  /story-done [story] ─────────────────────────────────────────► story 已关闭 + 下一个浮现
  /sprint-plan [下一个] ───────────────────────────────────────► 下一个冲刺
        │
        ▼（在 Production 里程碑后）
  /milestone-review ───────────────────────────────────────────► 里程碑报告
  /gate-check ─────────────────────────────────────────────────► 通过 → 进入打磨
        │
        ▼
阶段 6：打磨
  /perf-profile ───────────────────────────────────────────────► 性能报告 + 修复
  /balance-check ──────────────────────────────────────────────► 平衡报告 + 修复
  /asset-audit ────────────────────────────────────────────────► 资源合规报告
  /tech-debt ──────────────────────────────────────────────────► docs/tech-debt-register.md
  /soak-test ──────────────────────────────────────────────────► 浸泡测试协议 + 结果
  /localize ───────────────────────────────────────────────────► 本地化就绪报告
  /team-polish ────────────────────────────────────────────────► 打磨冲刺编排
  /team-qa ────────────────────────────────────────────────────► 完整 QA 周期签核
  /gate-check ─────────────────────────────────────────────────► 通过 → 进入发布
        │
        ▼
阶段 7：发布
  /launch-checklist ───────────────────────────────────────────► 发布就绪报告
  /release-checklist ──────────────────────────────────────────► 平台特定清单
  /changelog ──────────────────────────────────────────────────► CHANGELOG.md
  /patch-notes ────────────────────────────────────────────────► 面向玩家的补丁说明
  /team-release ───────────────────────────────────────────────► 发布管线编排
        │
        ▼（发布后，持续）
  /hotfix ─────────────────────────────────────────────────────► 带审计追踪的紧急修复
  /team-live-ops ──────────────────────────────────────────────► 持续运营内容计划
```

---

## Skill 链：/design-system 详解

单个 GDD 如何被创作、审查并移交给架构：

```
systems-index.md（输入）
game-concept.md（输入）
上游 GDD（输入，如果有）
        │
        ▼
/design-system [名称]
        │
        ├── 预检查：可行性表 + 引擎风险标志
        │
        ├── 部分循环 × 8：
        │     提问 → 选项 → 决策 → 草稿 → 批准 → 写入
        │     [每个部分批准后立即写入文件]
        │
        └── 输出：design/gdd/[系统].md（完整，所有 8 个部分）
                │
                ▼
        /design-review design/gdd/[系统].md
                │
                ├── 已批准 → 在 systems-index 中标记完成，进入下一个系统
                ├── 需要修改 → agent 显示具体问题，重新进入部分循环
                └── 重大修改 → 在下一个系统前需要重大重新设计
                        │
                        ▼（所有 MVP GDD + 交叉审查后）
                /review-all-gdds
                        │
                        └── 输出：gdd-cross-review-[日期].md
```

---

## Skill 链：UX / UI 管线详解

UX 规范在阶段 4（预生产）中创作，在编写 epic 之前，以便 story 验收标准可以引用特定的 UX 工件。

```
design/gdd/*.md（提取 UI/UX 需求）
design/player-journey.md（情感弧线，如果有创作）
        │
        ▼
/ux-design hud              → design/ux/hud.md
/ux-design screen [名称]    → design/ux/screens/[名称].md
/ux-design patterns         → design/ux/interaction-patterns.md
        │
        ▼
/ux-review design/ux/
        │
        ├── 已批准 → UX 规范就绪，继续到 /create-epics
        ├── 需要修改 → 列出阻止性问题 → 修复 → 重新运行审查
        └── 重大修改 → 根本性 UX 问题 → 在 epic 前重新设计
                │
                ▼（批准后 — 在阶段 5 实现 UI 特性时）
        /team-ui
                │
                ├── 阶段 1：/ux-design（如果仍有规范缺失）+ /ux-review
                ├── 阶段 2：视觉设计（art-director）
                ├── 阶段 3：布局实现（ui-programmer）
                ├── 阶段 4：可访问性审计（accessibility-specialist）
                └── 阶段 5：最终审查

注意：/ux-design 和 /ux-review 属于阶段 4（预生产）。
      /team-ui 属于阶段 5（生产），当正在构建 UI 特性时。
```

---

## Skill 链：开发 Story 流程详解

Story 如何从积压移动至关闭：

```
/story-readiness [story]
        │
        ├── 就绪 → 状态：ready-for-dev → 拾取用于实现
        ├── 需要工作 → agent 显示具体缺口 → 解决 → 重新运行就绪检查
        └── 被阻止 → ADR 仍为 Proposed，或上游 story 未完成
                │
                ▼（就绪后）
        /dev-story [story]
                │
                ├── 读取：story 文件、链接的 GDD 要求、ADR 决策、控制清单
                ├── 路由到：gameplay-programmer / engine-programmer / ui-programmer 等
                │
                └── 实现开始
                        │
                        ▼（可选，实现期间/后）
                /code-review          → 变更集的架构审查
                /scope-check          → 验证与原始 story 标准相比无范围蔓延
                /test-evidence-review → 验证测试文件和手动证据质量
                        │
                        ▼
                /story-done [story]
                        │
                        ├── 完成 → 状态：完成、sprint-status.yaml 已更新、下一个 story 已浮现
                        ├── 带备注完成 → 完成但有些标准已延迟（已记录）
                        └── 被阻止 → 验收标准无法验证 → 调查阻止因素
```

---

## Skill 链：Story 生命周期（积压到关闭）

Story 如何从积压到关闭（摘要视图）：

```
/create-epics [层]
        │
        └── 输出：production/epics/[slug]/EPIC.md
                │
                ▼
        /create-stories [epic-slug]
                │
                └── 输出：production/epics/[slug]/story-NNN-[slug].md
                            （状态：就绪，或如果 ADR 为 Proposed 则被阻止）
                │
                ▼
        /story-readiness [story]
                │
                ├── 就绪 → /dev-story → 实现 → /story-done
                ├── 需要工作 → 解决缺口 → 重新运行
                └── 被阻止 → 先修复上游依赖
```

---

## Skill 链：QA 管线详解

```
[阶段 4 — 一次性基础设施设置]
/test-setup ────────────────────────────────────────────────────► 测试框架搭建 + CI/CD 连接
/test-helpers ──────────────────────────────────────────────────► tests/helpers/[引擎].gd（GDUnit4、NUnit 等）

[阶段 5 — 每冲刺 QA 周期]
/qa-plan [冲刺或特性]
        │
        ├── 读取：story 文件、GDD、验收标准
        ├── 按测试类型分类每个 story：
        │     逻辑 → 自动化单元测试（阻止）
        │     集成 → 集成测试或记录的游戏测试（阻止）
        │     视觉/感觉 → 截图 + 负责人签核（建议）
        │     UI → 手动走查或交互测试（建议）
        │     配置/数据 → 冒烟检查（建议）
        └── 输出：production/qa/qa-plan-sprint-NN.md
                │
                ▼
        /smoke-check
                │
                ├── 通过 → QA 交接已清除
                └── 失败 → 阻止冲刺关闭 → 先修复关键路径
                        │
                        ▼
                /regression-suite
                        │
                        └── 覆盖缺口 + 缺少回归测试的已修复错误列表
                                │
                                ▼
                        /test-evidence-review
                                │
                                └── 验证证据质量，不仅是存在性
                                        │
                                        ▼（如果有 CI 运行历史可用）
                        /test-flakiness
                                │
                                └── 不稳定测试报告 + 修复建议

[阶段 6 — 扩展稳定性测试]
/soak-test ─────────────────────────────────────────────────────► 浸泡测试协议 + 观察结果
/team-qa ───────────────────────────────────────────────────────► 发布关卡的完整 QA 周期签核

[持续 — 错误管理]
/bug-report ────────────────────────────────────────────────────► production/qa/bugs/bug-NNN.md
/bug-triage ────────────────────────────────────────────────────► 未结错误重新确定优先级 + 分配

[元 — 框架验证]
/skill-test [lint|spec|catalog] ────────────────────────────────► skill 文件的结构和行为检查
```

---

## 棕地过渡流程

对于有现有工作的项目（使用 `/start` 选项 D 或直接运行）：

```
/project-stage-detect    → 阶段检测报告
        │
        ▼
/adopt
        │
        ├── 阶段 1：检测存在什么
        ├── 阶段 2：FORMAT 审计（不仅是存在性）
        ├── 阶段 3：分类缺口（阻止 / 高 / 中 / 低）
        ├── 阶段 4：有序的迁移计划
        ├── 阶段 5：写入 docs/adoption-plan-[日期].md
        └── 阶段 6：内联修复最紧迫的缺口（可选）
                │
                ▼
        /design-system retrofit [路径]    → 填补缺失的 GDD 部分
        /architecture-decision retrofit [路径] → 填补缺失的 ADR 部分
        /gate-check                       → 你在管线的哪个位置？
```

---

## 如何阅读这些图

| 符号 | 含义 |
|--------|---------|
| `──►` | 产生此工件 |
| `│ ▼` | 流入下一步 |
| `├──` | 分支（多个可能的结果） |
| `×N` | 运行 N 次（每个系统、story 等一次） |
| `(input)` | 由 skill 读取但非在此产生 |
| `[optional]` | 关卡通过不需要 |
| `WRITE`（大写） | 文件立即写入磁盘 |

---

## 常见入口点

| 你的位置 | 运行此 |
|---------------|---------|
| 全新的，没有想法 | `/start` → `/brainstorm` |
| 有概念，没有引擎 | `/setup-engine` |
| 有概念 + 引擎 | `/map-systems` |
| 系统设计中 | `/design-system [下一个系统]` 或 `/map-systems next` |
| 所有 GDD 完成 | `/review-all-gdds` → `/gate-check` |
| 技术设置中 | `/create-architecture` → `/architecture-decision` |
| 开始 UX 设计 | `/ux-design screen [名称]` 或 `/ux-design hud` |
| 搭建测试基础设施 | `/test-setup` → `/test-helpers` |
| 有 story，准备编码 | `/story-readiness [story]` → `/dev-story [story]` |
| Story 完成 | `/story-done [story]` |
| 为冲刺运行 QA | `/qa-plan` → `/smoke-check` → `/regression-suite` |
| 错误积压需要整理 | `/bug-triage` |
| 扩展稳定性测试 | `/soak-test` |
| 不确定 | `/help` |
| 现有项目 | `/adopt` |
