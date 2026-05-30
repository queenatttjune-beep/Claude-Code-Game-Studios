---
name: architecture-review
description: "验证项目架构对所有 GDD 的完整性和一致性。构建可追溯性矩阵，将每个 GDD 技术需求映射到 ADR，识别覆盖差距，检测跨 ADR 冲突，验证所有决策的引擎兼容性一致性，并生成 PASS/CONCERNS/FAIL 判定。是 /design-review 的架构对应物。"
argument-hint: "[focus: full | coverage | consistency | engine | single-gdd path/to/gdd.md]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
agent: technical-director
model: opus
---

# 架构审查

架构审查验证完整的架构决策体系是否覆盖所有游戏设计需求、内部一致且正确针对项目的固定引擎版本。它是 Technical Setup 和 Pre-Production 之间的质量关卡。

**参数模式：**
- **无参数 / `full`**：完整审查——所有阶段
- **`coverage`**：仅可追溯性——哪些 GDD 需求没有 ADR
- **`consistency`**：仅跨 ADR 冲突检测
- **`engine`**：仅引擎兼容性审计
- **`single-gdd [path]`**：审查一个特定 GDD 的架构覆盖范围
- **`rtm`**：需求可追溯性矩阵——扩展标准矩阵以包含故事文件路径和测试文件路径；输出完整的需求链
