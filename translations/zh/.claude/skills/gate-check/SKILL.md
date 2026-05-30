---
name: gate-check
description: "验证在不同开发阶段间推进的准备情况。生成 PASS/CONCERNS/FAIL 判定，包含具体阻塞项和必需工件。当用户说'are we ready to move to X'、'can we advance to production'、'check if we can start the next phase'、'pass the gate'时使用。"
argument-hint: "[target-phase: systems-design | technical-setup | pre-production | production | polish | release] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, Task, AskUserQuestion
model: opus
---
