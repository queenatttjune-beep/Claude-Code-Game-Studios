---
name: design-system
description: "引导式的、逐部分 GDD 编写，针对单个游戏系统。从现有文档收集上下文，协作式地完成每个必需部分，交叉引用依赖关系，并增量写入文件。"
argument-hint: "<system-name> [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion, TodoWrite
model: sonnet
---

当此 skill 被调用时：通过上下文收集、骨架创建和逐部分协作设计循环，引导用户完成完整的 GDD 编写过程。每个部分遵循：上下文 → 问题 → 选项 → 决策 → 草稿 → 批准 → 写入。委托给系统设计师、游戏设计师、QA 负责人和美术总监等专家 agent。
