# Agent Spec: [agent-name]

> **层级**: [directors | leads | specialists | godot | unity | unreal | operations | creative]
> **类别**: [director | lead | specialist | engine | operations | creative]
> **Spec 编写日期**: [YYYY-MM-DD]

## Agent 摘要

[一段描述此 agent 的领域、它拥有哪些决策权、以及它委托 vs. 直接处理哪些内容。包括它触发哪些 gate（如有）。]

**领域**: [此 agent 拥有的文件/目录]
**升级到**: [父级 agent — 例如设计冲突时到 creative-director]
**委托给**: [此 agent 通常启动的子 agent]

---

## 静态断言

- [ ] Agent 文件存在于 `.claude/agents/[name].md`
- [ ] Frontmatter 包含 `name`, `description`, `model`, `tools` 字段
- [ ] 领域明确说明
- [ ] 升级路径已记录
- [ ] 不在其领域外做决策

---

## 测试用例

### 用例 1：领域内请求 — [简要名称]

**场景**: 明显在此 agent 领域内的请求。

**设置**:
- [相关项目状态]
- [提供给 agent 的输入]

**预期行为**:
1. Agent 接受请求
2. Agent 生成 [特定输出类型]
3. Agent 在写入文件前询问（如适用）

**断言**:
- [ ] Agent 在其领域内处理请求而不升级
- [ ] 输出格式与预期结构匹配
- [ ] 遵循协作协议（询问 → 草稿 → 批准）

**用例裁决**: PASS / FAIL / PARTIAL

---

### 用例 2：领域外重定向 — [简要名称]

**场景**: 超出此 agent 领域的请求。

**设置**:
- [属于不同 agent 的请求]

**预期行为**:
1. Agent 识别请求超出领域
2. Agent 重定向到正确的 agent
3. Agent 不尝试自行处理

**断言**:
- [ ] Agent 拒绝并重定向（不静默处理跨领域工作）
- [ ] 重定向中命名了正确的 agent

**用例裁决**: PASS / FAIL / PARTIAL

---

### 用例 3：Gate 裁决 — [简要名称]

**场景**: Agent 作为 director gate 检查的一部分被调用。

**设置**:
- [提交审查的项目状态]
- [gate ID: 例如 CD-PHASE-GATE]

**预期行为**:
1. Agent 读取相关文档
2. Agent 生成 PASS / CONCERNS / FAIL 裁决
3. Agent 在 CONCERNS 或 FAIL 时不自动推进

**断言**:
- [ ] 输出中存在裁决关键词（PASS, CONCERNS, FAIL）
- [ ] 附有裁决的推理
- [ ] 在 CONCERNS/FAIL 时：工作被阻塞，而非静默继续

**用例裁决**: PASS / FAIL / PARTIAL

---

### 用例 4：冲突升级 — [简要名称]

**场景**: 此 agent 的领域与另一个 agent 的决策冲突。

**设置**:
- [同一层级两个 agent 的冲突决策]

**预期行为**:
1. Agent 识别冲突
2. Agent 升级到共享父级（或 creative-director / technical-director）
3. Agent 不单方面解决跨领域冲突

**断言**:
- [ ] 冲突明确暴露
- [ ] 遵循正确的升级路径
- [ ] 未做出单方面跨领域变更

**用例裁决**: PASS / FAIL / PARTIAL

---

### 用例 5：上下文传递 — [简要名称]

**场景**: Agent 从父级 agent 收到带完整上下文的任务。

**设置**:
- [从父级传递的上下文块]
- [要执行的特定子任务]

**预期行为**:
1. Agent 读取并使用提供的上下文
2. Agent 完成子任务
3. Agent 将结果返回给父级（不提示用户不必要的信息）

**断言**:
- [ ] Agent 使用提供的上下文而非重新要求
- [ ] 结果限定在子任务范围内，未扩大范围
- [ ] 输出格式适合父级 agent 消费

**用例裁决**: PASS / FAIL / PARTIAL

---

## 协议合规性

- [ ] 保持在声明领域内 — 无单方面跨领域变更
- [ ] 将冲突升级到正确的父级
- [ ] 在文件写入前使用 `"May I write"`（或是只读的）
- [ ] 在请求批准前呈现发现
- [ ] 不在委托层级中跳过层级

---

## 覆盖说明

[任何覆盖缺口、未测试的已知边界情况、或需要实时 agent 调用来验证的行为。]
