---
name: create-stories
description: "将单个 epic 分解为可实现的故事文件。读取 epic、其 GDD、管辖 ADR 和控制清单。每个故事嵌入其 GDD 需求 TR-ID、ADR 指南、验收标准、故事类型和测试证据路径。在每个 epic 的 /create-epics 后运行。"
argument-hint: "[epic-slug | epic-path] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
model: sonnet
agent: lead-programmer
---
