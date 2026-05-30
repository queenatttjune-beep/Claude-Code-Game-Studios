# Skill Spec: /[skill-name]

> **类别**: [gate | review | authoring | readiness | pipeline | analysis | team | sprint | utility]
> **优先级**: [critical | high | medium | low]
> **Spec 编写日期**: [YYYY-MM-DD]

## Skill 摘要

[一段描述此 skill 的作用、它接受什么输入以及产生什么输出。]

---

## 静态断言

这些应在任何行为测试前通过：

- [ ] Frontmatter 包含所有必填字段（`name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`）
- [ ] 找到 2 个以上阶段标题
- [ ] 至少存在一个裁决关键词（`PASS`, `FAIL`, `CONCERNS`, `APPROVED`, `BLOCKED`, `COMPLETE`, `READY`）
- [ ] 如果 `allowed-tools` 包含 Write/Edit：存在 `"May I write"` 语言
- [ ] 末尾存在下一步交接章节

---

## Director Gate 检查

[描述此 skill 触发哪些 director gate（如有），以及在何种 review 模式条件下。]

- **Full 模式**: [触发的 gates — 例如 CD-PHASE-GATE, TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE]
- **Lean 模式**: [仅阶段 gates — 例如仅 CD-PHASE-GATE，或无]
- **Solo 模式**: [无 gates — skill 在没有 director 审查的情况下运行]
- **不适用**: [如果此 skill 从不触发 gates，解释原因]

---

## 测试用例

### 用例 1：Happy Path — [简要名称]

**设置**（假设的项目状态）:
- [文件/条件 1]
- [文件/条件 2]

**预期行为**:
1. [步骤 1]
2. [步骤 2]
3. [步骤 3]

**断言**:
- [ ] [断言 1]
- [ ] [断言 2]
- [ ] [断言 3]

**用例裁决**: PASS / FAIL / PARTIAL

---

### 用例 2：失败 / 阻塞 — [简要名称]

**设置**:
- [缺失或无效的条件]

**预期行为**:
1. [Skill 检测问题]
2. [Skill 报告 FAIL/BLOCKED]
3. [Skill 不继续]

**断言**:
- [ ] Skill 提前停止且不产生输出
- [ ] 显示正确的错误/阻塞消息
- [ ] 未经用户批准未写入文件

**用例裁决**: PASS / FAIL / PARTIAL

---

### 用例 3：模式变体 — [简要名称]

**设置**:
- [标准项目状态]
- [特定模式或标记集]

**预期行为**:
1. [由于模式，行为与 Happy Path 不同]

**断言**:
- [ ] [模式特定断言]
- [ ] [输出与用例 1 正确不同]

**用例裁决**: PASS / FAIL / PARTIAL

---

### 用例 4：边界情况 — [简要名称]

**设置**:
- [不寻常或边界条件]

**预期行为**:
1. [Skill 优雅处理]

**断言**:
- [ ] [边界情况无崩溃或静默失败处理]
- [ ] [正确的输出或消息]

**用例裁决**: PASS / FAIL / PARTIAL

---

### 用例 5：Director Gate — [简要名称]

**设置**:
- [触发 gate 检查的项目状态]
- Review 模式: [full | lean | solo]

**预期行为**:
1. [基于模式的 Gate 触发/不触发]
2. [启动或跳过正确的 director agent]

**断言**:
- [ ] 在 full 模式下：[特定 gates 启动]
- [ ] 在 lean 模式下：[仅阶段 gates，或跳过]
- [ ] 在 solo 模式下：无 director gate 启动
- [ ] Skill 在 CONCERNS 或 FAIL 裁决后不自动推进

**用例裁决**: PASS / FAIL / PARTIAL

---

## 协议合规性

- [ ] 在任何文件写入前使用 `"May I write"`（或只读并跳过此项）
- [ ] 在请求批准前向用户呈现发现/草稿
- [ ] 以推荐下一步或后续行动结束
- [ ] 未经用户批准不自动创建文件

---

## 覆盖说明

[任何覆盖缺口、未测试的已知边界情况、或需要实时运行 skill 来验证的条件。]
