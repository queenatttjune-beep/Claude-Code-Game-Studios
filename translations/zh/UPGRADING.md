# 升级 Claude Code Game Studios

本指南涵盖了如何将现有游戏项目仓库从模板的一个版本
升级到下一个版本。

**在 git 日志中找到您的当前版本**:
```bash
git log --oneline | grep -i "release\|setup"
```
或者查看 `README.md` 中的版本徽章。

---

## 目录

- [升级策略](#升级策略)
- [v1.0.0-beta → v1.0](#v100-beta--v10)
- [v0.4.x → v1.0](#v04x--v10)
- [v0.4.0 → v0.4.1](#v040--v041)
- [v0.3.0 → v0.4.0](#v030--v040)
- [v0.2.0 → v0.3.0](#v020--v030)
- [v0.1.0 → v0.2.0](#v010--v020)

---

## 升级策略

有三种方式引入模板更新。根据仓库的设置方式选择。

### 策略 A -- Git 远程合并 (推荐)

最适合: 你克隆了模板并在其基础上有自己的提交。

```bash
# 将模板添加为远程仓库 (一次性设置)
git remote add template https://github.com/Donchitos/Claude-Code-Game-Studios.git

# 获取新版本
git fetch template main

# 合并到你的分支
git merge template/main --allow-unrelated-histories
```

Git 只会在模板 *和* 你都修改过的文件中标记冲突。逐一解决 -- 你的游戏内容保留，结构改进随之而来。然后提交合并。

**提示:** 最可能冲突的文件是 `CLAUDE.md` 和
`.claude/docs/technical-preferences.md`，因为你已经填写了引擎和项目设置。保留你的内容；接受结构变更。

---

### 策略 B -- Cherry-pick 特定提交

最适合: 你只想要一个特定功能 (例如，只想要新的 skill，而不是完整更新)。

```bash
git remote add template https://github.com/Donchitos/Claude-Code-Game-Studios.git
git fetch template main

# Cherry-pick 你想要的特定提交
git cherry-pick <commit-sha>
```

每个版本的提交 SHA 列在下面的版本章节中。

---

### 策略 C -- 手动文件复制

最适合: 你没有使用 git 来设置模板 (只是下载了 zip)。

1. 在仓库旁边下载或克隆新版本。
2. 直接复制 **"可安全覆盖"** 下列出的文件。
3. 对于 **"需仔细合并"** 下的文件，同时打开两个版本，
   手动合并结构变更，同时保留你的内容。

---

## v0.4.1

**发布日期:** 2026-04-02
**关键主题:** 美术方向集成、资源规格管道

### 变更内容

| 类别 | 变更 |
|----------|---------|
| **新 skill** | `/art-bible` -- 引导式逐章节视觉标识创作 (9 章节)。每章节强制生成 art-director Task。AD-ART-BIBLE 签字门控。在技术设置阶段必需。 |
| **新 skill** | `/asset-spec` -- 每个资源的视觉规格和 AI 生成提示生成器。读取 art bible + GDD/关卡/角色文档。写入 `design/assets/specs/` 文件和 `design/assets/asset-manifest.md`。支持 full/lean/solo 模式。 |
| **新总监门控 (3 个)** | `AD-CONCEPT-VISUAL` (头脑风暴阶段 4)，`AD-ART-BIBLE` (art bible 签字)，`AD-PHASE-GATE` (gate-check 面板) |
| **`/brainstorm` 更新** | 在 allowed-tools 中添加了 `Task` (之前缺失 -- 阻止了所有总监生成)。Art-director 现在与 creative-director 在 pillars 锁定后并行生成。视觉标识锚点写入 game-concept.md。 |
| **`/gate-check` 更新** | Art-director 作为第 4 个并行总监添加 (AD-PHASE-GATE)。视觉制品检查: 视觉标识锚点 (Concept 门控)、art bible (Technical Setup 门控)、AD-ART-BIBLE 签字 + 角色视觉档案 (Pre-Production 门控)。 |
| **`/team-level` 更新** | Art-director 添加到步骤 1 并行生成 (布局前的视觉方向)。Level-designer 现在接收 art-director 目标作为明确约束。步骤 4 art-director 角色修正为仅生产概念。 |
| **`/team-narrative` 更新** | Art-director 添加到阶段 2 并行生成 (角色视觉设计、环境叙事、电影基调)。 |
| **`/design-system` 更新** | 路由表扩展，为战斗、UI、对话、动画/VFX、角色类别添加 art-director + technical-artist。视觉/音频部分现在对 7 个系统类别强制 (包含 art-director Task 生成)。 |
| **`workflow-catalog.yaml`** | `/art-bible` 添加到 Technical Setup (必需)。`/asset-spec` 添加到 Pre-Production (可选、可重复)。 |

### 文件: 可安全覆盖

**需要添加的新文件:**
```
.claude/skills/art-bible/SKILL.md
.claude/skills/asset-spec/SKILL.md
.claude/docs/director-gates.md
```

**需要覆盖的现有文件 (无用户内容):**
```
.claude/skills/brainstorm/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/team-level/SKILL.md
.claude/skills/team-narrative/SKILL.md
.claude/skills/design-system/SKILL.md
.claude/docs/workflow-catalog.yaml
README.md
UPGRADING.md
```

### 文件: 需仔细合并

无 -- 所有变更都是基础设施文件，没有用户内容。

---

## v1.0.0-beta → v1.0

**发布日期:** 2026-05-13
**提交范围:** `49d1e45..HEAD`
**关键主题:** 新 `/vertical-slice` 门控、skill 打磨与 bug 修复、贡献者文档

### 变更内容

| 类别 | 变更 |
|----------|---------|
| **新 skill** | `/vertical-slice` -- Pre-Production 门控，在生产前用生产质量的端到端构建验证完整游戏循环。与重构后的 `/prototype` (在 `/brainstorm` 之后立即进行概念验证) 配合使用。 |
| **新流程** | `/map-systems` 中的实体清单步骤 -- 提前展示所有命名实体，以便下游更简洁地编写 GDD。 |
| **UX 打磨** | 为 7 个 skill 添加了缺失的 `AskUserQuestion` widget；全面 skill 审计确保一致性、提示和流程完整性；在所有 `team-*` skill 的 `argument-hints` 中暴露了 `--review` 标志。 |
| **Bug 修复** | `#21` log-agent hook 记录了 "unknown" `agent_type`；`#36` `/architecture-decision` 和 `/story-done` 中缺失 `allowed-tools`；`#42` `rg --type gdscript` 无效 (现在使用 `--glob *.gd`)；`#43` session-start 预览显示最旧状态而非最新；`#45` `/architecture-decision` 中重复的 `## 0.` 标题和错误的步骤编号。 |
| **项目文档** | 添加了 `CONTRIBUTING.md` (框架贡献指南) 和 `SECURITY.md` (协调披露政策)。 |
| **计数/引用** | 同步了 `WORKFLOW-GUIDE.md`、`README.md` 和 agent 名册中的 agent/skill/hook 计数；修复了过时的 agent 名称和 skill model-tier 字段。 |

---

### 文件: 可安全覆盖

**需要添加的新文件:**
```
.claude/skills/vertical-slice/SKILL.md
CONTRIBUTING.md
SECURITY.md
```

**需要覆盖的现有文件 (无用户内容):**
- 提交范围内修改的所有 `.claude/skills/` 下的文件 (skill 审计 + AskUserQuestion widget + `--review` argument-hints)
- `.claude/hooks/log-agent.sh` (修复 #21)
- `README.md`、`docs/WORKFLOW-GUIDE.md`、`docs/examples/skill-flow-diagrams.md`
- `UPGRADING.md`

---

### 文件: 需仔细合并

无 -- 所有变更都是基础设施文件，没有用户内容。

---

## v0.4.x → v1.0

**发布日期:** 2026-03-29
**提交范围:** `6c041ac..HEAD`
**关键主题:** 总监门控系统、门控强度模式、Godot C# 专家

### 变更内容

| 类别 | 变更 |
|----------|---------|
| **新系统** | 总监门控 -- 命名审查检查点，在所有工作流 skill 之间共享。在 `.claude/docs/director-gates.md` 中定义 |
| **新功能** | 门控强度模式: `full` (所有总监门控)、`lean` (仅阶段门控)、`solo` (无总监)。在 `/start` 期间通过 `production/review-mode.txt` 全局设置，或在任何使用门控的 skill 上通过 `--review [mode]` 每次运行覆盖 |
| **新 agent** | `godot-csharp-specialist` -- Godot 4 项目中的 C# 代码质量 |
| **Skill 更新 (13 个)** | 所有使用门控的 skill 现在解析 `--review [full|lean|solo]` 并在 argument-hint 中包含它: `brainstorm`、`map-systems`、`design-system`、`architecture-decision`、`create-architecture`、`create-epics`、`create-stories`、`sprint-plan`、`milestone-review`、`playtest-report`、`prototype`、`story-done`、`gate-check` |
| **`/start` 更新** | 添加了阶段 3b -- 在入门期间设置审查模式，写入 `production/review-mode.txt` |
| **`/setup-engine` 更新** | Godot 的语言选择步骤 (GDScript 与 C#) |
| **文档** | `director-gates.md` -- 完整门控目录；`WORKFLOW-GUIDE.md` -- 总监审查模式章节；`README.md` -- 审查强度自定义 |

---

### 文件: 可安全覆盖

**需要添加的新文件:**
```
.claude/agents/godot-csharp-specialist.md
.claude/docs/director-gates.md
```

**需要覆盖的现有文件 (无用户内容):**
```
.claude/skills/brainstorm/SKILL.md
.claude/skills/map-systems/SKILL.md
.claude/skills/design-system/SKILL.md
.claude/skills/architecture-decision/SKILL.md
.claude/skills/create-architecture/SKILL.md
.claude/skills/create-epics/SKILL.md
.claude/skills/create-stories/SKILL.md
.claude/skills/sprint-plan/SKILL.md
.claude/skills/milestone-review/SKILL.md
.claude/skills/playtest-report/SKILL.md
.claude/skills/prototype/SKILL.md
.claude/skills/story-done/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/start/SKILL.md
.claude/skills/quick-design/SKILL.md
.claude/skills/setup-engine/SKILL.md
README.md
docs/WORKFLOW-GUIDE.md
UPGRADING.md
```

---

### 文件: 需仔细合并

此版本没有需要手动合并的文件。所有变更都是基础设施文件，没有用户内容。

---

### 新功能

#### 总监门控系统

所有主要工作流 skill 现在引用在 `.claude/docs/director-gates.md` 中定义的命名门控检查点。门控通过域前缀和名称标识 (例如 `CD-CONCEPT`、`TD-ARCHITECTURE`、`LP-CODE-REVIEW`)。每个门控定义要生成哪个总监、传递什么输入、裁决意味着什么以及 lean/solo 模式如何影响它。

Skill 使用 `Task` 以门控 ID 和文档化输入生成门控，而不是将总监提示内联嵌入。这保持了 skill 主体的干净，并使门控行为在所有工作流阶段保持一致。

#### 门控强度模式

三种模式让你控制获得多少总监评审:

- **`full`** (默认) -- 所有总监门控在每个评审检查点运行
- **`lean`** -- 跳过每个 skill 的总监评审；`/gate-check` 的阶段门控仍然运行
- **`solo`** -- 任何地方都没有总监门控；`/gate-check` 仅检查制品是否存在

在 `/start` 期间全局设置 (写入 `production/review-mode.txt`)。在任何使用门控的 skill 上使用 `--review [mode]` 覆盖单次运行:

```
/design-system combat --review lean
/gate-check concept --review full
/brainstorm my-game-idea --review solo
```

---

### 升级后

1. 运行 `/start` 一次以设置您偏好的审查模式 -- 或手动创建 `production/review-mode.txt`，内容为 `full`、`lean` 或 `solo`。
2. 如果您正在进行中项目，请查看 `.claude/docs/director-gates.md` 以了解哪些门控适用于您当前的阶段。
3. 运行 `/skill-test static all` 以验证所有 skill 通过结构检查。

---

## v0.4.0 → v0.4.1

**发布日期:** 2026-03-26
**提交范围:** `04ed5d5..HEAD`
**关键主题:** 类型无关的 agent、新 skill、skill 修复

### 变更内容

| 类别 | 变更 |
|----------|---------|
| **新 skill (1 个)** | `/consistency-check` -- 跨 GDD 实体一致性扫描器 |
| **Skill 修复 (所有 team-\*)** | 添加了无参数守卫、正式的 `Verdict: COMPLETE / BLOCKED` 关键词、每步 AskUserQuestion 门控、相邻区域依赖检查 (team-level)、伦理执行 (team-live-ops)、带阶段跳过的 NO-GO 路径 (team-release) |
| **Agent 修复 (4 个)** | game-designer、systems-designer、economy-designer、live-ops-designer 中的类型无关语言 -- 移除了 RPG 特定术语 |

---

### 文件: 可安全覆盖

**需要添加的新文件:**
```
.claude/skills/consistency-check/SKILL.md
```

**需要覆盖的现有文件 (无用户内容):**
```
.claude/skills/team-combat/SKILL.md      ← 无参数守卫、裁决关键词、门控改进
.claude/skills/team-narrative/SKILL.md   ← 无参数守卫、裁决关键词、门控改进
.claude/skills/team-ui/SKILL.md          ← 无参数守卫、裁决关键词、门控改进
.claude/skills/team-release/SKILL.md     ← 无参数守卫、裁决关键词、NO-GO 路径
.claude/skills/team-polish/SKILL.md      ← 无参数守卫、裁决关键词、门控改进
.claude/skills/team-audio/SKILL.md       ← 无参数守卫、裁决关键词、门控改进
.claude/skills/team-level/SKILL.md       ← 无参数守卫、裁决关键词、相邻区域检查
.claude/skills/team-live-ops/SKILL.md    ← 无参数守卫、裁决关键词、伦理执行
.claude/skills/team-qa/SKILL.md          ← 无参数守卫、裁决关键词、门控改进
.claude/skills/map-systems/SKILL.md      ← 裁决关键词
.claude/skills/create-epics/SKILL.md     ← "May I write" 协议修复、裁决关键词
.claude/skills/create-stories/SKILL.md   ← 裁决关键词
.claude/agents/game-designer.md          ← 类型无关语言
.claude/agents/systems-designer.md       ← 类型无关语言
.claude/agents/economy-designer.md       ← 类型无关语言
.claude/agents/live-ops-designer.md      ← 类型无关语言
```

---

### 文件: 需仔细合并

此版本没有需要手动合并的文件。所有变更都是基础设施文件，没有用户内容。

---

### 升级后

1. 运行 `/skill-test catalog` 以验证所有 skill 已建立索引。
2. 在任何 skill 编辑后运行 `/skill-test lint [skill-name]` 以检查结构合规性。
3. 如果您自定义了任何 team-* skill，请查看更新后的版本 -- 所有 team-* skill 现在都需要无参数守卫和 `Verdict:` 关键词。

---

## v0.3.0 → v0.4.0

**发布日期:** 2026-03-21
**提交范围:** `b1cad29..HEAD`
**关键主题:** 完整 UX/UI 管道、完整 story 生命周期、棕地采用、全面 QA/测试框架、管道完整性、29 个新 skill

### 变更内容

| 类别 | 变更 |
|----------|---------|
| **新 skill (17 个)** | `/ux-design`、`/ux-review`、`/help`、`/quick-design`、`/review-all-gdds`、`/story-readiness`、`/story-done`、`/sprint-status`、`/adopt`、`/create-architecture`、`/create-control-manifest`、`/create-epics`、`/create-stories`、`/dev-story`、`/propagate-design-change`、`/content-audit`、`/architecture-review` |
| **新 skill QA (12 个)** | `/qa-plan`、`/smoke-check`、`/soak-test`、`/regression-suite`、`/test-setup`、`/test-helpers`、`/test-evidence-review`、`/test-flakiness`、`/skill-test`、`/bug-triage`、`/team-live-ops`、`/team-qa` |
| **新 hook (4 个)** | `log-agent-stop.sh` -- agent 审计追踪停止；`notify.sh` -- Windows 通知；`post-compact.sh` -- 压缩后会话恢复提醒；`validate-skill-change.sh` -- 在 skill 编辑后建议运行 `/skill-test` |
| **新模板 (8 个)** | `ux-spec.md`、`hud-design.md`、`accessibility-requirements.md`、`interaction-pattern-library.md`、`player-journey.md`、`difficulty-curve.md` 以及 2 个采用计划模板 |
| **新基础设施** | `workflow-catalog.yaml` (7 阶段管道，由 `/help` 读取)、`docs/architecture/tr-registry.yaml` (稳定 TR-ID)、`production/sprint-status.yaml` schema |
| **Skill 更新** | `/gate-check` -- 3 个门控现在需要 UX 制品；Pre-Production 门控需要 vertical slice (硬门控) |
| **Skill 更新** | `/sprint-plan` -- 写入 `sprint-status.yaml`；`/sprint-status` 读取它 |
| **Skill 更新** | `/story-done` -- 8 阶段完成评审，更新 story 文件，展示下一个就绪的 story |
| **Skill 更新** | `/design-review` -- 移除了架构差距检查 (错误阶段) |
| **Skill 更新** | `/team-ui` -- 完整 UX 管道 (ux-design → ux-review → team phases) |
| **Agent 更新** | 14 个专家 agent -- 添加了 `memory: project` |
| **Agent 更新** | `prototyper` -- `isolation: worktree` (在隔离的 git 分支中进行一次性工作) |
| **模型路由** | Haiku/Sonnet/Opus 层级分配在协调规则中记录；skill 在其 frontmatter 中声明其层级 |
| **目录 CLAUDE.md** | 搭建了 `design/CLAUDE.md`、`src/CLAUDE.md`、`docs/CLAUDE.md` -- 每个目录的路径作用域指令 |
| **管道完整性** | TR-ID 稳定性、manifest 版本控制、ADR 状态门控、TR-ID 引用而非引用 |
| **GDD 模板** | 添加了 `## Game Feel` 章节 (输入响应、动画目标、冲击时刻) |

---

### 文件: 可安全覆盖

**需要添加的新文件:**
```
.claude/skills/ux-design/SKILL.md
.claude/skills/ux-review/SKILL.md
.claude/skills/help/SKILL.md
.claude/skills/quick-design/SKILL.md
.claude/skills/review-all-gdds/SKILL.md
.claude/skills/story-readiness/SKILL.md
.claude/skills/story-done/SKILL.md
.claude/skills/sprint-status/SKILL.md
.claude/skills/adopt/SKILL.md
.claude/skills/create-architecture/SKILL.md
.claude/skills/create-control-manifest/SKILL.md
.claude/skills/create-epics/SKILL.md
.claude/skills/create-stories/SKILL.md
.claude/skills/dev-story/SKILL.md
.claude/skills/propagate-design-change/SKILL.md
.claude/skills/content-audit/SKILL.md
.claude/skills/architecture-review/SKILL.md
.claude/skills/qa-plan/SKILL.md
.claude/skills/smoke-check/SKILL.md
.claude/skills/soak-test/SKILL.md
.claude/skills/regression-suite/SKILL.md
.claude/skills/test-setup/SKILL.md
.claude/skills/test-helpers/SKILL.md
.claude/skills/test-evidence-review/SKILL.md
.claude/skills/test-flakiness/SKILL.md
.claude/skills/skill-test/SKILL.md
.claude/skills/bug-triage/SKILL.md
.claude/skills/team-live-ops/SKILL.md
.claude/skills/team-qa/SKILL.md
.claude/hooks/log-agent-stop.sh
.claude/hooks/notify.sh
.claude/hooks/post-compact.sh
.claude/hooks/validate-skill-change.sh
.claude/docs/workflow-catalog.yaml
.claude/docs/templates/ux-spec.md
.claude/docs/templates/hud-design.md
.claude/docs/templates/accessibility-requirements.md
.claude/docs/templates/interaction-pattern-library.md
.claude/docs/templates/player-journey.md
.claude/docs/templates/difficulty-curve.md
design/CLAUDE.md
src/CLAUDE.md
docs/CLAUDE.md
```

**需要覆盖的现有文件 (无用户内容):**
```
.claude/skills/gate-check/SKILL.md
.claude/skills/sprint-plan/SKILL.md
.claude/skills/sprint-status/SKILL.md
.claude/skills/design-review/SKILL.md
.claude/skills/team-ui/SKILL.md
.claude/skills/story-readiness/SKILL.md
.claude/skills/story-done/SKILL.md
.claude/docs/templates/game-design-document.md    ← 添加了 Game Feel 章节
README.md
docs/WORKFLOW-GUIDE.md
UPGRADING.md
```

**需要覆盖的 Agent 文件** (如果你没有写入自定义提示到其中):
```
.claude/agents/prototyper.md         ← 添加了 isolation: worktree
.claude/agents/art-director.md       ← 添加了 memory: project
.claude/agents/audio-director.md     ← 添加了 memory: project
.claude/agents/economy-designer.md   ← 添加了 memory: project
.claude/agents/game-designer.md      ← 添加了 memory: project
.claude/agents/gameplay-programmer.md ← 添加了 memory: project
.claude/agents/lead-programmer.md    ← 添加了 memory: project
.claude/agents/level-designer.md     ← 添加了 memory: project
.claude/agents/narrative-director.md ← 添加了 memory: project
.claude/agents/systems-designer.md   ← 添加了 memory: project
.claude/agents/technical-artist.md   ← 添加了 memory: project
.claude/agents/ui-programmer.md      ← 添加了 memory: project
.claude/agents/ux-designer.md        ← 添加了 memory: project
.claude/agents/world-builder.md      ← 添加了 memory: project
```

---

### 文件: 需仔细合并

#### `.claude/settings.json`

此版本注册了四个新 hook。如果你没有自定义 `settings.json`，覆盖是安全的。否则，手动添加以下 hook 条目:

- `log-agent-stop.sh` -- `SubagentStop` 事件 (agent 审计追踪停止)
- `notify.sh` -- `Notification` 事件 (Windows 通知)
- `post-compact.sh` -- `PostCompact` 事件 (会话恢复提醒)
- `validate-skill-change.sh` -- `PostToolUse` 事件，过滤到 `.claude/skills/` 写入

#### 自定义的 Agent 文件

如果你在 agent `.md` 文件中添加了项目特定知识，请进行 diff 并手动在适当的 YAML frontmatter 中添加 `memory: project` 行。创意和技术总监 agent 有意保持 `memory: user` -- 只有专家 agent 获得 `memory: project`。

---

### 新功能

#### 完整 Story 生命周期

Story 现在拥有由两个 skill 强制执行的正式生命周期:

- **`/story-readiness`** -- 验证 story 在开发者接手前已达到实现就绪状态。检查 Design (GDD req 链接)、Architecture (ADR 已接受)、Scope (标准可测试) 和 DoD (manifest 版本当前)。裁决: READY / NEEDS WORK / BLOCKED。
- **`/story-done`** -- 实现后的 8 阶段完成评审。验证每个验收标准、检查 GDD/ADR 偏差、提示代码评审、将 story 文件更新为 `Status: Complete`，并展示下一个就绪的 story。

流程: `/story-readiness` → 实现 → `/story-done` → 下一个 story

#### 完整 UX/UI 管道

- **`/ux-design`** -- 引导式逐章节 UX 规范创作。三种模式: 屏幕/流程、HUD 或交互模式库。读取 GDD UI 需求和玩家旅程。输出到 `design/ux/`。
- **`/ux-review`** -- 根据 GDD 一致性、无障碍层级和模式库验证 UX 规范。裁决: APPROVED / NEEDS REVISION / MAJOR REVISION。
- **`/team-ui` 更新**: 阶段 1 现在在视觉设计开始前运行 `/ux-design` + `/ux-review` 作为硬门控。

#### 棕地采用

**`/adopt`** 将现有项目纳入模板格式。审计 GDD、ADR、story、systems-index 和基础设施的内部结构。对缺口进行分类 (BLOCKING/HIGH/MEDIUM/LOW)。构建有序的迁移计划。从不重新生成现有制品 -- 只填补缺口。

参数模式: `full | gdds | adrs | stories | infra`

还有: `/design-system retrofit [path]` 和 `/architecture-decision retrofit [path]` 检测现有文件并仅添加缺失的章节。

#### Sprint 跟踪 YAML

`production/sprint-status.yaml` 现在是权威的 story 跟踪格式:
- 由 `/sprint-plan` 写入 (初始化所有 story) 和 `/story-done` (将状态设置为 `done`)
- 由 `/sprint-status` (快速快照) 和 `/help` (生产阶段中每个 story 的状态) 读取
- 状态值: `backlog | ready-for-dev | in-progress | review | done | blocked`
- 如果文件不存在，优雅地回退到 markdown 扫描

#### `/help` -- 上下文感知的下一步

`/help` 读取你当前的阶段和进行中的工作，检查哪些制品已完成，并准确告诉你下一步该做什么 -- 一个主要必需步骤，加上可选机会。与 `/start` (仅首次) 和 `/project-stage-detect` (完整审计) 不同。

#### 全面 QA 和测试框架

九个新的 QA/测试 skill 涵盖完整测试生命周期:

- **`/test-setup`** -- 为你的引擎搭建测试框架和 CI/CD 管道
- **`/test-helpers`** -- 生成引擎特定的测试辅助库 (GDUnit4、NUnit 等)
- **`/qa-plan`** -- 为 sprint 或功能生成 QA 测试计划，按测试类型对 story 分类
- **`/smoke-check`** -- 在 QA 交接前运行关键路径冒烟测试门控
- **`/soak-test`** -- 为长时间游戏会话生成浸泡测试协议 (稳定性、内存泄漏)
- **`/regression-suite`** -- 将测试覆盖率映射到 GDD 关键路径，识别缺少回归测试的已修复 bug
- **`/test-evidence-review`** -- 测试文件和手动证据文档的质量审查
- **`/test-flakiness`** -- 通过读取 CI 运行日志检测非确定性测试
- **`/skill-test`** -- 验证 skill 文件的结构合规性和行为正确性 (三种模式: lint、spec、catalog)

还有新功能: **`/bug-triage`** 重新评估所有未解决 bug 的优先级、严重性和归属。

#### Skill 验证器 (`/skill-test`)

`/skill-test` 是一个用于验证框架本身的元 skill。在任何 skill 文件编辑后运行它。三种模式:
- `lint` -- 验证 YAML frontmatter 和必需字段
- `spec [skill-name]` -- 针对特定 skill 运行行为规范测试
- `catalog` -- 检查 `.claude/skills/` 中的所有 skill 是否已在目录中建立索引

新的 `validate-skill-change.sh` hook 会在 skill 文件被修改时自动提醒你运行 `/skill-test`。

#### Team Live-Ops 和 Team QA 编排

- **`/team-live-ops`** -- 协调 live-ops-designer + economy-designer + community-manager + analytics-engineer 进行发布后内容规划 (季节性活动、战斗通行证、留存)
- **`/team-qa`** -- 编排 qa-lead + qa-tester + gameplay-programmer + producer 完成完整 QA 周期: 策略、执行、覆盖和签字

#### 模型层级路由

Skill 现在根据任务复杂度明确分配到 Haiku、Sonnet 或 Opus 层级。只读状态检查使用 Haiku；复杂的多文档综合使用 Opus；其他所有情况默认为 Sonnet。层级分配在 `.claude/docs/coordination-rules.md` 中记录。

#### 目录 CLAUDE.md 文件

三个新的目录作用域 CLAUDE.md 文件 (`design/`、`src/`、`docs/`) 为在这些目录中工作的 agent 提供路径特定的指令。当 Claude Code 读取该目录中的文件时，这些指令会自动加载。

---

### 升级后

1. **验证新 hook** 已在 `.claude/settings.json` 中注册 -- 检查全部四个: `log-agent-stop.sh`、`notify.sh`、`post-compact.sh`、`validate-skill-change.sh`。

2. **测试审计追踪** 通过生成任何子 agent -- 开始和停止事件都应该出现在 `production/session-logs/` 中。

3. **生成 sprint-status.yaml** 如果你处于活跃的生产阶段:
   ```
   /sprint-plan status
   ```

4. **运行 `/adopt`** 如果你有早于此模板版本的现有 GDD 或 ADR -- 它将识别需要添加哪些章节，而不会覆盖你的内容。

5. **在任何 skill 编辑后使用 `/skill-test` 验证你的 skill** -- 新的 `validate-skill-change.sh` hook 会自动提醒你这样做。

---

## v0.2.0 → v0.3.0

**发布日期:** 2026-03-09
**提交范围:** `e289ce9..HEAD`
**关键主题:** `/design-system` GDD 创作、`/map-systems` 重命名、自定义状态行

### 破坏性变更

#### `/design-systems` 重命名为 `/map-systems`

`/design-systems` skill 已重命名为 `/map-systems` 以更清晰 (分解 = *映射*，而不是 *设计*)。

**需要操作:** 更新所有调用 `/design-systems` 的文档、笔记或脚本。新的调用方式是 `/map-systems`。

### 变更内容

| 类别 | 变更 |
|----------|---------|
| **新 skill** | `/design-system` (引导式 GDD 创作，逐章节) |
| **已重命名 skill** | `/design-systems` → `/map-systems` (破坏性重命名) |
| **新文件** | `.claude/statusline.sh`、`.claude/settings.json` statusline 配置 |
| **Skill 更新** | `/gate-check` -- 在 PASS 时写入 `production/stage.txt`，新的阶段定义 |
| **Skill 更新** | `brainstorm`、`start`、`design-review`、`project-stage-detect`、`setup-engine` -- 交叉引用修复 |
| **Bug 修复** | `log-agent.sh`、`validate-commit.sh` -- hook 执行修复 |
| **文档** | 添加了 `UPGRADING.md`，更新了 `README.md`，更新了 `WORKFLOW-GUIDE.md` |

---

### 文件: 可安全覆盖

**需要添加的新文件:**
```
.claude/skills/design-system/SKILL.md
.claude/statusline.sh
```

**需要覆盖的现有文件 (无用户内容):**
```
.claude/skills/map-systems/SKILL.md      ← 原为 design-systems/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/brainstorm/SKILL.md
.claude/skills/start/SKILL.md
.claude/skills/design-review/SKILL.md
.claude/skills/project-stage-detect/SKILL.md
.claude/skills/setup-engine/SKILL.md
.claude/hooks/log-agent.sh
.claude/hooks/validate-commit.sh
README.md
docs/WORKFLOW-GUIDE.md
UPGRADING.md
```

**删除 (已被重命名替换):**
```
.claude/skills/design-systems/   ← 整个目录；已替换为 map-systems/
```

---

### 文件: 需仔细合并

#### `.claude/settings.json`

新版本添加了一个指向 `.claude/statusline.sh` 的 `statusLine` 配置块。如果你没有自定义 `settings.json`，覆盖是安全的。否则，手动添加此块:

```json
"statusLine": {
  "script": ".claude/statusline.sh"
}
```

---

### 新功能

#### 自定义状态行

`.claude/statusline.sh` 在终端状态行中显示 7 阶段生产管道面包屑:

```
ctx: 42% | claude-sonnet-4-6 | Systems Design
```

在生产/打磨/发布阶段，如果存在 `<!-- STATUS -->` 块，它还会显示来自 `production/session-state/active.md` 的活动 Epic/Feature/Task:

```
ctx: 42% | claude-sonnet-4-6 | Production | Combat System > Melee Combat > Hitboxes
```

当前阶段从项目制品中自动检测，也可以通过将阶段名称写入 `production/stage.txt` 来固定。

#### `/gate-check` 阶段推进

当门控 PASS 裁决确认后，`/gate-check` 现在将新阶段名称写入 `production/stage.txt`。这立即更新所有未来会话的状态行，无需手动编辑文件。

---

### 升级后

1. **删除旧的 skill 目录:**
   ```bash
   rm -rf .claude/skills/design-systems/
   ```

2. **测试状态行** 通过启动 Claude Code 会话 -- 你应该在终端底部看到阶段面包屑。

3. **验证 hook 执行** 仍然有效:
   ```bash
   bash .claude/hooks/log-agent.sh '{}' '{}'
   bash .claude/hooks/validate-commit.sh '{}' '{}'
   ```

---

## v0.1.0 → v0.2.0

**发布日期:** 2026-02-21
**提交范围:** `ad540fe..e289ce9`
**关键主题:** 上下文韧性、AskUserQuestion 集成、`/map-systems` skill

### 变更内容

| 类别 | 变更 |
|----------|---------|
| **新 skill** | `/start` (入门)、`/map-systems` (系统分解)、`/design-system` (引导式 GDD 创作) |
| **新 hook** | `session-start.sh` (恢复)、`detect-gaps.sh` (缺口检测) |
| **新模板** | `systems-index.md`、3 个协作协议模板 |
| **上下文管理** | 重大重写 -- 添加了文件支持状态策略 |
| **Agent 更新** | 14 个设计/创意 agent -- AskUserQuestion 集成 |
| **Skill 更新** | 所有 7 个 `team-*` skill + `brainstorm` -- 阶段转换时的 AskUserQuestion |
| **CLAUDE.md** | 从约 159 行精简到约 60 行；5 个文档导入代替了 10 个 |
| **Hook 更新** | 所有 8 个 hook -- Windows 兼容性修复、新功能 |
| **移除了文档** | `docs/IMPROVEMENTS-PROPOSAL.md`、`docs/MULTI-STAGE-DOCUMENT-WORKFLOW.md` |

---

### 文件: 可安全覆盖

这些是纯基础设施 -- 你没有自定义它们。直接复制新版本，不会对你的项目内容造成风险。

**需要添加的新文件:**
```
.claude/skills/start/SKILL.md
.claude/skills/map-systems/SKILL.md
.claude/skills/design-system/SKILL.md
.claude/docs/templates/systems-index.md
.claude/docs/templates/collaborative-protocols/design-agent-protocol.md
.claude/docs/templates/collaborative-protocols/implementation-agent-protocol.md
.claude/docs/templates/collaborative-protocols/leadership-agent-protocol.md
.claude/hooks/detect-gaps.sh
.claude/hooks/session-start.sh
production/session-state/.gitkeep
docs/examples/README.md
.github/ISSUE_TEMPLATE/bug_report.md
.github/ISSUE_TEMPLATE/feature_request.md
.github/PULL_REQUEST_TEMPLATE.md
```

**需要覆盖的现有文件 (无用户内容):**
```
.claude/skills/brainstorm/SKILL.md
.claude/skills/design-review/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/project-stage-detect/SKILL.md
.claude/skills/setup-engine/SKILL.md
.claude/skills/team-audio/SKILL.md
.claude/skills/team-combat/SKILL.md
.claude/skills/team-level/SKILL.md
.claude/skills/team-narrative/SKILL.md
.claude/skills/team-polish/SKILL.md
.claude/skills/team-release/SKILL.md
.claude/skills/team-ui/SKILL.md
.claude/hooks/log-agent.sh
.claude/hooks/pre-compact.sh
.claude/hooks/session-stop.sh
.claude/hooks/validate-assets.sh
.claude/hooks/validate-commit.sh
.claude/hooks/validate-push.sh
.claude/rules/design-docs.md
.claude/docs/hooks-reference.md
.claude/docs/skills-reference.md
.claude/docs/quick-start.md
.claude/docs/directory-structure.md
.claude/docs/context-management.md
docs/COLLABORATIVE-DESIGN-PRINCIPLE.md
docs/WORKFLOW-GUIDE.md
README.md
```

**需要覆盖的 Agent 文件** (如果你没有写入自定义提示到其中):
```
.claude/agents/art-director.md
.claude/agents/audio-director.md
.claude/agents/creative-director.md
.claude/agents/economy-designer.md
.claude/agents/game-designer.md
.claude/agents/level-designer.md
.claude/agents/live-ops-designer.md
.claude/agents/narrative-director.md
.claude/agents/producer.md
.claude/agents/systems-designer.md
.claude/agents/technical-director.md
.claude/agents/ux-designer.md
.claude/agents/world-builder.md
.claude/agents/writer.md
```

如果你 *已* 自定义了 agent 提示，请参见下面的"需仔细合并"。

---

### 文件: 需仔细合并

这些文件既包含模板结构又包含你的项目特定内容。
请 **不要** 覆盖它们 -- 手动合并变更。

#### `CLAUDE.md`

模板版本从约 159 行精简到约 60 行。关键结构变更: 移除了 5 个文档导入，因为它们反正会被 Claude Code 自动加载 (agent-roster、skills-reference、hooks-reference、rules-reference、review-workflow)。

**保留你版本中的内容:**
- `## Technology Stack` 章节 (你的引擎/语言选择)
- 你添加的任何项目特定内容

**采用新版本中的内容:**
- 更精简的导入列表 (如果存在，删除 5 个冗余的 `@` 导入)
- 更新的协作协议措辞

#### `.claude/docs/technical-preferences.md`

如果你运行了 `/setup-engine`，此文件包含你的引擎配置、命名约定和性能预算。全部保留。模板版本只是空占位符。

#### `.claude/docs/templates/game-concept.md`

轻微结构更新 -- 添加了指向 `/map-systems` 的 `## Next Steps` 章节。如果你想获得更新的指导，请将该章节添加到你的副本中，但不是必需的。

#### `.claude/settings.json`

检查新版本是否添加了你想要的任何权限规则。变更很小 (schema 更新)。如果你没有自定义 `settings.json`，覆盖是安全的。

#### 自定义的 Agent 文件

如果你在任何 agent `.md` 文件中添加了项目特定知识或自定义行为，请进行 diff 并手动添加新的 AskUserQuestion 集成章节，而不是覆盖。每个 agent 的变更是在系统提示末尾添加标准化的协作协议块。

---

### 文件: 删除

这些文件在 v0.2.0 中被移除。如果它们存在于你的仓库中，你可以安全地删除 -- 它们已被组织得更好的替代方案所取代。

```
docs/IMPROVEMENTS-PROPOSAL.md      → 已被 WORKFLOW-GUIDE.md 取代
docs/MULTI-STAGE-DOCUMENT-WORKFLOW.md → 内容已合并到 context-management.md
```

---

### 升级后

1. **运行 `/project-stage-detect`** 以验证系统使用新的检测逻辑正确读取你的项目。

2. **如果你还没有使用过 `/start`，运行一次** -- 它现在能正确识别你的阶段并跳过你已经完成的入门步骤。

3. **检查 `production/session-state/`** 存在并已被 gitignore:
   ```bash
   ls production/session-state/
   cat .gitignore | grep session-state
   ```

4. **测试 hook 执行** -- 如果你在 Windows 上，请在 Git Bash 中验证新 hook 运行时没有错误:
   ```bash
   bash .claude/hooks/detect-gaps.sh '{}' '{}'
   bash .claude/hooks/session-start.sh '{}' '{}'
   ```

---

*每个未来版本都将在此文件中拥有自己的章节。*
