---
name: smoke-check
description: "在 QA 交接前运行关键路径冒烟测试关卡。执行自动化测试套件，验证核心功能，生成 PASS/FAIL 报告。在 sprint 的故事实现后、手动 QA 开始前运行。冒烟检查失败意味着构建尚未准备好交给 QA。"
argument-hint: "[sprint | quick | --platform pc|console|mobile|all]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, AskUserQuestion
model: sonnet
---
