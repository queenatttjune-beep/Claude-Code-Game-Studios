---
name: qa-plan
description: "为 sprint 或功能生成 QA 测试计划。读取 GDD 和故事文件，按测试类型（Logic/Integration/Visual/UI）分类故事，生成结构化的测试计划，涵盖所需的自动化测试、手动测试用例、烟雾测试范围和游戏测试签署要求。在 sprint 开始前或开始主要功能时运行。"
argument-hint: "[sprint | feature: system-name | story: path]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, AskUserQuestion
model: sonnet
agent: qa-lead
---
