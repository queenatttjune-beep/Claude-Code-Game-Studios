# CCGS Skill Testing Framework — Claude 指令

本文件夹是 Claude Code Game Studios skill/agent 框架的质量保证层。它是自包含的，与任何游戏项目分离。

## 关键文件

| 文件 | 用途 |
|------|---------|
| `catalog.yaml` | 所有 73 个 skill 和 49 个 agent 的主注册表。包含类别、spec 路径和最后测试跟踪字段。运行任何测试命令时，请始终先读取此文件。 |
| `quality-rubric.md` | 按类别划分的通过/失败指标。运行 `/skill-test category` 时，读取与 skill 类别匹配的 `###` 章节。 |
| `skills/[category]/[name].md` | skill 的行为规范 — 5 个测试用例 + 协议合规断言。 |
| `agents/[tier]/[name].md` | agent 的行为规范 — 5 个测试用例 + 协议合规断言。 |
| `templates/skill-test-spec.md` | 编写新 skill spec 文件的模板。 |
| `templates/agent-test-spec.md` | 编写新 agent spec 文件的模板。 |
| `results/` | 由 `/skill-test spec` 在保存结果时写入。已在 gitignore 中。 |

## 路径约定

- Skill specs: `CCGS Skill Testing Framework/skills/[category]/[name].md`
- Agent specs: `CCGS Skill Testing Framework/agents/[tier]/[name].md`
- 目录: `CCGS Skill Testing Framework/catalog.yaml`
- 评分标准: `CCGS Skill Testing Framework/quality-rubric.md`

`catalog.yaml` 中的 `spec:` 字段是每个 skill/agent spec 的权威路径。务必读取它而不是猜测路径。

## Skill 类别

```
gate        → gate-check
review      → design-review, architecture-review, review-all-gdds
authoring   → design-system, quick-design, architecture-decision, art-bible,
              create-architecture, ux-design, ux-review
readiness   → story-readiness, story-done
pipeline    → create-epics, create-stories, dev-story, create-control-manifest,
              propagate-design-change, map-systems
analysis    → consistency-check, balance-check, content-audit, code-review,
              tech-debt, scope-check, estimate, perf-profile, asset-audit,
              security-audit, test-evidence-review, test-flakiness
team        → team-combat, team-narrative, team-audio, team-level, team-ui,
              team-qa, team-release, team-polish, team-live-ops
sprint      → sprint-plan, sprint-status, milestone-review, retrospective,
              changelog, patch-notes
utility     → 其余所有 skill
```

## Agent 层级

```
directors   → creative-director, technical-director, producer, art-director
leads       → lead-programmer, narrative-director, audio-director, ux-designer,
              qa-lead, release-manager, localization-lead
specialists → gameplay-programmer, engine-programmer, ui-programmer,
              tools-programmer, network-programmer, ai-programmer,
              level-designer, sound-designer, technical-artist
godot       → godot-specialist, godot-gdscript-specialist, godot-csharp-specialist,
              godot-shader-specialist, godot-gdextension-specialist
unity       → unity-specialist, unity-ui-specialist, unity-shader-specialist,
              unity-dots-specialist, unity-addressables-specialist
unreal      → unreal-specialist, ue-gas-specialist, ue-replication-specialist,
              ue-umg-specialist, ue-blueprint-specialist
operations  → devops-engineer, security-engineer, performance-analyst,
              analytics-engineer, community-manager
creative    → writer, world-builder, game-designer, economy-designer,
              systems-designer, prototyper
```

## 测试 skill 的工作流程

1. 读取 `catalog.yaml` 获取 skill 的 `spec:` 路径和 `category:`
2. 读取 `.claude/skills/[name]/SKILL.md` 下的 skill
3. 读取 `spec:` 路径下的 spec 文件
4. 逐条评估断言
5. 主动提供将结果写入 `results/` 并更新 `catalog.yaml`

## 改进 skill 的工作流程

使用 `/skill-improve [name]`。它处理完整的循环：
测试 → 诊断 → 提出修复方案 → 重写 → 重新测试 → 保留或还原。

## Spec 有效性说明

本文件夹中的 spec 文件描述的是**当前行为**，而非理想行为。它们是通过读取 skill 编写的，因此可能包含 bug。当 skill 在实践中表现异常时，请先修正 skill，然后更新 spec 以匹配修复后的行为。将 spec 失败视为"需要调查"，而非"skill 绝对有问题"。

## 本文件夹可删除

`.claude/` 中的任何内容都不从此处导入。删除此文件夹对 CCGS 的 skill 或 agent 本身没有影响。`/skill-test` 和 `/skill-improve` 将报告 `catalog.yaml` 缺失，并引导用户初始化它。
