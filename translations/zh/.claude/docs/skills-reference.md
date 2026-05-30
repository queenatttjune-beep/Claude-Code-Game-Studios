# 可用 Skill（斜杠命令）

73 个按阶段组织的斜杠命令。在 Claude Code 中输入 `/` 即可访问任意命令。

## 引导与导航

| 命令 | 用途 |
|---------|---------|
| `/start` | 首次引导 — 询问你当前状态，然后引导到正确的工作流程 |
| `/help` | 上下文感知的"我下一步该做什么？" — 读取当前阶段并展示所需的下一步 |
| `/project-stage-detect` | 完整项目审计 — 检测阶段、识别存在差距、推荐后续步骤 |
| `/setup-engine` | 配置引擎 + 版本、检测知识差距、填充版本感知的参考文档 |
| `/adopt` | 现有项目（Brownfield）格式审计 — 检查现有 GDD/ADR/story 的内部结构，生成迁移计划 |

## 游戏设计

| 命令 | 用途 |
|---------|---------|
| `/brainstorm` | 使用专业工作室方法（MDA、SDT、Bartle、动词优先）引导式构思 |
| `/map-systems` | 将游戏概念分解为系统、映射依赖关系、优先设计顺序 |
| `/design-system` | 针对单个游戏系统的逐个章节引导式 GDD 编写 |
| `/quick-design` | 小型更改的轻量级设计规格 — 调优、微调、小功能添加 |
| `/review-all-gdds` | 跨 GDD 一致性和游戏设计整体性审查 |
| `/propagate-design-change` | 当 GDD 被修订时，查找受影响的 ADR 并生成影响报告 |

## 美术与资源

| 命令 | 用途 |
|---------|---------|
| `/art-bible` | 逐个章节引导式 Art Bible 编写 — 在资源生产前创建视觉标识规格 |
| `/asset-spec` | 从 GDD、关卡文档或角色档案生成每个资源的视觉规格和 AI 生成提示 |
| `/asset-audit` | 审计资源的命名规范、文件大小预算和流水线合规性 |

## UX 与界面设计

| 命令 | 用途 |
|---------|---------|
| `/ux-design` | 逐个章节引导式 UX 规格编写（界面/流程、HUD 或模式库） |
| `/ux-review` | 验证 UX 规格的 GDD 对齐、无障碍性和模式合规性 |

## 架构

| 命令 | 用途 |
|---------|---------|
| `/create-architecture` | 引导式编写主架构文档 |
| `/architecture-decision` | 创建架构决策记录（ADR） |
| `/architecture-review` | 验证所有 ADR 的完整性、依赖顺序和 GDD 覆盖范围 |
| `/create-control-manifest` | 根据已接受的 ADR 生成扁平化的程序员规则表 |

## Story 与 Sprint

| 命令 | 用途 |
|---------|---------|
| `/create-epics` | 将 GDD + ADR 转换为史诗 — 每个架构模块一个 |
| `/create-stories` | 将单个史诗分解为可实现的 story 文件 |
| `/dev-story` | 读取并实现一个 story — 路由到正确的程序员 Agent |
| `/sprint-plan` | 生成或更新 sprint 计划；初始化 sprint-status.yaml |
| `/sprint-status` | 快速的 30 行 sprint 快照（读取 sprint-status.yaml） |
| `/story-readiness` | 验证 story 是否已准备好实现（READY/NEEDS WORK/BLOCKED） |
| `/story-done` | 8 阶段完成审查；更新 story 文件，展示下一个 story |
| `/estimate` | 包含复杂度、依赖关系和风险分解的结构化工作量估算 |

## 审查与分析

| 命令 | 用途 |
|---------|---------|
| `/design-review` | 审查游戏设计文档的完整性和一致性 |
| `/code-review` | 对文件或变更集进行架构代码审查 |
| `/balance-check` | 分析游戏平衡数据、公式和配置 — 标记异常值 |
| `/content-audit` | 审计 GDD 指定内容数量与已实现内容的对比 |
| `/scope-check` | 分析功能或 sprint 范围与原始计划对比，标记范围蔓延 |
| `/perf-profile` | 带有瓶颈识别的结构化性能分析 |
| `/tech-debt` | 扫描、跟踪、优先排序和报告技术债务 |
| `/gate-check` | 验证在开发阶段之间推进的就绪性（PASS/CONCERNS/FAIL） |
| `/consistency-check` | 扫描所有 GDD 与实体注册表以发现跨文档不一致（相互矛盾的数据、名称、规则） |
| `/security-audit` | 审计游戏安全漏洞：存档篡改、作弊向量、网络攻击、数据泄露和输入验证缺口 |

