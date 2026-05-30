---
name: balance-check
description: "分析游戏平衡数据文件、公式和配置，识别异常值、断裂的进度曲线、退化策略和经济不平衡。在修改任何与平衡相关的数据或设计后使用。当用户说'balance report'、'check game balance'、'run a balance check'时使用。"
argument-hint: "[system-name|path-to-data-file]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, AskUserQuestion
model: sonnet
agent: economy-designer
---

# 平衡检查

分析游戏数据以识别不平衡的领域。检查战斗、经济、进度或战利品平衡。
