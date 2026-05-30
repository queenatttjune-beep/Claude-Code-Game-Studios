# Claude Code Game Studios -- 游戏工作室 Agent 架构

通过 49 个协调的 Claude Code 子 Agent 管理独立游戏开发。
每个 Agent 拥有特定领域，强制执行关注点分离和质量控制。

## 技术栈

- **引擎**: [选择: Godot 4 / Unity / Unreal Engine 5]
- **语言**: [选择: GDScript / C# / C++ / Blueprint]
- **版本控制**: 基于主干开发的 Git
- **构建系统**: [选择引擎后指定]
- **资源管道**: [选择引擎后指定]

> **注意**: 引擎专家 agent 适用于 Godot、Unity 和 Unreal，
> 并有专门的子专家。请使用与您的引擎匹配的集合。

## 项目结构

@.claude/docs/directory-structure.md

## 引擎版本参考

@docs/engine-reference/godot/VERSION.md

## 技术偏好

@.claude/docs/technical-preferences.md

## 协调规则

@.claude/docs/coordination-rules.md

## 协作协议

**用户驱动的协作，而非自主执行。**
每个任务遵循: **提问 -> 选项 -> 决策 -> 草稿 -> 批准**

- Agent 在使用 Write/Edit 工具前必须询问"我可以将此写入 [文件路径] 吗？"
- Agent 必须在请求批准前展示草稿或摘要
- 多文件变更需要完整变更集的明确批准
- 未经用户指示不得提交

完整协议和示例请参见 `docs/COLLABORATIVE-DESIGN-PRINCIPLE.md`。

> **首次会话？** 如果项目尚未配置引擎且没有游戏概念，
> 请运行 `/start` 开始引导式入门流程。

## 编码标准

@.claude/docs/coding-standards.md

## 上下文管理

@.claude/docs/context-management.md
