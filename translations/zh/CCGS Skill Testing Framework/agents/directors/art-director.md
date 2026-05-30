# Agent 测试 Spec: art-director

## Agent 摘要
**所属领域：** 视觉形象、艺术规范（art bible）编写与执行、资产质量标准、UI/UX 视觉设计、视觉阶段关卡、概念艺术评估。
**不拥有：** UX 交互流程和信息架构（ux-designer 的领域）、音频指导（audio-director）、代码实现。
**模型层级：** Sonnet（注意：尽管有"director"头衔，根据 coordination-rules.md，art-director 被分配为 Sonnet——它处理单个系统分析，而非 Opus 级别的多文档阶段关卡综合）。
**处理的 Gate IDs：** AD-CONCEPT-VISUAL, AD-ART-BIBLE, AD-PHASE-GATE。

---

## 静态断言（结构性）

通过读取 agent 的 `.claude/agents/art-director.md` frontmatter 验证：

- [ ] `description:` 字段存在且领域特定（引用视觉形象、art bible、资产标准——非通用）
- [ ] `allowed-tools:` 列表以读取为主；如支持则包含图像审查能力；除非资产 pipeline 检查有正当理由，否则无 Bash
- [ ] 模型层级为 `claude-sonnet-4-6`（不是 Opus——coordination-rules.md 将 art-director 分配为 Sonnet）
- [ ] Agent 定义不声称对 UX 交互流程或音频指导拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出格式
**场景：** art bible 的调色板章节提交审阅。该章节定义了以"毁灭之美"游戏支柱为关联的、去饱和土色调主调色板及高对比度强调色。调色板内部一致并引用了支柱词汇。请求标记为 AD-ART-BIBLE。
**预期：** 返回 `AD-ART-BIBLE: APPROVE`，附有理据确认调色板的内部一致性及其与所述支柱的对齐。
**断言：**
- [ ] 裁决必须是 APPROVE / CONCERNS / REJECT 之一
- [ ] 裁决令牌格式化为 `AD-ART-BIBLE: APPROVE`
- [ ] 理据引用特定的调色板特征和支柱对齐——而非通用艺术建议
- [ ] 输出保持在视觉领域内——不评论 UX 交互模式或音频氛围

### 用例 2：领域外请求——重定向或升级
**场景：** 音效设计师要求 art-director 规定当玩家进入战斗区域时环境音效应如何分层和闪避。
**预期：** Agent 拒绝定义音频行为并重定向到 audio-director。
**断言：**
- [ ] 不对音频分层或闪避行为做出任何有约束力的决定
- [ ] 明确命名 `audio-director` 为正确的处理者
- [ ] 可以指出音频是否有视觉氛围含义（例如"音频应与区域的视觉张力相匹配"），但将所有音频规范推迟到 audio-director

### 用例 3：Gate 裁决——正确词汇
**场景：** 主角的概念艺术提交审阅。该艺术使用鲜艳、饱和的调色板（主色：#FF4500, #00BFFF），直接与已建立的 art bible 的"去饱和土色调"调色板规范相矛盾。请求标记为 AD-CONCEPT-VISUAL。
**预期：** 返回 `AD-CONCEPT-VISUAL: CONCERNS`，附有具体的调色板差异引用，引用 art bible 所述调色板值与提交概念调色板的对比。
**断言：**
- [ ] 裁决必须是 APPROVE / CONCERNS / REJECT 之一——而非自由格式文本
- [ ] 裁决令牌格式化为 `AD-CONCEPT-VISUAL: CONCERNS`
- [ ] 理据具体识别调色板冲突——而非通用的"不符合风格"评论
- [ ] 引用 art bible 作为正确调色板的权威来源

### 用例 4：冲突升级——正确父级
**场景：** ux-designer 建议为 HUD 使用高对比度、亮色图标以提高可读性。art-director 认为这违反了 art bible 的低调视觉语言，会损害视觉形象。
**预期：** art-director 陈述视觉形象关切并引用 art bible，认可 ux-designer 的可读性目标为合理，并升级到 creative-director 以仲裁视觉连贯性与可用性之间的权衡。
**断言：**
- [ ] 升级到 `creative-director`（创意领域冲突的共享父级）
- [ ] 不单方面覆盖 ux-designer 的可读性建议
- [ ] 清晰地将冲突表述为两个合理目标之间的权衡
- [ ] 引用被违反的具体 art bible 规则

### 用例 5：上下文传递——使用提供的上下文
**场景：** Agent 收到一个 gate 上下文块，其中包含现有的 art bible，带有特定调色板值（主色：#8B7355, #6B6B47；强调色：#C8A96E）和风格规则（"不要纯白，不要纯黑；所有阴影有暖色调"）。一个资产提交审阅。
**预期：** 评估引用提供的 art bible 中的具体十六进制值和风格规则，而非通用的色彩理论建议。任何关切都与提供的规则的具体违反相关联。
**断言：**
- [ ] 引用提供的 art bible 上下文中的特定调色板值
- [ ] 应用提供的文档中的具体风格规则（不要纯白/纯黑，暖色阴影色调）
- [ ] 不生成与提供的 art bible 脱节的通用艺术指导反馈
- [ ] 裁决理据可追溯到提供上下文中的特定行或规则

---

## 协议合规性

- [ ] 仅使用 APPROVE / CONCERNS / REJECT 词汇返回裁决
- [ ] 保持在声明的视觉领域内
- [ ] 将 UX vs 视觉冲突升级到 creative-director
- [ ] 在输出中使用 gate ID（例如 `AD-ART-BIBLE: APPROVE`），而非内联散文式裁决
- [ ] 不做有约束力的 UX 交互、音频或代码实现决定

---

## 覆盖说明
- AD-PHASE-GATE（完整的视觉阶段推进）未覆盖——延迟到与 `/gate-check` skill 的集成。
- 资产 pipeline 标准（文件格式、分辨率、命名约定）合规性检查未在此覆盖。
- 着色器视觉输出审阅未覆盖——该与引擎 specialist 的交互已延迟。
- UI 组件视觉审阅（区别于 UX 流程审阅）可以从额外用例中受益。
