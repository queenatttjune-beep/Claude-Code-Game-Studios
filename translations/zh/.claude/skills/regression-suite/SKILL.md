---
name: regression-suite
description: "将测试覆盖范围映射到 GDD 关键路径，识别没有回归测试的已修复错误，标记新功能的覆盖漂移，维护 tests/regression-suite.md。在实现错误修复后或发布关卡前运行。"
argument-hint: "[update | audit | report]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion
model: sonnet
---
