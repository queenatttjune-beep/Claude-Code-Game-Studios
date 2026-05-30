# 为 Claude Code Game Studios 做贡献

CCGS 是一个使用 Claude Code 进行独立游戏开发的协调框架。
欢迎贡献 -- 错误修复、填补实际空白的新 skill、agent
改进以及 hook 修复。不符合框架方向的 PR
将被关闭，恕不详细解释。

## 什么样的 PR 是好的

- **错误修复** -- 某功能出问题了，这是修复方法
- **新 skill**，解决尚未覆盖的工作流空白
- **对现有 agent、skill 或 hook 的改进**
- **文档修正** -- 错误信息、损坏的引用、过时的步骤

作为 PR 提交的功能请求将被关闭。请改为提交 issue。

**这个 repo 不是什么:**
CCGS 是帮助你构建游戏的系统，而不是存储你用
它构建的游戏的地方。GDD、ADR、PRD、游戏概念、关卡设计、叙事
文档或 CCGS 为你的项目生成的任何其他输出都不会
合并到这里 -- 请将这些保留在你自己的 repo 中。

## 不可谈判的技术规则

以下是如果你遗漏了会导致 PR 被拒绝的事项。

**Skill 文件**
- Skill 位于 `.claude/skills/<name>/SKILL.md` -- 子目录格式是
  必需的。Claude Code 会静默忽略平面 `.md` 文件。
- SKILL.md 必须包含 YAML frontmatter: `name`、`description`、
  `argument-hint`、`allowed-tools` 和 `model`
- 模型层级: `haiku` 用于只读状态检查，`opus` 用于多文档
  综合和阶段门控，`sonnet` 用于其他所有情况

**Hook**
- 使用 `grep -E` -- 永远不要使用 `grep -P` (Perl 正则表达式在 Windows Git Bash 上会出错)
- 为没有安装 `jq` 或 `python` 的系统包含后备方案
- Hook 在每次会话启动时运行 -- 它们必须快速优雅地退出
  (不适用时 `exit 0`)

**Agent**
- 新 agent 必须包含 **协作协议** 部分，描述
  agent 如何提问以及如何将决策推迟给用户
- 未经明确用户授权，agent 不得修改其记录域之外的文件

**参考文档**
- 如果你的 PR 添加或更改了 skill、agent 或 hook，请更新相应的
  参考文档 (agent-roster、skills-reference、hooks-reference 或
  rules-reference)。添加内容而不更新索引的 PR
  将被退回。

## 协作原则

CCGS 不是一个自主系统。每个工作流遵循:
**提问 -> 选项 -> 决策 -> 草稿 -> 批准 -> 写入**

Skill 和 agent 必须在行动前询问。未经明确的用户确认，
不得写入文件。如果你的贡献让 agent 单方面做出决策
或写入文件，将不会被合并。

## 测试你的更改

在 Claude Code 会话中运行并确认其端到端工作。对于 skill，
调用该 skill 并验证输出是否与 skill 声称的功能匹配。
对于 hook，触发相关事件并确认 hook 正确触发
并干净退出。

在你的 PR 描述中包含简短的说明，描述你测试了什么
以及输出看起来如何。

## 提交格式

使用 [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: 添加 /retrospective skill 用于 sprint 结束回顾
fix: 修正 session-start hook 中 grep -P 的使用
docs: 用新的 /qa-plan 条目更新 skills-reference
```

类型: `feat`、`fix`、`docs`、`chore`、`refactor`、`test`

## PR 流程

- 你的 PR 将通过 CODEOWNERS 自动分配给维护者
- 审核会在适当的时候进行 -- 这是一个单人维护的项目
- 如果你的 PR 在几周内保持开放且没有反馈，提醒评论是可以的
- 被合并的贡献者会在发布说明中得到致谢

## 平台兼容性

CCGS 必须在 Windows (Git Bash)、macOS 和 Linux 上工作。如果你的 hook 或
脚本使用了任何平台特定的内容，将被拒绝。如有疑问，
请在 Windows 上测试。
