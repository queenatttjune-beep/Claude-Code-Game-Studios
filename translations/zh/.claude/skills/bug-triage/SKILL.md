---
name: bug-triage
description: "读取 production/qa/bugs/ 中的所有开放错误，重新评估优先级与严重性，分配给 sprint，发现系统性问题趋势，生成分类报告。在 sprint 开始时或错误数量增长到需要重新优先排序时运行。"
argument-hint: "[sprint | full | trend]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit
model: sonnet
---
