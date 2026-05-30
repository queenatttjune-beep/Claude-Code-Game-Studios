---
name: team-qa
description: "编排 QA 团队完成完整的测试周期。协调 qa-lead（策略 + 测试计划）和 qa-tester（测试用例编写 + 错误报告），为 sprint 或功能生成完整的 QA 包。涵盖：测试计划生成、测试用例编写、冒烟检查关卡、手动 QA 执行和签署报告。"
argument-hint: "[sprint | feature: system-name] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
model: sonnet
agent: qa-lead
---
