---
name: test-helpers
description: "为项目的测试套件生成引擎特定的测试辅助库。读取现有测试模式，生成 tests/helpers/，包含断言工具、工厂函数和针对项目系统定制的模拟对象。减少新测试文件中的样板代码。"
argument-hint: "[system-name | all | scaffold]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---
