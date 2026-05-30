---
name: code-review
description: "对指定文件或文件集执行架构和质量代码审查。检查编码标准合规性、架构模式遵循情况、SOLID 原则、可测试性和性能问题。"
argument-hint: "[path-to-file-or-directory]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Task, AskUserQuestion
model: sonnet
agent: lead-programmer
---
