---
name: architecture-decision
description: "创建架构决策记录（ADR），记录重要的技术决策及其上下文、考虑的备选方案和后果。每个重大技术选择都应有一个 ADR。"
argument-hint: "[title] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
---

当此 skill 被调用时：

## 0. 解析参数 — 检测改造模式

解析审查模式（一次性，存储用于本次运行的所有关卡生成）：
1. 如果传递了 `--review [full|lean|solo]` → 使用该模式
2. 否则读取 `production/review-mode.txt` → 使用该值
3. 否则 → 默认为 `lean`

参见 `.claude/docs/director-gates.md` 了解完整的检查模式。

**如果参数以 `retrofit` 开头，后跟文件路径**
（例如，`/architecture-decision retrofit docs/architecture/adr-0001-event-system.md`）：

进入**改造模式**：

1. 完整读取现有 ADR 文件。
2. 通过扫描标题识别哪些模板部分存在：
   - `## Status` — **如果缺失则为 BLOCKING**：`/story-readiness` 无法检查 ADR 验收状态
   - `## ADR Dependencies` — 如果缺失则为 HIGH：依赖排序中断
   - `## Engine Compatibility` — 如果缺失则为 HIGH：版本截止后风险未知
   - `## GDD Requirements Addressed` — 如果缺失则为 MEDIUM：可追溯性丢失
3. 向用户展示：
   ```
   ## 改造：[ADR 标题]
   文件：[path]

   已存在的部分（不会被触及）：
   ✓ Status：[当前值，或"MISSING — 将添加"]
   ✓ [部分]

   将添加的缺失部分：
   ✗ Status — BLOCKING（没有此字段，故事无法验证 ADR 验收状态）
   ✗ ADR Dependencies — HIGH
   ✗ Engine Compatibility — HIGH
   ```
4. 询问："要我添加 [N] 个缺失的部分吗？我不会修改任何现有内容。"
5. 如果是：
   - 对于 **Status**：询问用户——"此决策的当前状态是什么？"选项："Proposed"、"Accepted"、"Deprecated"、"Superseded by ADR-XXXX"
   - 对于 **ADR Dependencies**：询问——"此决策是否依赖于任何其他 ADR？它是否启用或阻塞任何其他 ADR 或 epic？"对每个字段接受"None"。
   - 对于 **Engine Compatibility**：读取引擎参考文档（与下面的步骤 1 相同）并要求用户确认域。然后使用已验证的数据生成表格。
   - 对于 **GDD Requirements Addressed**：询问——"哪些 GDD 系统促使了这个决策？此 ADR 解决了每个 GDD 中的哪些具体需求？"
   - 使用 Edit 工具将每个缺失的部分追加到 ADR 文件中。
   - **绝不修改任何现有部分。**仅追加或填充缺失的部分。
6. 添加所有缺失部分后，如果 ADR 的 `## Date` 字段不存在，则更新它。
7. 建议："现在此 ADR 有了 Status 和 Dependencies 字段，运行 `/architecture-review` 以重新验证覆盖范围。"

如果不在改造模式，进入下面的步骤 1（正常 ADR 编写）。

**无参数保护**：如果未提供参数（标题为空），在运行阶段 0 前询问：

> "您在记录什么技术决策？请提供一个简短的标题（例如，`event-system-architecture`、`physics-engine-choice`）。"

使用用户的响应作为标题，然后进入步骤 1。

---

## 1. 加载引擎上下文（始終優先）

在进行任何其他操作之前，建立引擎环境：

1. 读取 `docs/engine-reference/[engine]/VERSION.md` 获取：
   - 引擎名称和版本
   - LLM 知识截止日期
   - 版本截止后风险级别（LOW / MEDIUM / HIGH）

2. 从标题或用户描述中识别此架构决策的**域**。常见域：Physics、Rendering、UI、Audio、Navigation、Animation、Networking、Core、Input、Scripting。

3. 如果存在，读取相应的模块参考：`docs/engine-reference/[engine]/modules/[domain].md`

4. 读取 `docs/engine-reference/[engine]/breaking-changes.md` — 标记相关域中任何在 LLM 训练截止日期之后的更改。

5. 读取 `docs/engine-reference/[engine]/deprecated-apis.md` — 标记相关域中不应使用的任何 API。

6. **在继续前显示知识差距警告**，如果域具有 MEDIUM 或 HIGH 风险：

   ```
   ⚠️ 引擎知识差距警告
   引擎：[名称 + 版本]
   域：[domain]
   风险级别：HIGH — 此版本在 LLM 知识截止日期之后。

   从引擎参考文档验证的关键更改：
   - [与此域相关的更改 1]
   - [更改 2]

   此 ADR 将根据引擎参考库进行交叉验证。
   仅使用已验证的信息进行——不要仅依赖训练数据。
   ```

   如果尚未配置引擎，提示："尚未配置引擎。先运行 `/setup-engine`，或告诉我您正在使用哪个引擎。"

