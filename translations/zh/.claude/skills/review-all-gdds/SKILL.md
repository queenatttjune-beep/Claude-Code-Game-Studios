---
name: review-all-gdds
description: "整体跨 GDD 一致性和游戏设计审查。同时读取所有系统 GDD，检查它们之间的矛盾、过时引用、所有权冲突、公式不兼容和游戏设计理论违规（主导策略、经济不平衡、认知超载、支柱漂移）。在所有 MVP GDD 编写完成后、架构开始前运行。"
argument-hint: "[focus: full | consistency | design-theory | since-last-review]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash, AskUserQuestion, Task
model: opus
---
