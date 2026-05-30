---
name: project-stage-detect
description: "自动分析项目状态、检测阶段、识别差距并基于现有工件推荐后续步骤。当用户问'where are we in development'、'what stage are we in'、'full project audit'时使用。"
argument-hint: "[optional: role filter like 'programmer' or 'designer']"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write
model: haiku
---
