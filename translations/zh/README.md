<p align="center">
  <h1 align="center">Claude Code Game Studios</h1>
  <p align="center">
    将单个 Claude Code 会话转变为完整的游戏开发工作室。
    <br />
    49 个 agent。73 个 skill。一个协调的 AI 团队。
  </p>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="MIT License"></a>
  <a href=".claude/agents"><img src="https://img.shields.io/badge/agents-49-blueviolet" alt="49 Agents"></a>
  <a href=".claude/skills"><img src="https://img.shields.io/badge/skills-73-green" alt="73 Skills"></a>
  <a href=".claude/hooks"><img src="https://img.shields.io/badge/hooks-12-orange" alt="12 Hooks"></a>
  <a href=".claude/rules"><img src="https://img.shields.io/badge/rules-11-red" alt="11 Rules"></a>
  <a href="https://docs.anthropic.com/en/docs/claude-code"><img src="https://img.shields.io/badge/built%20for-Claude%20Code-f5f5f5?logo=anthropic" alt="Built for Claude Code"></a>
  <a href="https://www.buymeacoffee.com/donchitos3"><img src="https://img.shields.io/badge/Buy%20Me%20a%20Coffee-Support%20this%20project-FFDD00?logo=buymeacoffee&logoColor=black" alt="Buy Me a Coffee"></a>
  <a href="https://github.com/sponsors/Donchitos"><img src="https://img.shields.io/badge/GitHub%20Sponsors-Support%20this%20project-ea4aaa?logo=githubsponsors&logoColor=white" alt="GitHub Sponsors"></a>
</p>

---

## 为什么存在

独自用 AI 构建游戏很强大 -- 但单个聊天会话没有结构。没人阻止你对魔法数字进行硬编码、跳过设计文档或编写意大利面条式代码。没有 QA 检查、没有设计评审、没有人问"这真的符合游戏的愿景吗？"

**Claude Code Game Studios** 通过给你的 AI 会话提供真实工作室的结构来解决这个问题。你不是只有一个通用助手，而是获得 49 个专业 agent，组织成一个工作室层级 -- 守护愿景的总监、拥有其领域的部门主管以及进行实际工作的专家。每个 agent 都有明确的职责、升级路径和质量门控。

结果: 你仍然做出所有决策，但现在你拥有了一个会提出正确问题、及早发现错误并让你的项目从第一次头脑风暴到发布始终保持有序的团队。

---

## 目录

