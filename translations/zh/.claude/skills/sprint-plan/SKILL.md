---
name: sprint-plan
description: "基于当前里程碑、已完成工作和可用容量，生成新的 sprint 计划或更新现有计划。从生产文档和设计积压中提取上下文。"
argument-hint: "[new|update|status] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
---
