---
name: test-flakiness
description: "通过读取 CI 运行日志或测试结果历史检测非确定性（不稳定）测试。汇总每个测试的通过率，识别间歇性失败，推荐隔离或修复，维护不稳定测试注册表。最好在 Polish 阶段或多次 CI 运行后运行。"
argument-hint: "[ci-log-path | scan | registry]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
model: sonnet
---
