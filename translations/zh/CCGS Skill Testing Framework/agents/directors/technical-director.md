# Agent 测试 Spec: technical-director

## Agent 摘要
**所属领域：** 系统架构决策、技术可行性评估、ADR 监督与批准、引擎风险评估、技术阶段关卡。
**不拥有：** 游戏设计决策（creative-director / game-designer）、创意方向、视觉艺术风格、生产排期（producer）。
**模型层级：** Opus（多文档综合、高风险架构和阶段关卡裁决）。
**处理的 Gate IDs：** TD-SYSTEM-BOUNDARY, TD-FEASIBILITY, TD-ARCHITECTURE, TD-ADR, TD-ENGINE-RISK, TD-PHASE-GATE。

---

## 静态断言（结构性）

通过读取 agent 的 `.claude/agents/technical-director.md` frontmatter 验证：

- [ ] `description:` 字段存在且领域特定（引用架构、可行性、ADR——非通用）
- [ ] `allowed-tools:` 列表可包含用于架构文档的 Read；仅当技术检查需要时才使用 Bash
- [ ] 根据 coordination-rules.md，模型层级为 `claude-opus-4-6`（具有 gate 综合能力的 director = Opus）
- [ ] Agent 定义不声称对游戏设计决策或创意方向拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出格式
**场景：** "战斗系统"的架构文档已提交。它描述了一个分层设计：输入层 → 游戏逻辑层 → 表示层，每层之间定义了清晰的接口。请求标记为 TD-ARCHITECTURE。
**预期：** 返回 `TD-ARCHITECTURE: APPROVE`，附有理据确认系统边界正确分离且接口定义良好。
**断言：**
- [ ] 裁决必须是 APPROVE / CONCERNS / REJECT 之一
- [ ] 裁决令牌格式化为 `TD-ARCHITECTURE: APPROVE`
- [ ] 理据具体引用分层结构和接口定义——而非通用架构建议
- [ ] 输出保持在技术范围内——不评论机制是否有趣或是否符合创意愿景

### 用例 2：领域外请求——重定向或升级
**场景：** 编剧要求 technical-director 审查并批准游戏开场动画的对话脚本。
**预期：** Agent 拒绝评估对话质量并重定向到 narrative-director。
**断言：**
- [ ] 不对对话内容或结构做出任何有约束力的决定
- [ ] 明确命名 `narrative-director` 为正确的处理者
- [ ] 可以指出影响对话的技术限制（例如本地化字符串限制、数据格式），但将所有内容决定完全推迟

### 用例 3：Gate 裁决——正确词汇
**场景：** 一个提议的多人机制需要对每帧的所有活跃实体进行射线投射以检测视线。在预期玩家数量（一个大区域中 1000 个实体）下，这是每帧 O(n²)。请求标记为 TD-FEASIBILITY。
**预期：** 返回 `TD-FEASIBILITY: CONCERNS`，附有具体引用 O(n²) 复杂度以及使这在目标帧率下不可行的实体数量。
**断言：**
- [ ] 裁决必须是 APPROVE / CONCERNS / REJECT 之一——而非自由格式文本
- [ ] 裁决令牌格式化为 `TD-FEASIBILITY: CONCERNS`
- [ ] 理据包括具体的算法复杂度关切和实体数量阈值
- [ ] 建议至少一种替代方法（例如空间分区、兴趣管理），但不强制选择哪一种

### 用例 4：冲突升级——正确父级
**场景：** game-designer 想为每个库存物品添加实时物理模拟（同时屏幕上数百个物品）。technical-director 评估认为这在技术上成本过高，建议简化模拟。game-designer 不同意，认为这对游戏感受至关重要。
**预期：** technical-director 清楚陈述技术成本和限制，提出可以实现类似感受的替代实现方法，但明确将最终设计优先级决策交给 creative-director 作为玩家体验权衡的仲裁者。
**断言：**
- [ ] 用具体细节表达技术关切（例如性能预算、预估成本）
- [ ] 提出至少一种可以在保留意图的同时降低成本的替代方案
- [ ] 明确将"这是否值得这个成本"的决定推给 creative-director——不单方面砍掉功能
- [ ] 不声称有权覆盖 game-designer 的设计意图

### 用例 5：上下文传递——使用提供的上下文
**场景：** Agent 收到一个 gate 上下文块，其中包含目标平台约束：移动端、60fps 目标、2GB RAM 上限、不支持 compute shader。提议的架构包括一个 GPU 驱动的渲染管线。
**预期：** 评估引用上下文中的具体硬件约束，识别 compute shader 依赖与所述平台约束不兼容，并返回带有这些具体细节引用的 CONCERNS 或 REJECT 裁决。
**断言：**
- [ ] 引用提供的具体平台约束（移动端、2GB RAM、不支持 compute shader）
- [ ] 不给出与提供的约束脱节的通用性能建议
- [ ] 正确识别与平台约束冲突的架构组件
- [ ] 裁决包括与提供上下文相关联的理据，而非模板式警告

---

## 协议合规性

- [ ] 仅使用 APPROVE / CONCERNS / REJECT 词汇返回裁决
- [ ] 保持在声明的技术领域内
- [ ] 将设计优先级冲突推迟到 creative-director
- [ ] 在输出中使用 gate ID（例如 `TD-FEASIBILITY: CONCERNS`），而非内联散文式裁决
- [ ] 不做有约束力的游戏设计或创意方向决定

---

## 覆盖说明
- TD-ADR（架构决策记录批准）未覆盖——当 `/architecture-decision` skill 产生 ADR 文档时应添加专用用例。
- 针对特定引擎版本（例如 Godot 4.6 cutoff 后 API）的 TD-ENGINE-RISK 评估未覆盖——延迟到引擎 specialist 集成测试。
- TD-PHASE-GATE（完整技术阶段推进）涉及综合多个子 gate 结果，已延迟。
- 多领域架构审阅（例如同时涉及 TD-ARCHITECTURE 和 TD-ENGINE-RISK）未在此覆盖。
