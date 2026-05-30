---
name: localize
description: "完整本地化流水线：扫描硬编码字符串、提取和管理字符串表、验证翻译、生成翻译人员简报、运行文化/敏感度审查、管理 VO 本地化、测试 RTL/平台要求、强制执行字符串冻结和报告覆盖情况。"
argument-hint: "[scan|extract|validate|status|brief|cultural-review|vo-pipeline|rtl-check|freeze|qa]"
user-invocable: true
agent: localization-lead
allowed-tools: Read, Glob, Grep, Write, Bash, Task, AskUserQuestion
model: sonnet
---
