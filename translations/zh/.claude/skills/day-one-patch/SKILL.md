---
name: day-one-patch
description: "为游戏发布准备首日补丁。确定范围、优先排序、实现和 QA 把关一个集中的补丁，解决在 gold master 之后但在公开发布之前或之后发现的已知问题。将补丁视为一个带有自己的 QA 关和回滚计划的迷你 sprint。"
argument-hint: "[scope: known-bugs | cert-feedback | all]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model: sonnet
---
