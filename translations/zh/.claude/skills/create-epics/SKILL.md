---
name: create-epics
description: "将已批准的 GDD + 架构转化为 epics——每个架构模块一个 epic。定义范围、管辖 ADR、引擎风险和未追踪的需求。不分解为故事——在每个 epic 创建后运行 /create-stories [epic-slug]。"
argument-hint: "[system-name | layer: foundation|core|feature|presentation | all] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
model: sonnet
agent: technical-director
---
