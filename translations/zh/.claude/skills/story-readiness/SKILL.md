---
name: story-readiness
description: "验证故事文件是否已准备好实现。检查嵌入的 GDD 需求、ADR 引用、引擎注释、清晰的验收标准和未解决的设计问题。生成 READY / NEEDS WORK / BLOCKED 判定及具体差距。当用户问'is this story ready'、'can I start on this story'、'is story X ready to implement'时使用。"
argument-hint: "[story-file-path or 'all' or 'sprint']"
user-invocable: true
allowed-tools: Read, Glob, Grep, AskUserQuestion, Task
model: sonnet
---
