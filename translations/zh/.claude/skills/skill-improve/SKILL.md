---
name: skill-improve
description: "使用测试-修复-重测循环改进 skill。运行静态检查，提出目标修复，重写 skill，重新测试，根据分数变化决定保留或回滚。"
argument-hint: "[skill-name]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash
model: sonnet
---
