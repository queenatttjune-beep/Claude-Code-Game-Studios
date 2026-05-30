---
name: dev-story
description: "读取故事文件并实现它。加载完整上下文（故事、GDD 需求、ADR 指南、控制清单），针对系统和引擎路由到正确的程序员 agent，实现代码和测试，确认每个验收标准。核心实现 skill——在 /story-readiness 之后、/code-review 和 /story-done 之前运行。"
argument-hint: "[story-path]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash, Task, AskUserQuestion
model: sonnet
---

# Dev Story

此 skill 桥接规划与代码。读取故事文件，组装程序员需要的所有上下文，路由到正确的专家 agent，驱动实现至完成——包括编写测试。
