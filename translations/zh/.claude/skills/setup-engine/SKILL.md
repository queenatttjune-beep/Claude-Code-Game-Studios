---
name: setup-engine
description: "配置项目的游戏引擎和版本。在 CLAUDE.md 中固定引擎，检测知识差距，并在版本超出 LLM 训练数据时通过 WebSearch 填充引擎参考文档。"
argument-hint: "[engine] | [engine version] | refresh | upgrade [old-version] [new-version] | no args for guided selection"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, WebSearch, WebFetch, Task, AskUserQuestion
model: sonnet
---
