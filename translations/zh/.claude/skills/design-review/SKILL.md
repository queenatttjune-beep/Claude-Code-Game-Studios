---
name: design-review
description: "审查游戏设计文档的完整性、内部一致性、可实现性和对项目设计标准的遵循程度。在将设计文档交给程序员之前运行此 skill。"
argument-hint: "[path-to-design-doc] [--depth full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
---

对设计文档进行完整性、一致性、可实现性和设计理论的多阶段审查。包括对抗性专家审查（full 模式）和创意总监综合判定。
