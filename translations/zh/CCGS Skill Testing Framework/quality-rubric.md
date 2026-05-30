# Skill 质量评分标准

由 `/skill-test category [name|all]` 使用，用于评估 skill 超出结构合规性的质量。
每个类别定义了 4-5 个特定于该 skill 工作的二元 PASS/FAIL 指标。

当 skill 的书面指令明确满足标准时为 PASS。
当指令缺失、模糊或矛盾时为 FAIL。
当指令仅部分满足标准时为 WARN。

---

## Skill 类别

### `gate`

**Skills**: gate-check

Gate skill 控制阶段转换。它们必须在不自动推进阶段的前提下强制执行正确性，并且必须尊重三种 review 模式。

| 指标 | PASS 标准 |
|---|---|
| **G1 — Review 模式读取** | Skill 在决定启动哪些 director 之前，读取 `production/session-state/review-mode.txt`（或等效文件） |
| **G2 — Full 模式：所有 4 个 director 启动** | 在 `full` 模式下，所有 4 个 Tier-1 director（CD, TD, PR, AD）的 PHASE-GATE 提示并行调用 |
| **G3 — Lean 模式：仅 PHASE-GATE** | 在 `lean` 模式下，仅运行 `*-PHASE-GATE` 关卡；内联关卡（CD-PILLARS, TD-ARCHITECTURE 等）被跳过 |
| **G4 — Solo 模式：无 director** | 在 `solo` 模式下，不启动任何 director gate；每个都被标注为"已跳过 — Solo 模式" |
| **G5 — 不自动推进** | Skill 在未经用户通过"May I write"明确确认的情况下，从不写入 `production/stage.txt` |

---

### `review`

**Skills**: design-review, architecture-review, review-all-gdds

Review skill 读取文档并产生结构化裁决。它们主要是只读的，不得在分析阶段触发 director gate。

| 指标 | PASS 标准 |
|---|---|
| **R1 — 只读执行** | Skill 未经用户明确批准不得修改被审阅的文档；任何写入操作（review 日志、索引更新）都必须通过"May I write"门控 |
| **R2 — 8 章节检查** | Skill 明确评估所有 8 个必需的 GDD 章节（或等效的架构章节） |
| **R3 — 正确裁决词汇** | 裁决必须是以下之一：APPROVED / NEEDS REVISION / MAJOR REVISION NEEDED（设计类）或 PASS / CONCERNS / FAIL（架构类） |
| **R4 — 分析期间无 director gate** | Skill 在分析阶段不启动 director gate；分析后的 director 审阅（如 architecture-review 中的）在 skill 的范围和重要性证明合理时是可接受的 |
| **R5 — 结构化发现** | 输出在最终裁决之前包含每个章节的状态表或清单 |

> **例外情况：**
> - `design-review`: 在 allowed-tools 中有 `Write, Edit`，用于支持可选的"立即修订"路径（所有写入都需用户批准门控）以及写入 review 日志。R1 已满足，因为被审阅的文档从未被静默修改。
> - `architecture-review`: 在其分析完成后启动 TD-ARCHITECTURE 和 LP-FEASIBILITY gates。这是有意为之——架构审阅风险高，需要 director 签字。R4 已满足，因为这些 gate 在分析之后而非期间运行。

---

### `authoring`

**Skills**: design-system, quick-design, architecture-decision, ux-design, ux-review, art-bible, create-architecture

Authoring skill 协作创建或更新设计文档。完整的 GDD/UX authoring skill 使用逐章节循环；轻量级 authoring skill 使用适合其较小范围的单次起草模式。

| 指标 | PASS 标准 |
|---|---|
| **A1 — 逐章节循环** | 完整的 authoring skill（design-system, ux-design, art-bible）一次编写一个章节，在继续下一个之前展示内容供批准。轻量级 skill（quick-design, architecture-decision, create-architecture）可以起草完整文档然后请求批准——对于大约 4 小时实施范围以内的文档，单次起草是可接受的。 |
| **A2 — 每个章节 May-I-write** | 完整的 authoring skill 在每次章节写入前询问"May I write this to [filepath]?"。轻量级 skill 为整个文档询问一次。 |
| **A3 — 改装模式** | Skill 检测目标文件是否已存在，并提供更新特定章节而非覆盖整个文档。始终创建新文件的轻量级 skill（quick-design）可豁免。 |
| **A4 — 在正确层级运行 director gate** | 如果为此 skill 定义了 director gate（例如 CD-GDD-ALIGN, TD-ADR），它在正确的模式阈值（full/lean）下运行——不在 solo 模式下 |
| **A5 — 先建骨架** | 完整的 authoring skill 在填充内容之前创建包含所有章节标题的文件骨架，以便在会话中断时保留进度。轻量级 skill 可豁免。 |

> **完整 authoring skills**（必须通过所有 5 个指标）：`design-system`, `ux-design`, `art-bible`
> **轻量级 authoring skills**（A1, A2, A5 使用单次起草模式；A3 对新建文件类 skill 豁免）：`quick-design`, `architecture-decision`, `create-architecture`
> **Review 模式 skill**（按 review 指标评估）：`ux-review`

