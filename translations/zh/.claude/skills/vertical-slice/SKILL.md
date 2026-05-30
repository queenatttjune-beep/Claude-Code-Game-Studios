---
name: vertical-slice
description: "Pre-Production 验证——构建一个生产质量的端到端构建，确认完整的游戏循环在投入 Production 之前是可实现的。在 GDD、架构和 UX 规范完成后运行。生成 PROCEED/PIVOT/KILL 判定，把关 Pre-Production 到 Production 的过渡。"
argument-hint: "[--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model: sonnet
agent: prototyper
isolation: worktree
---
