---
name: skill-test
description: "验证技能文件的结构合规性和行为正确性。三种模式：static（linter）、spec（行为验证）、audit（覆盖报告）。"
argument-hint: "static [skill-name | all] | spec [skill-name] | category [skill-name | all] | audit"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---
