---
name: asset-spec
description: "从 GDD、关卡文档或角色档案生成每个资产的视觉规范和 AI 生成提示。生成结构化规范文件并更新主资产清单。在美术圣经和 GDD/关卡设计批准后、生产开始前运行。"
argument-hint: "[system:<name> | level:<name> | character:<name>] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
---
