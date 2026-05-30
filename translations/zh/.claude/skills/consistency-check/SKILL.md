---
name: consistency-check
description: "扫描所有 GDD 与实体注册表的对比，检测跨文档不一致：同一实体不同属性、同一物品不同数值、同一公式不同变量。采用 Grep 优先方法——先读取注册表，然后仅针对冲突的 GDD 部分，而不是完整文档读取。"
argument-hint: "[full | since-last-review | entity:<name> | item:<name>]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, AskUserQuestion
model: sonnet
---
