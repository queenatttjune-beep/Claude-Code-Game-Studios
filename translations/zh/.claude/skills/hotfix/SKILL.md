---
name: hotfix
description: "绕过正常 sprint 流程的紧急修复工作流，带有完整的审计跟踪。创建 hotfix 分支，跟踪批准，确保修复正确回溯。"
argument-hint: "[bug-id or description]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model: sonnet
---
