---
name: create-architecture
description: "引导式的、逐部分编写游戏主架构文档。读取所有 GDD、系统索引、现有 ADR 和引擎参考库，在编写任何代码之前生成完整的架构蓝图。引擎版本感知：标记知识差距并针对固定引擎版本验证决策。"
argument-hint: "[focus-area: full | layers | data-flow | api-boundaries | adr-audit] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash, AskUserQuestion, Task
model: sonnet
agent: technical-director
---
