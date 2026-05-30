# CCGS Skill Testing Framework

**Claude Code Game Studios** 框架的质量保证基础设施。
测试的是 skill 和 agent 本身——而不是使用它们构建的任何游戏。

> **本文件夹是自包含且可选的。**
> 使用 CCGS 的游戏开发者不需要它。要完全删除：
> `rm -rf "CCGS Skill Testing Framework"` — `.claude/` 中没有依赖它。

---

## 内容说明

```
CCGS Skill Testing Framework/
├── README.md              ← 你在这里
├── CLAUDE.md              ← 告诉 Claude 如何使用本框架
├── catalog.yaml           ← 主注册表：所有 73 个 skill + 49 个 agent，覆盖追踪
├── quality-rubric.md      ← 按类别划分的通过/失败指标，用于 /skill-test category
│
├── skills/                ← skill 的行为规范文件（每个 skill 一个）
│   ├── gate/              ← gate 类别 specs
│   ├── review/            ← review 类别 specs
│   ├── authoring/         ← authoring 类别 specs
│   ├── readiness/         ← readiness 类别 specs
│   ├── pipeline/          ← pipeline 类别 specs
│   ├── analysis/          ← analysis 类别 specs
│   ├── team/              ← team 类别 specs
│   ├── sprint/            ← sprint 类别 specs
│   └── utility/           ← utility 类别 specs
│
├── agents/                ← agent 的行为规范文件（每个 agent 一个）
│   ├── directors/         ← creative-director, technical-director, producer, art-director
│   ├── leads/             ← lead-programmer, narrative-director, audio-director 等
│   ├── specialists/       ← 引擎/代码/着色器/UI 专家
│   ├── godot/             ← Godot 专用专家
│   ├── unity/             ← Unity 专用专家
│   ├── unreal/            ← Unreal 专用专家
│   ├── operations/        ← QA, live-ops, release, 本地化 等
│   └── creative/          ← writer, world-builder, game-designer 等
│
├── templates/             ← 用于编写新 spec 文件的模板
│   ├── skill-test-spec.md ← skill 行为 spec 模板
│   └── agent-test-spec.md ← agent 行为 spec 模板
│
└── results/               ← 测试运行输出（由 /skill-test spec 写入，已在 gitignore 中）
```

---

## 如何使用

所有测试由框架中已有的两个 skill 驱动：

### 检查结构合规性

```
/skill-test static [skill-name]     # 检查一个 skill（7 项检查）
/skill-test static all              # 检查所有 73 个 skill
```

### 运行行为 spec 测试

```
/skill-test spec gate-check         # 根据书面 spec 评估 skill
/skill-test spec design-review
```

### 按类别评分标准检查

```
/skill-test category gate-check     # 根据类别指标评估一个 skill
/skill-test category all            # 对所有分类 skill 运行评分标准检查
```

### 查看完整覆盖情况

```
/skill-test audit                   # Skills + agents：是否有 spec、最后测试时间、结果
```

### 改进失败的 skill

```
/skill-improve gate-check           # 测试 → 诊断 → 提出修复 → 重新测试循环
```

---

## Skill 类别

| 类别 | Skills | 关键指标 |
|----------|--------|-------------|
| `gate` | gate-check | Review 模式读取，full/lean/solo 导演面板，不自动推进 |
| `review` | design-review, architecture-review, review-all-gdds | 只读，8 章节检查，正确裁决 |
| `authoring` | design-system, quick-design, art-bible, create-architecture, ... | 逐章节 May-I-write，先建骨架 |
| `readiness` | story-readiness, story-done | 阻塞项暴露，full 模式下的导演 gate |
| `pipeline` | create-epics, create-stories, dev-story, map-systems, ... | 上游依赖检查，交接路径清晰 |
| `analysis` | consistency-check, balance-check, code-review, tech-debt, ... | 只读报告，裁决关键词，不写入 |
| `team` | team-combat, team-narrative, team-audio, ... | 启动所有必需 agent，暴露阻塞项 |
| `sprint` | sprint-plan, sprint-status, milestone-review, ... | 读取 sprint 数据，包含状态关键词 |
| `utility` | start, adopt, hotfix, localize, setup-engine, ... | 通过静态检查 |

---

## Agent 层级

| 层级 | Agents |
|------|--------|
| `directors` | creative-director, technical-director, producer, art-director |
| `leads` | lead-programmer, narrative-director, audio-director, ux-designer, qa-lead, release-manager, localization-lead |
| `specialists` | gameplay-programmer, engine-programmer, ui-programmer, tools-programmer, network-programmer, ai-programmer, level-designer, sound-designer, technical-artist |
| `godot` | godot-specialist, godot-gdscript-specialist, godot-csharp-specialist, godot-shader-specialist, godot-gdextension-specialist |
| `unity` | unity-specialist, unity-ui-specialist, unity-shader-specialist, unity-dots-specialist, unity-addressables-specialist |
| `unreal` | unreal-specialist, ue-gas-specialist, ue-replication-specialist, ue-umg-specialist, ue-blueprint-specialist |
| `operations` | devops-engineer, security-engineer, performance-analyst, analytics-engineer, community-manager |
| `creative` | writer, world-builder, game-designer, economy-designer, systems-designer, prototyper |

---

## 更新目录

`catalog.yaml` 追踪每个 skill 和 agent 的测试覆盖情况。运行测试后：

- `/skill-test spec [name]` 将提供更新 `last_spec` 和 `last_spec_result`
- `/skill-test category [name]` 将提供更新 `last_category` 和 `last_category_result`
- `last_static` 和 `last_static_result` 手动更新或通过 `/skill-improve` 更新

---

## 编写新 spec

1. 在 `templates/skill-test-spec.md` 找到 spec 模板
2. 复制到 `skills/[category]/[skill-name].md`
3. 更新 `catalog.yaml` 中的 `spec:` 字段指向新文件
4. 运行 `/skill-test spec [skill-name]` 验证

---

## 移除本框架

本文件夹与主项目无任何钩子。移除方式：

```bash
rm -rf "CCGS Skill Testing Framework"
```

skill `/skill-test` 和 `/skill-improve` 仍将正常运行——它们只会报告 `catalog.yaml` 缺失，并建议运行 `/skill-test audit` 来初始化它。