---

## 2. 确定下一个 ADR 编号

扫描 `docs/architecture/` 中现有的 ADR，找到下一个编号。

---

## 3. 收集上下文

读取 `design/gdd/` 中的相关代码、现有 ADR 和相关 GDD。

### 3a：架构注册表检查（BLOCKING 关卡）

读取 `docs/registry/architecture.yaml`。提取与此 ADR 的域和决策相关的条目（按系统名称、域关键字或正在触及的状态进行 grep）。

在协作设计开始之前，向用户展示任何相关立场作为锁定约束：

```
## 现有架构立场（不得矛盾）

状态所有权：
  player_health → 由 health-system 拥有（ADR-0001）
  接口：HealthComponent.current_health（只读 float）
  → 如果此 ADR 读取或写入 player health，必须使用此接口。

接口契约：
  damage_delivery → signal 模式（ADR-0003）
  信号：damage_dealt(amount, target, is_crit)
  → 如果此 ADR 传递或接收伤害事件，必须使用此信号。

禁止模式：
  ✗ autoload_singleton_coupling（ADR-0001）
  ✗ direct_cross_system_state_write（ADR-0000）
  → 提议的方法不得使用这些模式。
```

如果用户提议的决策会与任何已注册的立场矛盾，立即暴露冲突：

> "⚠️ 冲突：此 ADR 提议 [X]，但 ADR-[NNNN] 已确立 [Y] 是此目的的接受模式。在不解决此冲突的情况下继续会产生矛盾的 ADR 和不一致的故事。
> 选项：(1) 与现有立场对齐，(2) 用明确的替换内容取代 ADR-[NNNN]，(3) 解释为什么此情况是例外。"

在冲突解决或被明确接受为有意例外之前，不要进入步骤 4（协作设计）。

---

## 4. 协作引导决策

在询问任何问题之前，从已收集的上下文（已读取的 GDD、已加载的引擎参考、已扫描的现有 ADR）中推导出 skill 的最佳猜测。然后使用 `AskUserQuestion` 呈现**确认/调整**提示——而不是开放式问题。

**首先推导假设：**
- **问题**：从标题 + GDD 上下文中推断需要做出什么决策
- **备选方案**：从引擎参考 + GDD 需求中提出 2-3 个具体选项
- **依赖关系**：扫描现有 ADR 以查找上游依赖；如果不明确，假定为 None
- **GDD 关联**：提取标题直接关联的 GDD 系统
- **状态**：新 ADR 始终为 `Proposed`——从不询问用户状态是什么

**假设标签的范围**：假设仅涵盖：问题框架、备选方案、上游依赖、GDD 关联和状态。模式设计问题（例如，"生成时机应该如何工作？"、"数据应该是内联的还是外部的？"）不是假设——它们是属于假设确认后的独立步骤的设计决策。不要在假设 AskUserQuestion widget 中包含模式设计问题。

**假设确认后**，如果 ADR 涉及模式或数据设计选择，在草稿前使用多个标签的 `AskUserQuestion` 独立询问每个设计问题。

**使用 `AskUserQuestion` 呈现假设：**

```
在起草前，我做了以下假设：

问题：[从上下文推导的一句话问题陈述]
我将考虑的备选方案：
  A) [从引擎参考推导的选项]
  B) [从 GDD 需求推导的选项]
  C) [从常见模式推导的选项]
驱动此决策的 GDD 系统：[从上下文推导的列表]
依赖：[上游 ADR（如果有），否则为"None"]
状态：Proposed

[A] 继续——用这些假设起草
[B] 更改备选方案列表
[C] 调整 GDD 关联
[D] 添加性能预算约束
[E] 有其他需要先更改的内容
```

在用户确认假设或提供修正前，不要生成 ADR。

**引擎专家和 TD 审查返回后**（步骤 5.5/5.6），如果仍有未解决的决策，将每个决策作为单独的 `AskUserQuestion` 呈现，包含提议的选项作为选择项加上自由文本退出。

**ADR Dependencies** — 从现有 ADR 推导，然后确认：
- 此决策是否依赖于任何尚未 Accepted 的其他 ADR？
- 它是否解锁或解阻塞任何其他 ADR 或 epic？
- 它是否阻塞任何特定 epic 的开始？

在 **ADR Dependencies** 部分记录答案。如果无约束适用，为每个字段填写"None"。

---

## 5. 生成 ADR

遵循以下格式：

