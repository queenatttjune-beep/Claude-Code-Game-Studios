---
name: sprint-status
description: "快速 sprint 状态检查。读取当前 sprint 计划，扫描故事文件的状态，生成包含进度摘要、燃尽评估和新兴风险的简洁快照。在 sprint 期间的任何时间运行，以获得快速情境感知。当用户问'how is the sprint going'、'sprint update'、'show sprint progress'时使用。"
argument-hint: "[sprint-number or blank for current]"
user-invocable: true
allowed-tools: Read, Glob, Grep
model: haiku
---
