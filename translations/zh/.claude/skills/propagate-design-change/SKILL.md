---
name: propagate-design-change
description: "当 GDD 被修订时，扫描所有 ADR 和可追溯性索引，识别哪些架构决策现在可能已过时。生成变更影响报告并引导用户解决。"
argument-hint: "[path/to/changed-gdd.md]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash, Task
model: sonnet
agent: technical-director
---