```markdown
# ADR-[NNNN]: [Title]

## Status
[Proposed | Accepted | Deprecated | Superseded by ADR-XXXX]

## Date
[决策日期]

## Engine Compatibility

| 字段 | 值 |
|-------|-------|
| **Engine** | [例如 Godot 4.6] |
| **Domain** | [Physics / Rendering / UI / Audio / Navigation / Animation / Networking / Core / Input] |
| **Knowledge Risk** | [LOW / MEDIUM / HIGH — 来自 VERSION.md] |
| **References Consulted** | [读取的引擎参考文档列表] |
| **Post-Cutoff APIs Used** | [此决策依赖的来自 LLM 截止日期后版本的任何 API，或"None"] |
| **Verification Required** | [在发布前需要测试的具体行为，或"None"] |

## ADR Dependencies

| 字段 | 值 |
|-------|-------|
| **Depends On** | [ADR-NNNN（必须 Accepted 后才能实现），或"None"] |
| **Enables** | [ADR-NNNN（此 ADR 解锁该决策），或"None"] |
| **Blocks** | [Epic/Story 名称——此 ADR Accepted 前不能开始，或"None"] |
| **Ordering Note** | [任何上面未涵盖的顺序约束] |

## Context

### Problem Statement
[我们在解决什么问题？为什么现在需要做出这个决定？]

### Constraints
- [技术约束]
- [时间线约束]
- [资源约束]
- [兼容性要求]

### Requirements
- [必须支持 X]
- [性能必须在 Y 预算内]
- [必须与 Z 集成]

## Decision
[所做的具体技术决策，描述足够详细以供实施]

### 架构图
[此决策创建的系统架构的 ASCII 图或描述]

### 关键接口
[此决策创建的 API 合约或接口定义]

## Alternatives Considered

### Alternative 1: [Name]
- **描述**：[这将如何工作]
- **优势**：[优点]
- **劣势**：[缺点]
- **拒绝原因**：[为什么未选择]

### Alternative 2: [Name]
- **描述**：[这将如何工作]
- **优势**：[优点]
- **劣势**：[缺点]
- **拒绝原因**：[为什么未选择]

## Consequences

### 积极影响
- [此决策的良好结果]

### 消极影响
- [接受的权衡和成本]

### 风险
- [可能出错的事情]
- [每个风险的缓解措施]

## GDD Requirements Addressed

| GDD 系统 | 需求 | 此 ADR 如何解决 |
|------------|-------------|--------------------------|
| [system-name].md | [来自该 GDD 的具体规则、公式或性能约束] | [此决策如何满足它] |

## Performance Implications
- **CPU**：[预期影响]
- **Memory**：[预期影响]
- **Load Time**：[预期影响]
- **Network**：[预期影响，如果适用]

## Migration Plan
[如果这更改了现有代码，我们如何从当前状态过渡]

## Validation Criteria
[我们如何知道这个决策是正确的？什么指标或测试？]

## Related Decisions
- [相关 ADR 的链接]
- [相关设计文档的链接]
```

5.5. **引擎专家验证** — 在保存前，通过 Task 生成主要引擎专家以验证草拟的 ADR：
   - 读取 `.claude/docs/technical-preferences.md` 的 `Engine Specialists` 部分以获取主要专家
   - 如果未配置引擎，跳过此步骤
   - 生成引擎专家，要求他们确认提议的方法是否适合固定的引擎版本

**审查模式检查** — 在生成 TD-ADR 前应用：
- `solo` → 跳过。注明："TD-ADR 已跳过 — Solo 模式。"
- `lean` → 跳过（不是 PHASE-GATE）。注明："TD-ADR 已跳过 — Lean 模式。"
- `full` → 正常生成。

5.6. **技术总监战略审查** — 在引擎专家验证后，通过 Task 使用关卡 **TD-ADR** 生成 `technical-director`。

5.7. **GDD 同步检查** — 在展示写入批准前，扫描 GDD 中与 ADR 命名不一致的地方。

5. **写入批准** — 使用 `AskUserQuestion`：

如果发现 GDD 同步问题：
- "ADR 草案已完成。您想如何继续？"
  - [A] 在同一个阶段写入 ADR + 更新 GDD
  - [B] 仅写入 ADR——我会手动更新 GDD
  - [C] 还不行——我需要进一步审查

如果没有 GDD 同步问题：
- "ADR 草案已完成。可以写入吗？"
  - [A] 将 ADR 写入 `docs/architecture/adr-[NNNN]-[slug].md`
  - [B] 还不行——我需要进一步审查

6. **更新架构注册表**

扫描已写入的 ADR 以查找应注册的新架构立场。

**BLOCKING — 未经用户明确批准，不得写入 `docs/registry/architecture.yaml`。**
