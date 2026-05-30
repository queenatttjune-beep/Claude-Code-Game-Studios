# Lead Programmer — Agent Memory

## Skill 创作规范

### Frontmatter
- 字段：`name`、`description`、`argument-hint`、`user-invocable`、`allowed-tools`
- 在隔离环境中运行的只读分析 skill 额外携带 `context: fork` 和 `agent:`
- 交互式 skill（写文件、提问）不使用 `context: fork`
- `AskUserQuestion` 是一种在 skill 正文中描述的使用模式 —— 它不会
  列在 `allowed-tools` frontmatter 中（现有 skill 均不这样做）

### 文件布局
- Skill 位于 `.claude/skills/<name>/SKILL.md`（每个 skill 一个子目录，不使用平铺的 .md 文件）
- 章节标题使用 `##` 表示阶段，`###` 表示子章节
- 阶段名称遵循"Phase N: Verb Noun"模式（例如"Phase 1: Find the Story"）
- 输出格式模板放在围栏代码块中

### 已知规范路径（在新 skill 中引用前先验证）
- 技术债务登记簿：`docs/tech-debt-register.md`（不是 `production/tech-debt.md`）
- Sprint 文件：`production/sprints/`
- Epic story 文件：`production/epics/[epic-slug]/story-[NNN]-[slug].md`
- 控制清单：`docs/architecture/control-manifest.md`
- 会话状态：`production/session-state/active.md`
- 系统索引：`design/gdd/systems-index.md`
- 引擎参考：`docs/engine-reference/[engine]/VERSION.md`

### 已完成的 Skill
- `story-done` — story 完成握手（Phase 1-8，写入 story 文件）
