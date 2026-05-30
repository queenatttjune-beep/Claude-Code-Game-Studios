---
name: story-done
description: "故事完成的结束审查。读取故事文件，对照实现验证每个验收标准，检查 GDD/ADR 偏差，提示代码审查，将故事状态更新为 Complete，并从 sprint 中呈现下一个就绪的故事。"
argument-hint: "[story-file-path] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion, Task
model: sonnet
---