---

### `readiness`

**Skills**: story-readiness, story-done

Readiness skill 在实施之前或之后验证 story。它们必须产生多维度裁决，并正确与 director gate 模式集成。

| 指标 | PASS 标准 |
|---|---|
| **RD1 — 多维检查** | Skill 检查 ≥3 个独立维度（例如设计、架构、范围、DoD），并分别报告每个维度 |
| **RD2 — 三级裁决** | 裁决层级清晰定义：READY/COMPLETE > NEEDS WORK/COMPLETE WITH NOTES > BLOCKED |
| **RD3 — BLOCKED 需要外部行动** | BLOCKED 裁决保留给 story 作者无法自行解决的问题（例如待定 ADR、不可解决的依赖） |
| **RD4 — 在正确模式运行 director gate** | QL-STORY-READY 或 LP-CODE-REVIEW gate 在 `full` 模式下启动，在 `lean`/`solo` 模式下跳过并显示注释跳跃消息 |
| **RD5 — 下一个 story 交接** | 完成后，skill 从当前 sprint 中呈现下一个 READY 状态的 story |

---

### `pipeline`

**Skills**: create-epics, create-stories, dev-story, create-control-manifest, propagate-design-change, map-systems

Pipeline skill 产生供其他 skill 使用的工件。它们必须使用正确的 schema 写入文件，尊重层级/优先级排序，并在写入前进行门控。

| 指标 | PASS 标准 |
|---|---|
| **P1 — 正确的输出 schema** | 每个生成的文件遵循项目模板（EPIC.md, story frontmatter 等）；skill 引用模板路径 |
| **P2 — 层级/优先级排序** | 生成 epic 或 story 的 skill 尊重层级排序（core → extended → meta）和优先级字段 |
| **P3 — 每个工件前 May-I-write** | Skill 在创建每个输出文件前询问"May I write [artifact]?"，而不是一次性批量批准所有文件 |
| **P4 — 在正确层级运行 director gate** | 范围内的 gate（PR-EPIC, QL-STORY-READY, LP-CODE-REVIEW 等）在 `full` 模式下运行，在 `lean`/`solo` 模式下跳过并注明 |
| **P5 — 先读后写** | Skill 在生成工件前读取相关的 GDD/ADR/manifest 以确保对齐 |

---

### `analysis`

**Skills**: consistency-check, balance-check, content-audit, code-review, tech-debt, scope-check, estimate, perf-profile, asset-audit, security-audit, test-evidence-review, test-flakiness

分析 skill 扫描项目并呈现发现。它们在分析期间是只读的，在推荐任何文件写入之前必须询问。

| 指标 | PASS 标准 |
|---|---|
| **AN1 — 只读扫描** | 分析阶段仅使用 Read/Glob/Grep 工具；扫描本身期间无 Write 或 Edit |
| **AN2 — 结构化发现表格** | 输出包含发现表格或清单（不仅仅是散文），每个发现带有严重/优先级 |
| **AN3 — 不自动写入** | 任何建议的文件写入（例如 tech-debt 注册表、修复补丁）都通过"May I write"门控 |
| **AN4 — 分析期间无 director gate** | 分析 skill 不启动 director gate；它们产生供人工审阅的发现 |

---

### `team`

**Skills**: team-combat, team-narrative, team-audio, team-level, team-ui, team-qa, team-release, team-polish, team-live-ops

Team skill 为某个部门编排多个 specialist agent。它们必须启动正确的 agent，并行运行独立的 agent，并立即暴露阻塞项。

| 指标 | PASS 标准 |
|---|---|
| **T1 — 具名 agent 列表** | Skill 明确命名它启动哪些 agent 以及顺序 |
| **T2 — 独立时并行** | 输入不相互依赖的 agent 被并行启动（单条消息，多个 Task 调用） |
| **T3 — BLOCKED 暴露** | 如果任何启动的 agent 返回 BLOCKED 或失败，skill 立即暴露它并停止依赖的工作——绝不静默跳过 |
| **T4 — 继续前收集所有裁决** | 依赖阶段等待所有并行 agent 完成后才继续 |
| **T5 — 无参数时显示使用错误** | 如果必需的参数（例如功能名称）缺失，skill 输出使用提示并停止，不启动 agent |

---

### `sprint`

**Skills**: sprint-plan, sprint-status, milestone-review, retrospective, changelog, patch-notes

Sprint skill 读取生产状态并生成报告或规划工件。它们在特定的模式阈值下有 PR-SPRINT 或 PR-MILESTONE gate。

| 指标 | PASS 标准 |
|---|---|
| **SP1 — 读取 sprint/milestone 状态** | Skill 在生成输出前读取 `production/sprints/` 或 `production/milestones/` |
| **SP2 — 正确的 sprint gate** | PR-SPRINT（用于规划）或 PR-MILESTONE（用于里程碑审阅）gate 在 `full` 模式下运行，在 `lean`/`solo` 模式下跳过 |
| **SP3 — 结构化输出** | 输出使用一致的结构（速度表、风险列表、行动项）而非自由散文 |
| **SP4 — 不自动提交** | Skill 在无"May I write"的情况下从不写入 sprint 文件或里程碑记录 |

