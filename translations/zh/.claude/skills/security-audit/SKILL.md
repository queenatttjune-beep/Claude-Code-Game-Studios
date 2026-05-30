---
name: security-audit
description: "审计游戏的安全漏洞：存档篡改、作弊向量、网络漏洞、数据暴露和输入验证缺口。生成带修复指导的优先安全报告。在任何公开发布或多人在线发布前运行。"
argument-hint: "[full | network | save | input | quick]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, Task
model: sonnet
agent: security-engineer
---
