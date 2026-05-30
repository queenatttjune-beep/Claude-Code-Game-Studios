# Agent 测试 Spec: creative-director

## Agent 摘要
**所属领域：** 创意愿景、游戏支柱、GDD 对齐、系统分解反馈、叙事方向、游戏测试反馈解读、阶段关卡（创意方面）。
**不拥有：** 技术架构或实现细节（委托给 technical-director）、生产排期（producer）、视觉艺术风格执行（委托给 art-director）。
**模型层级：** Opus（多文档综合、高风险的阶段关卡裁决）。
**处理的 Gate IDs：** CD-PILLARS, CD-GDD-ALIGN, CD-SYSTEMS, CD-NARRATIVE, CD-PLAYTEST, CD-PHASE-GATE。

---

## 静态断言（结构性）

通过读取 agent 的 `.claude/agents/creative-director.md` frontmatter 验证：

- [ ] `description:` 字段存在且领域特定（引用创意愿景、支柱、GDD 对齐——非通用）
- [ ] `allowed-tools:` 列表以读取为主；除非创意工作流需要，不应包含 Bash
- [ ] 根据 coordination-rules.md，模型层级为 `claude-opus-4-6`（具有 gate 综合能力的 director = Opus）
- [ ] Agent 定义不声称对技术架构或生产排期拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出格式
**场景：** 一个游戏概念文档提交进行支柱审查。该概念描述了一个围绕三大支柱构建的叙事生存游戏："涌现故事"、"有意义的牺牲"和"鲜活的世界"。请求标记为 CD-PILLARS。
**预期：** 返回 `CD-PILLARS: APPROVE`，附有理据说明每个支柱在概念中如何体现，以及文档中发现的任何强化或弱化信号。
**断言：**
- [ ] 裁决必须是 APPROVE / CONCERNS / REJECT 之一
- [ ] 裁决令牌格式化为 `CD-PILLARS: APPROVE`（gate ID 前缀、冒号、裁决关键词）
- [ ] 理据按名称引用三个特定支柱，而非通用创意建议
- [ ] 输出保持在创意范围内——不评论引擎可行性或 sprint 排期

### 用例 2：领域外请求——重定向或升级
**场景：** 开发者要求 creative-director 审查一个用于存储玩家存档数据的 PostgreSQL schema。
**预期：** Agent 拒绝评估 schema 并重定向到 technical-director。
**断言：**
- [ ] 不对 schema 设计做出任何有约束力的决定
- [ ] 明确命名 `technical-director` 为正确的处理者
- [ ] 可以指出数据模型是否有创意含义（例如追踪哪些玩家数据），但将所有结构性决定完全推迟

### 用例 3：Gate 裁决——正确词汇
**场景：** "Crafting"系统的 GDD 已提交。第 4 节（公式）定义了一个惩罚探索的资源衰减公式——与"自由漫游无需恐惧"的玩家幻想章节相矛盾。请求标记为 CD-GDD-ALIGN。
**预期：** 返回 `CD-GDD-ALIGN: CONCERNS`，附有公式行为与玩家幻想陈述之间矛盾的引用。
**断言：**
- [ ] 裁决必须是 APPROVE / CONCERNS / REJECT 之一——而非自由格式文本
- [ ] 裁决令牌格式化为 `CD-GDD-ALIGN: CONCERNS`
- [ ] 理据引用或直接引用 GDD 第 4 节（公式）和玩家幻想章节
- [ ] 不规定具体的公式修复——那属于 systems-designer

### 用例 4：冲突升级——正确父级
**场景：** technical-director 提出核心循环机制（实时分支对话）实施成本过高，建议砍掉。creative-director 以创意为由不同意。
**预期：** creative-director 承认技术约束，不覆盖 technical-director 的可行性评估，但保留定义创意目标是什么的权威。对于冲突本身，creative-director 是最高创意升级点，在倡导设计意图的同时将实现可行性问题交给 technical-director。解决路径是两者共同向用户呈现权衡选项。
**断言：**
- [ ] 不单方面覆盖 technical-director 的可行性关切
- [ ] 清晰区分"我们在创意上想要什么"和"它如何被构建"
- [ ] 提议向用户呈现权衡而非单方面解决
- [ ] 不声称拥有实施决定权

### 用例 5：上下文传递——使用提供的上下文
**场景：** Agent 收到一个 gate 上下文块，其中包含游戏支柱文档（`design/gdd/pillars.md`）和一个待审阅的新机制 spec。该支柱文档将"玩家自主创作"、"后果永久性"和"世界响应性"定义为三大核心支柱。
**预期：** 评估使用提供文档中的确切支柱词汇，而非通用创意启发式规则。任何批准或关切都与一个或多个命名支柱相关联。
**断言：**
- [ ] 使用提供上下文文档中的确切支柱名称
- [ ] 不生成与提供的支柱脱节的通用创意反馈
- [ ] 引用与正在审阅的机制最相关的具体支柱
- [ ] 不引用提供文档中不存在的支柱

---

## 协议合规性

- [ ] 仅使用 APPROVE / CONCERNS / REJECT 词汇返回裁决
- [ ] 保持在声明的创意领域内
- [ ] 通过向用户呈现权衡而非单方面覆盖来解决冲突
- [ ] 在输出中使用 gate ID（例如 `CD-PILLARS: APPROVE`），而非内联散文式裁决
- [ ] 不做有约束力的跨领域决定（技术、生产、艺术执行）

---

## 覆盖说明
- 多 gate 场景（例如一次提交同时触发 CD-PILLARS 和 CD-GDD-ALIGN）未在此覆盖——延迟到集成测试。
- CD-PHASE-GATE（完整阶段推进）涉及综合多个子 gate 结果；此复杂案例已延迟。
- 游戏测试报告解读（CD-PLAYTEST）未覆盖——当 playtest-report skill 产生结构化输出时应添加专用用例。
- 与 art-director 在视觉支柱对齐方面的交互未覆盖。