---

### `utility`

**Skills**: start, help, brainstorm, onboard, adopt, hotfix, prototype, localize, launch-checklist, release-checklist, smoke-check, soak-test, test-setup, test-helpers, regression-suite, qa-plan, bug-triage, bug-report, playtest-report, asset-spec, reverse-document, project-stage-detect, setup-engine, skill-test, skill-improve, day-one-patch, 以及其他不在以上类别中的 skill

Utility skill 通过 7 项标准静态检查。如果它们碰巧启动 director gate，gate 模式逻辑也必须正确。

| 指标 | PASS 标准 |
|---|---|
| **U1 — 通过所有 7 项静态检查** | `/skill-test static [name]` 返回 COMPLIANT，0 个 FAIL |
| **U2 — Gate 模式正确（如适用）** | 如果 skill 启动任何 director gate，它会读取 review-mode 并正确应用 full/lean/solo 逻辑 |

---

## Agent 类别

用于验证 `tests/agents/` 中的 agent spec 文件。

### `director`

**Agents**: creative-director, technical-director, art-director, producer

| 指标 | PASS 标准 |
|---|---|
| **D1 — 正确裁决词汇** | 返回 APPROVE / CONCERNS / REJECT（或领域等效：producer 为 REALISTIC/CONCERNS/UNREALISTIC） |
| **D2 — 领域边界受尊重** | 不在其声明的领域之外做有约束力的决定 |
| **D3 — 冲突升级** | 当两个部门冲突时，升级到正确的父级（creative-director 或 technical-director），而非单方面决定 |
| **D4 — Opus 模型层级** | 根据 coordination-rules.md，agent 被分配 Opus 模型 |

### `lead`

**Agents**: lead-programmer, qa-lead, narrative-director, audio-director, game-designer, systems-designer, level-designer

| 指标 | PASS 标准 |
|---|---|
| **L1 — 领域裁决** | 返回领域特定的裁决（例如 lead-programmer 的 FEASIBLE/INFEASIBLE, qa-lead 的 PASS/FAIL） |
| **L2 — 升级到共享父级** | 领域外冲突升级到 creative-director（设计）或 technical-director（技术） |
| **L3 — Sonnet 模型层级** | 根据 coordination-rules.md，agent 被分配 Sonnet 模型（默认） |

### `specialist`

**Agents**: gameplay-programmer, ai-programmer, technical-artist, sound-designer, engine-programmer, tools-programmer, network-programmer, security-engineer, accessibility-specialist, ux-designer, ui-programmer, performance-analyst, prototyper, qa-tester, writer, world-builder

| 指标 | PASS 标准 |
|---|---|
| **S1 — 留在领域内** | 明确将其范围限定在声明的领域内；推迟领域外的请求 |
| **S2 — 不做有约束力的跨领域决策** | 不单方面决定属于另一个 specialist 的事项 |
| **S3 — 正确推迟** | 领域外的请求重定向到正确的 agent，而非静默拒绝 |

### `engine`

**Agents**: godot-specialist, godot-gdscript-specialist, godot-csharp-specialist, godot-shader-specialist, godot-gdextension-specialist, unity-specialist, unity-ui-specialist, unity-shader-specialist, unity-dots-specialist, unity-addressables-specialist, unreal-specialist, ue-blueprint-specialist, ue-gas-specialist, ue-umg-specialist, ue-replication-specialist

| 指标 | PASS 标准 |
|---|---|
| **E1 — 版本感知** | 在建议 API 调用前，引用 `docs/engine-reference/` 中的引擎版本；标记 cutoff 后风险 |
| **E2 — 文件路由** | 将文件类型路由到正确的子 specialist（例如 `.gdshader` → godot-shader-specialist, 而非 godot-gdscript-specialist） |
| **E3 — 引擎特定模式** | 执行引擎特定的惯用写法（例如 GDScript 静态类型、C# 属性导出、Blueprint 函数库） |

### `qa`

**Agents**: qa-tester, qa-lead, security-engineer, accessibility-specialist

| 指标 | PASS 标准 |
|---|---|
| **Q1 — 产生工件而非代码** | 主要输出是测试用例、bug 报告或覆盖缺口——而非实现代码 |
| **Q2 — 证据格式** | 测试用例遵循项目的测试证据格式（根据 coding-standards.md 的 unit/integration/visual/UI） |
| **Q3 — 无范围蔓延** | 不提议新功能；标记缺口供人类决定 |

### `operations`

**Agents**: devops-engineer, release-manager, live-ops-designer, community-manager, analytics-engineer, economy-designer, localization-lead

| 指标 | PASS 标准 |
|---|---|
| **O1 — 领域所有权清晰** | Agent 描述清晰地说明其拥有什么（pipeline、发布、经济系统等） |
| **O2 — 推迟实施** | 不编写游戏逻辑或引擎代码；委托给适当的 specialist |
| **O3 — 工具集匹配角色** | frontmatter 中的 `allowed-tools` 匹配角色的运营（而非编码）性质 |
