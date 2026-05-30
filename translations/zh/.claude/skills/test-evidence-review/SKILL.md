---
name: test-evidence-review
description: "对测试文件和手动证据文档进行质量审查。超越存在性检查——评估断言覆盖范围、边缘情况处理、命名约定和证据完整性。为每个故事生成 ADEQUATE/INCOMPLETE/MISSING 判定。在 QA 签署前或按需运行。"
argument-hint: "[story-path | sprint | system-name]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---
