---
name: soak-test
description: "生成长时间游戏会话的浸泡测试协议。定义在长时间游戏过程中观察、测量和记录的内容，以发现缓慢泄漏、疲劳效应和仅在持续游戏后出现的边缘情况。主要在 Polish 和 Release 阶段使用。"
argument-hint: "[duration: 30m | 1h | 2h | 4h] [focus: memory | stability | balance | all]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---
