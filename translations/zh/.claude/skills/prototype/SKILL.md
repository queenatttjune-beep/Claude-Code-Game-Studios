---
name: prototype
description: "概念原型——在编写 GDD 之前验证核心创意是否值得设计。在 /brainstorm 和 /setup-engine 之后运行。根据游戏类型路由到 HTML、引擎或纸面路径。生成可丢弃的构建和 PROCEED/PIVOT/KILL 判定。"
argument-hint: "[concept-description] [--path html|engine|paper] [--review full|lean|solo] [--spike]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model: sonnet
agent: prototyper
isolation: worktree
---