## QA 与测试

| 命令 | 用途 |
|---------|---------|
| `/qa-plan` | 为 sprint 或功能生成 QA 测试计划 |
| `/smoke-check` | 在 QA 移交前运行关键路径冒烟测试关卡 |
| `/soak-test` | 为长时间游戏会话生成浸泡测试协议 |
| `/regression-suite` | 将测试覆盖映射到 GDD 关键路径，识别缺少回归测试的已修复 bug |
| `/test-setup` | 为项目引擎搭建测试框架和 CI/CD 流水线 |
| `/test-helpers` | 为测试套件生成引擎特定的测试辅助库 |
| `/test-evidence-review` | 测试文件和手动证据文档的质量审查 |
| `/test-flakiness` | 从 CI 运行日志检测非确定性（不稳定）测试 |
| `/skill-test` | 验证 skill 文件的结构合规性和行为正确性 |
| `/skill-improve` | 使用测试-修复-重测循环改进 skill — 诊断、提出修复方案、重写、验证 |

## 生产

| 命令 | 用途 |
|---------|---------|
| `/milestone-review` | 审查里程碑进度并生成状态报告 |
| `/retrospective` | 运行结构化的 sprint 或里程碑回顾 |
| `/bug-report` | 创建结构化的 Bug 报告 |
| `/bug-triage` | 读取所有开放的 bug，重新评估优先级与严重性，分配所有者和标签 |
| `/reverse-document` | 从现有实现生成设计或架构文档 |
| `/playtest-report` | 生成结构化的玩法测试报告或分析现有的玩法测试记录 |

## 发布

| 命令 | 用途 |
|---------|---------|
| `/release-checklist` | 为当前构建生成并验证发布前检查清单 |
| `/launch-checklist` | 跨所有部门的完整发布就绪验证 |
| `/changelog` | 从 git 提交和 sprint 数据自动生成更新日志 |
| `/patch-notes` | 从 git 历史与内部数据生成面向玩家的补丁说明 |
| `/hotfix` | 带有审计跟踪的紧急修复工作流，绕过正常 sprint 流程 |
| `/day-one-patch` | 为 Gold Master 之后、公开上线之前发现的已知问题准备首日补丁 |

## 创意与内容

| 命令 | 用途 |
|---------|---------|
| `/prototype` | 概念原型 — 头脑风暴后立即构建一次性原型以验证核心创意（阶段 1） |
| `/vertical-slice` | 预制作验证 — 在进入制作阶段前构建生产质量的端到端构建（阶段 4） |
| `/onboard` | 为新贡献者或 Agent 生成上下文相关的入职文档 |
| `/localize` | 本地化工作流：字符串提取、验证、翻译就绪性 |

## 团队编排

协调多个 Agent 处理单个功能领域：

| 命令 | 协调对象 |
|---------|-------------|
| `/team-combat` | game-designer + gameplay-programmer + ai-programmer + technical-artist + sound-designer + qa-tester |
| `/team-narrative` | narrative-director + writer + world-builder + level-designer |
| `/team-ui` | ux-designer + ui-programmer + art-director + accessibility-specialist |
| `/team-release` | release-manager + qa-lead + devops-engineer + producer |
| `/team-polish` | performance-analyst + technical-artist + sound-designer + qa-tester |
| `/team-audio` | audio-director + sound-designer + technical-artist + gameplay-programmer |
| `/team-level` | level-designer + narrative-director + world-builder + art-director + systems-designer + qa-tester |
| `/team-live-ops` | live-ops-designer + economy-designer + community-manager + analytics-engineer |
| `/team-qa` | qa-lead + qa-tester + gameplay-programmer + producer |
