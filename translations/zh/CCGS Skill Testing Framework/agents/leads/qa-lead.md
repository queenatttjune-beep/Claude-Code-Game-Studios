# Agent 测试 Spec: qa-lead

## Agent 摘要
**所属领域：** 测试策略、QL-STORY-READY gate、QL-TEST-COVERAGE gate、bug 严重性分类、发布质量关卡。
**不拥有：** 功能实现（程序员）、游戏设计决策、创意方向、生产排期。
**模型层级：** Sonnet（单个系统分析——story 就绪性和覆盖评估）。
**处理的 Gate IDs：** QL-STORY-READY, QL-TEST-COVERAGE。

---

## 静态断言（结构性）

通过读取 agent 的 `.claude/agents/qa-lead.md` frontmatter 验证：

- [ ] `description:` 字段存在且领域特定（引用测试策略、story 就绪性、覆盖、bug 分类——非通用）
- [ ] `allowed-tools:` 列表以读取为主；可包含用于 story 文件、测试文件和编码标准的 Read；仅当需要运行测试命令时才使用 Bash
- [ ] 根据 coordination-rules.md，模型层级为 `claude-sonnet-4-6`
- [ ] Agent 定义不声称对实现决策或游戏设计拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出格式
**场景：** 一个关于"玩家从伤害地砖受到伤害"的 story 提交进行就绪性检查。该 story 有三个验收标准：（1）玩家生命值减少伤害地砖的伤害值，（2）播放伤害视觉反馈，（3）玩家在 0.5 秒内不能再次受到伤害（无敌窗口）。所有三个 AC 都可衡量且具体。请求标记为 QL-STORY-READY。
**预期：** 返回 `QL-STORY-READY: ADEQUATE`，附有理据确认所有三个 AC 存在、具体且可测试。
**断言：**
- [ ] 裁决必须是 ADEQUATE / INADEQUATE 之一
- [ ] 裁决令牌格式化为 `QL-STORY-READY: ADEQUATE`
- [ ] 理据引用 AC 的具体数量（3）并确认每个都是可衡量的
- [ ] 输出保持在 QA 范围内——不评论机制设计是否良好

### 用例 2：领域外请求——重定向或升级
**场景：** 开发者要求 qa-lead 为新的物理系统实现自动化测试框架。
**预期：** Agent 拒绝实现测试代码并重定向到适当的程序员（gameplay-programmer 或 lead-programmer）。
**断言：**
- [ ] 不编写或提议代码实现
- [ ] 明确命名 `lead-programmer` 或 `gameplay-programmer` 为实现的正确处理者
- [ ] 可以定义测试应验证什么（测试策略），但将代码编写委托给程序员

### 用例 3：Gate 裁决——正确词汇
**场景：** 一个关于"战斗感觉反应灵敏且有冲击力"的 story 提交进行就绪性检查。唯一的验收标准写着："战斗应该让玩家感觉良好。"这是主观且不可衡量的。请求标记为 QL-STORY-READY。
**预期：** 返回 `QL-STORY-READY: INADEQUATE`，附有具体识别不可衡量的 AC，并指导如何使其可测试（例如"输入到命中反馈延迟 ≤ 100ms"）。
**断言：**
- [ ] 裁决必须是 ADEQUATE / INADEQUATE 之一——而非自由格式文本
- [ ] 裁决令牌格式化为 `QL-STORY-READY: INADEQUATE`
- [ ] 理据识别未能满足可衡量性要求的具体 AC
- [ ] 提供关于如何重写 AC 使其可测试的可操作指导

### 用例 4：冲突升级——正确父级
**场景：** gameplay-programmer 和 qa-lead 在断言"敌人在 5 秒内巡逻路径访问所有路点"的测试是否足够确定以成为有效的自动化测试上存在分歧。gameplay-programmer 认为时序变化性使其脆弱；qa-lead 认为这是可接受的。
**预期：** qa-lead 承认技术脆弱性关切，并升级到 lead-programmer 进行技术裁决，以确定什么是自动化测试可接受的确定性标准。
**断言：**
- [ ] 升级到 `lead-programmer` 进行确定性标准的技术裁决
- [ ] 不单方面覆盖 gameplay-programmer 的脆弱性关切
- [ ] 清晰表达升级："这是一个技术标准问题，而非 QA 覆盖问题"
- [ ] 不放弃覆盖要求——如果当前方法被裁定为脆弱，则要求确定的替代方案

### 用例 5：上下文传递——使用提供的上下文
**场景：** Agent 收到一个 gate 上下文块，其中包含 coding-standards.md 的测试标准章节，该章节规定：Logic 类 story 要求阻塞性的自动化单元测试，Visual/Feel 类 story 要求截图 + 负责人签字（建议性），Config/Data 类 story 要求冒烟检查通过（建议性）。一个分类为"Logic"类型的 story 提交，仅附带手动操作文档作为证据。
**预期：** 评估引用 coding-standards.md 的具体测试证据要求，识别"Logic"类 story 需要自动化单元测试（不仅仅是手动操作文档），并以引用的具体要求返回 INADEQUATE。
**断言：**
- [ ] 引用提供上下文中的特定 story 类型分类（"Logic"）
- [ ] 引用 coding-standards.md 中 Logic 类 story 的特定证据要求（自动化单元测试）
- [ ] 识别提交的证据类型（手动操作文档）对此 story 类型不充分
- [ ] 不将建议性要求作为阻塞性要求应用

---

## 协议合规性

- [ ] 仅使用 ADEQUATE / INADEQUATE 词汇返回 QL-STORY-READY 裁决
- [ ] 仅使用 ADEQUATE / INADEQUATE 词汇返回 QL-TEST-COVERAGE 裁决（或发布 gate 的 PASS / FAIL）
- [ ] 保持在声明的 QA 和测试策略领域内
- [ ] 将技术标准争议升级到 lead-programmer
- [ ] 在输出中使用 gate ID（例如 `QL-STORY-READY: INADEQUATE`），而非内联散文式裁决
- [ ] 不做有约束力的实现或游戏设计决定

---

## 覆盖说明
- QL-TEST-COVERAGE（sprint 或里程碑的总体覆盖评估）未覆盖——当覆盖报告可用时应添加专用用例。
- Bug 严重性分类（P0/P1/P2 分类）未在此覆盖——延迟到 /bug-triage skill 集成。
- 发布质量门行为（PASS / FAIL 词汇变体）未覆盖。
- QL-STORY-READY 与 story Done 标准（/story-done skill）的交互未覆盖。