- [包含内容](#包含内容)
- [工作室层级](#工作室层级)
- [斜杠命令](#斜杠命令)
- [快速开始](#快速开始)
- [升级](#升级)
- [项目结构](#项目结构)
- [工作原理](#工作原理)
- [设计理念](#设计理念)
- [自定义](#自定义)
- [平台支持](#平台支持)
- [社区](#社区)
- [支持本项目](#支持本项目)
- [许可](#许可)

---

## 包含内容

| 类别 | 数量 | 描述 |
|----------|-------|-------------|
| **Agent** | 49 | 涵盖设计、编程、美术、音频、叙事、QA 和制作的专业子 agent |
| **Skill** | 73 | 面向每个工作流阶段的斜杠命令 (`/start`、`/design-system`、`/create-epics`、`/create-stories`、`/dev-story`、`/story-done` 等) |
| **Hook** | 12 | 提交、推送、资源变更、会话生命周期、agent 审计追踪和缺口检测时的自动验证 |
| **规则** | 11 | 路径作用域编码标准，在编辑游戏玩法、引擎、AI、UI、网络代码等时强制执行 |
| **模板** | 41 | 面向 GDD、UX 规范、ADR、sprint 计划、HUD 设计、无障碍等的文档模板 |

## 工作室层级

Agent 被组织为三个层级，与真实工作室的运作方式相匹配:

```
第一层 -- 总监 (Opus)
  creative-director    technical-director    producer

第二层 -- 部门主管 (Sonnet)
  game-designer        lead-programmer       art-director
  audio-director       narrative-director    qa-lead
  release-manager      localization-lead

第三层 -- 专家 (Sonnet/Haiku)
  gameplay-programmer  engine-programmer     ai-programmer
  network-programmer   tools-programmer      ui-programmer
  systems-designer     level-designer        economy-designer
  technical-artist     sound-designer        writer
  world-builder        ux-designer           prototyper
  performance-analyst  devops-engineer       analytics-engineer
  security-engineer    qa-tester             accessibility-specialist
  live-ops-designer    community-manager
```

### 引擎专家

模板包含所有三大引擎的 agent 集。使用与你的项目匹配的集合:

| 引擎 | 主导 Agent | 子专家 |
|--------|-----------|-----------------|
| **Godot 4** | `godot-specialist` | GDScript、Shaders、GDExtension |
| **Unity** | `unity-specialist` | DOTS/ECS、Shaders/VFX、Addressables、UI Toolkit |
| **Unreal Engine 5** | `unreal-specialist` | GAS、Blueprints、Replication、UMG/CommonUI |

## 斜杠命令

在 Claude Code 中键入 `/` 来访问所有 73 个 skill:

**入门与导航**
`/start` `/help` `/project-stage-detect` `/setup-engine` `/adopt`

**游戏设计**
`/brainstorm` `/map-systems` `/design-system` `/quick-design` `/review-all-gdds` `/propagate-design-change`

**美术与资源**
`/art-bible` `/asset-spec` `/asset-audit`

**UX 与界面设计**
`/ux-design` `/ux-review`

**架构**
`/create-architecture` `/architecture-decision` `/architecture-review` `/create-control-manifest`

**Story 与 Sprint**
`/create-epics` `/create-stories` `/dev-story` `/sprint-plan` `/sprint-status` `/story-readiness` `/story-done` `/estimate`

**评审与分析**
`/design-review` `/code-review` `/balance-check` `/content-audit` `/scope-check` `/perf-profile` `/tech-debt` `/gate-check` `/consistency-check` `/security-audit`

**QA 与测试**
`/qa-plan` `/smoke-check` `/soak-test` `/regression-suite` `/test-setup` `/test-helpers` `/test-evidence-review` `/test-flakiness` `/skill-test` `/skill-improve`

**生产**
`/milestone-review` `/retrospective` `/bug-report` `/bug-triage` `/reverse-document` `/playtest-report`

**发布**
`/release-checklist` `/launch-checklist` `/changelog` `/patch-notes` `/hotfix` `/day-one-patch`

**创意与内容**
`/prototype` `/onboard` `/localize`

**团队编排** (在单个 feature 上协调多个 agent)
`/team-combat` `/team-narrative` `/team-ui` `/team-release` `/team-polish` `/team-audio` `/team-level` `/team-live-ops` `/team-qa`

## 快速开始

### 前置条件

- [Git](https://git-scm.com/)
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (`npm install -g @anthropic-ai/claude-code`)
- **推荐**: [jq](https://jqlang.github.io/jq/) (用于 hook 验证) 和 Python 3 (用于 JSON 验证)

如果缺少可选工具，所有 hook 都会优雅降级 -- 不会出问题，只是失去验证功能。

### 设置

1. **克隆或用作模板**:
   ```bash
   git clone https://github.com/Donchitos/Claude-Code-Game-Studios.git my-game
   cd my-game
   ```

2. **打开 Claude Code** 并启动会话:
   ```bash
   claude
   ```

3. **运行 `/start`** -- 系统会询问你的情况 (毫无头绪、概念模糊、
   设计明确、已有项目) 并引导你到正确的工作流。不做任何假设。

   如果你已经知道自己需要什么，也可以直接跳到特定 skill:
   - `/brainstorm` -- 从头探索游戏创意
   - `/setup-engine godot 4.6` -- 如果你已知道引擎，进行配置
   - `/project-stage-detect` -- 分析现有项目

## 升级

已经在使用此模板的旧版本？请参见 [UPGRADING.md](UPGRADING.md)
了解逐步迁移说明、版本间变更详情以及哪些文件可以安全覆盖 vs. 哪些需要手动合并。

## 项目结构

```
CLAUDE.md                           # 主配置
.claude/
  settings.json                     # Hook、权限、安全规则
  agents/                           # 49 个 agent 定义 (markdown + YAML frontmatter)
  skills/                           # 73 个斜杠命令 (每个 skill 子目录)
  hooks/                            # 12 个 hook 脚本 (bash、跨平台)
  rules/                            # 11 个路径作用域编码标准
  statusline.sh                     # 状态行脚本 (context%、model、stage、epic 面包屑)
  docs/
    workflow-catalog.yaml           # 7 阶段管道定义 (由 /help 读取)
    templates/                      # 41 个文档模板
src/                                # 游戏源代码
assets/                             # 美术、音频、VFX、着色器、数据文件
design/                             # GDD、叙事文档、关卡设计
docs/                               # 技术文档和 ADR
tests/                              # 测试套件 (单元、集成、性能、试玩)
tools/                              # 构建和管道工具
prototypes/                         # 一次性原型 (与 src/ 隔离)
production/                         # Sprint 计划、里程碑、发布跟踪
```

## 工作原理

### Agent 协调

Agent 遵循结构化的委托模型:

1. **垂直委托** -- 总监委托给主管，主管委托给专家
2. **水平咨询** -- 同级 agent 可以互相咨询，但不能做出跨域的具有约束力的决策
3. **冲突解决** -- 分歧向共享父级升级 (`creative-director` 处理设计问题，`technical-director` 处理技术问题)
4. **变更传播** -- 跨部门变更由 `producer` 协调
5. **领域边界** -- agent 未经明确授权不得修改其领域之外的文件

### 协作而非自主

这 **不是** 一个自动驾驶系统。每个 agent 遵循严格的协作协议:

1. **询问** -- agent 在提出解决方案前会先提问
2. **展示选项** -- agent 展示 2-4 个选项，附有优缺点
3. **你做决定** -- 用户始终做出最终决定
4. **草稿** -- agent 在最终确定前展示工作成果
5. **批准** -- 未经你的签字确认，不会写入任何内容

你保持控制。Agent 提供结构和专业知识，而非自主权。

### 自动化安全

**Hook** 在每个会话中自动运行:

| Hook | 触发器 | 作用 |
|------|---------|--------------|
| `validate-commit.sh` | PreToolUse (Bash) | 检查硬编码值、TODO 格式、JSON 有效性、设计文档章节 -- 如果命令不是 `git commit` 则提前退出 |
| `validate-push.sh` | PreToolUse (Bash) | 推送到受保护分支时发出警告 -- 如果命令不是 `git push` 则提前退出 |
| `validate-assets.sh` | PostToolUse (Write/Edit) | 验证命名约定和 JSON 结构 -- 如果文件不在 `assets/` 中则提前退出 |
| `session-start.sh` | 会话打开 | 显示当前分支和最近提交以提供方向感 |
| `detect-gaps.sh` | 会话打开 | 检测新项目 (建议运行 `/start`) 以及在存在代码或原型时的缺失设计文档 |
| `pre-compact.sh` | 压缩前 | 保留会话进度笔记 |
| `post-compact.sh` | 压缩后 | 提醒 Claude 从 `active.md` 恢复会话状态 |
| `notify.sh` | 通知事件 | 通过 PowerShell 显示 Windows 通知 |
| `session-stop.sh` | 会话关闭 | 将 `active.md` 归档到会话日志并记录 git 活动 |
| `log-agent.sh` | Agent 生成 | 审计追踪开始 -- 记录子 agent 调用 |
| `log-agent-stop.sh` | Agent 停止 | 审计追踪结束 -- 完成子 agent 记录 |
| `validate-skill-change.sh` | PostToolUse (Write/Edit) | 在 `.claude/skills/` 变更后建议运行 `/skill-test` |

> **注意**: `validate-commit.sh`、`validate-assets.sh` 和 `validate-skill-change.sh` 在每次 Bash/Write 工具调用时触发，并在命令或文件路径不相关时立即退出 (exit 0)。这是正常的 hook 行为 -- 不是性能问题。

**权限规则** 在 `settings.json` 中自动允许安全操作 (git status、测试运行) 并阻止危险操作 (force push、`rm -rf`、读取 `.env` 文件)。

### 路径作用域规则

编码标准根据文件位置自动强制执行:

| 路径 | 强制执行内容 |
|------|----------|
| `src/gameplay/**` | 数据驱动值、delta time 使用、无 UI 引用 |
| `src/core/**` | 热路径零分配、线程安全、API 稳定性 |
| `src/ai/**` | 性能预算、可调试性、数据驱动参数 |
| `src/networking/**` | 服务端权威、版本化消息、安全性 |
| `src/ui/**` | 无游戏状态所有权、本地化就绪、无障碍 |
| `design/gdd/**` | 必需的 8 个章节、公式格式、边界情况 |
| `tests/**` | 测试命名、覆盖率要求、fixture 模式 |
| `prototypes/**` | 宽松标准、需要 README、记录假设 |

## 设计理念

此模板基于专业游戏开发实践:

- **MDA 框架** -- 机制、动态、美学分析用于游戏设计
- **自我决定理论** -- 自主、胜任、关联用于玩家动机
- **心流状态设计** -- 挑战-技能平衡用于玩家参与度
- **Bartle 玩家类型** -- 受众定位和验证
- **验证驱动开发** -- 先测试，后实现

## 自定义

这是一个 **模板**，不是锁定的框架。所有内容都可以自定义:

- **添加/删除 agent** -- 删除不需要的 agent 文件，为你的领域添加新的
- **编辑 agent 提示** -- 调整 agent 行为，添加项目特定知识
- **修改 skill** -- 调整工作流以匹配你团队的流程
- **添加规则** -- 为项目目录结构创建新的路径作用域规则
- **调整 hook** -- 调整验证严格度，添加新检查
- **选择你的引擎** -- 使用 Godot、Unity 或 Unreal agent 集 (或不使用)
- **设置评审强度** -- `full` (所有总监门控)、`lean` (仅阶段门控) 或 `solo` (无)。在 `/start` 期间设置或编辑 `production/review-mode.txt`。在任何 skill 上使用 `--review solo` 每次运行覆盖。

## 平台支持

主要开发和测试在 **Windows 10** 上使用 Git Bash 进行。所有 hook 使用 POSIX 兼容模式 (`grep -E`，而不是 `grep -P`) 并为缺失的工具包含后备方案，因此它们应该在 macOS 和 Linux 上也能运行。`notify.sh` hook 使用 PowerShell 进行 Windows 通知，在其他平台上是空操作 -- macOS/Linux 上的桌面通知尚未连接。跨平台测试正在进行中；请提交 issue 报告任何特定平台的故障。

## 社区

- **讨论** -- [GitHub Discussions](https://github.com/Donchitos/Claude-Code-Game-Studios/discussions) 用于提问、分享想法和展示你的成果
- **Issues** -- [Bug 报告和功能请求](https://github.com/Donchitos/Claude-Code-Game-Studios/issues)

---

## 支持本项目

Claude Code Game Studios 是免费开源的。如果它节省了你的时间或帮助你发布了你的游戏，请考虑支持持续开发:

<p>
  <a href="https://www.buymeacoffee.com/donchitos3"><img src="https://img.shields.io/badge/Buy%20Me%20a%20Coffee-FFDD00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black" alt="Buy Me a Coffee"></a>
  &nbsp;
  <a href="https://github.com/sponsors/Donchitos"><img src="https://img.shields.io/badge/GitHub%20Sponsors-ea4aaa?style=for-the-badge&logo=githubsponsors&logoColor=white" alt="GitHub Sponsors"></a>
</p>

- **[Buy Me a Coffee](https://www.buymeacoffee.com/donchitos3)** -- 一次性支持
- **[GitHub Sponsors](https://github.com/sponsors/Donchitos)** -- 通过 GitHub 的定期支持

赞助有助于资助维护 skill、添加新 agent、跟进 Claude Code 和引擎 API 变更以及回复社区 issue 所花费的时间。

---

*为 Claude Code 构建。维护和扩展 -- 欢迎通过 [GitHub Discussions](https://github.com/Donchitos/Claude-Code-Game-Studios/discussions) 贡献。*

## 许可

MIT 许可。详情请参见 [LICENSE](LICENSE)。
