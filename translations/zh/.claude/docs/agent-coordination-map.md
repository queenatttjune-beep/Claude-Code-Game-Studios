# Agent 协调与委托图

## 组织层级

```
                           [人类开发者]
                                 |
                 +---------------+---------------+
                 |               |               |
         creative-director  technical-director  producer
                 |               |               |
        +--------+--------+     |        (协调所有)
        |        |        |     |
  game-designer art-dir  narr-dir  lead-programmer  qa-lead  audio-dir
        |        |        |         |                |        |
     +--+--+     |     +--+--+  +--+--+--+--+--+   |        |
     |  |  |     |     |     |  |  |  |  |  |  |   |        |
    sys lvl eco  ta   wrt  wrld gp ep  ai net tl ui qa-t    snd
                                 |
                             +---+---+
                             |       |
                          perf-a   devops   analytics

  其他主管 (向 producer/directors 汇报):
    release-manager         -- 发布管道、版本管理、部署
    localization-lead       -- i18n、字符串表、翻译管道
    prototyper              -- 快速一次性原型、概念验证
    security-engineer       -- 反作弊、漏洞利用、数据隐私、网络安全
    accessibility-specialist -- WCAG、色盲、重映射、文本缩放
    live-ops-designer       -- 赛季、活动、战斗通行证、留存、在线经济
    community-manager       -- 补丁说明、玩家反馈、危机公关

  引擎专家 (使用与你的引擎匹配的 SET):
    unreal-specialist  -- UE5 主导: Blueprint/C++、GAS 概述、UE 子系统
      ue-gas-specialist         -- GAS: 能力、效果、属性、标签、预测
      ue-blueprint-specialist   -- Blueprint: BP/C++ 边界、图形标准、优化
      ue-replication-specialist -- 网络: 复制、RPC、预测、带宽
      ue-umg-specialist         -- UI: UMG、CommonUI、Widget 层级、数据绑定

    unity-specialist   -- Unity 主导: MonoBehaviour/DOTS、Addressables、URP/HDRP
      unity-dots-specialist         -- DOTS/ECS: Jobs、Burst、混合渲染器
      unity-shader-specialist       -- 着色器: Shader Graph、VFX Graph、SRP 自定义
      unity-addressables-specialist -- 资源: 异步加载、Bundle、内存、CDN
      unity-ui-specialist           -- UI: UI Toolkit、UGUI、UXML/USS、数据绑定

    godot-specialist   -- Godot 4 主导: GDScript、节点/场景、信号、资源
      godot-gdscript-specialist    -- GDScript: 静态类型、模式、信号、性能
      godot-csharp-specialist      -- C#: .NET 模式、[Signal] 委托、异步、类型安全节点访问
      godot-shader-specialist      -- 着色器: Godot 着色语言、视觉着色器、VFX
      godot-gdextension-specialist -- 原生: C++/Rust 绑定、GDExtension、构建系统
```

### 图例
```
sys  = systems-designer       gp  = gameplay-programmer
lvl  = level-designer         ep  = engine-programmer
eco  = economy-designer       ai  = ai-programmer
ta   = technical-artist       net = network-programmer
wrt  = writer                 tl  = tools-programmer
wrld = world-builder          ui  = ui-programmer
snd  = sound-designer         qa-t = qa-tester
narr-dir = narrative-director perf-a = performance-analyst
art-dir = art-director
```

## 委托规则

### 谁可以委托给谁

| 来自 | 可以委托给 |
|------|----------------|
| creative-director | game-designer、art-director、audio-director、narrative-director |
| technical-director | lead-programmer、devops-engineer、performance-analyst、technical-artist (技术决策) |
| producer | 任何 agent (仅限其领域内的任务分配) |
| game-designer | systems-designer、level-designer、economy-designer |
| lead-programmer | gameplay-programmer、engine-programmer、ai-programmer、network-programmer、tools-programmer、ui-programmer |
| art-director | technical-artist、ux-designer |
| audio-director | sound-designer |
| narrative-director | writer、world-builder |
| qa-lead | qa-tester |
| release-manager | devops-engineer (发布构建)、qa-lead (发布测试) |
| localization-lead | writer (字符串审查)、ui-programmer (文本适配) |
| prototyper | (独立工作，向 producer 和相关主管报告发现) |
| security-engineer | network-programmer (安全审查)、lead-programmer (安全模式) |
| accessibility-specialist | ux-designer (无障碍模式)、ui-programmer (实现)、qa-tester (a11y 测试) |
| [engine]-specialist | 引擎子专家 (委托子系统特定工作) |
| [engine] 子专家 | (就引擎子系统模式和优化向所有程序员提供建议) |
| live-ops-designer | economy-designer (在线经济)、community-manager (活动公关)、analytics-engineer (参与度指标) |
| community-manager | (与 producer 合作获取批准，与 release-manager 协调补丁说明时间) |

### 升级路径

| 情况 | 升级给 |
|-----------|------------|
| 两个设计师对某个机制有分歧 | game-designer |
| 游戏设计与叙事冲突 | creative-director |
| 游戏设计与技术可行性冲突 | producer (促进)，然后 creative-director + technical-director |
| 美术与音频调性冲突 | creative-director |
| 代码架构分歧 | technical-director |
| 跨系统代码冲突 | lead-programmer，然后 technical-director |
| 部门间进度冲突 | producer |
| 范围超出容量 | producer，然后 creative-director 决定削减 |
| 质量门控分歧 | qa-lead，然后 technical-director |
| 性能预算违规 | performance-analyst 标记，technical-director 决定 |

## 常见工作流模式

### 模式 1: 新功能 (完整管道)

```
1. creative-director  -- 批准功能概念符合愿景
2. game-designer      -- 创建包含完整规范的 design document
3. producer           -- 安排工作，识别依赖关系
4. lead-programmer    -- 设计代码架构，创建接口草图
5. [专家程序员] -- 实现功能
6. technical-artist   -- 实现视觉效果 (如果需要)
7. writer             -- 创建文本内容 (如果需要)
8. sound-designer     -- 创建音频事件列表 (如果需要)
9. qa-tester          -- 编写测试用例
10. qa-lead           -- 审查并批准测试覆盖率
11. lead-programmer   -- 代码审查
12. qa-tester         -- 执行测试
13. producer          -- 标记任务完成
```

### 模式 2: Bug 修复

```
1. qa-tester          -- 使用 /bug-report 提交 bug 报告
2. qa-lead            -- 分类严重性和优先级
3. producer           -- 分配到 sprint (如果不是 S1)
4. lead-programmer    -- 识别根因，分配给程序员
5. [专家程序员] -- 修复 bug
6. lead-programmer    -- 代码审查
7. qa-tester          -- 验证修复并运行回归测试
8. qa-lead            -- 关闭 bug
```

### 模式 3: 平衡性调整

```
1. analytics-engineer -- 从数据 (或玩家报告) 识别不平衡
2. game-designer      -- 根据设计意图评估问题
3. economy-designer   -- 建模调整方案
4. game-designer      -- 批准新值
5. [数据文件更新] -- 更改配置值
6. qa-tester          -- 回归测试受影响系统
7. analytics-engineer -- 监控变更后的指标
```

### 模式 4: 新区域/关卡

```
1. narrative-director -- 定义区域的叙事目的和节拍
2. world-builder      -- 创建背景故事和环境上下文
3. level-designer     -- 设计布局、遭遇、节奏
4. game-designer      -- 审查遭遇的机制设计
5. art-director       -- 定义区域的视觉方向
6. audio-director     -- 定义区域的音频方向
7. [相关程序员和美工实现]
8. writer             -- 创建区域特定的文本内容
9. qa-tester          -- 测试完整区域
```

### 模式 5: Sprint 周期

```
1. producer           -- 使用 /sprint-plan new 规划 sprint
2. [所有 Agent]       -- 执行分配的任务
3. producer           -- 使用 /sprint-plan status 每日状态
4. qa-lead            -- Sprint 期间的持续测试
5. lead-programmer    -- Sprint 期间的持续代码审查
6. producer           -- 使用 post-sprint hook 进行 sprint 回顾
7. producer           -- 纳入经验教训后规划下一个 sprint
```

### 模式 6: 里程碑检查点

```
1. producer           -- 运行 /milestone-review
2. creative-director  -- 审查创意进展
3. technical-director -- 审查技术健康度
4. qa-lead            -- 审查质量指标
5. producer           -- 促进 go/no-go 讨论
6. [所有总监]         -- 如果需要，就范围调整达成一致
7. producer           -- 记录决策并更新计划
```

### 模式 7: 发布管道

```text
1. producer             -- 声明发布候选版本，确认里程碑标准已满足
2. release-manager      -- 创建发布分支，生成 /release-checklist
3. qa-lead              -- 运行完整回归测试，签署质量
4. localization-lead    -- 验证所有字符串已翻译，文本适配通过
5. performance-analyst  -- 确认性能基准在目标范围内
6. devops-engineer      -- 构建发布制品，运行部署管道
7. release-manager      -- 生成 /changelog，打标签，创建发布说明
8. technical-director   -- 主要发布的最终签署
9. release-manager      -- 部署并监控 48 小时
10. producer            -- 标记发布完成
```

### 模式 8: 概念原型 (早期 -- 在 GDD 之前)

```text
1. game-designer        -- 定义假设和成功标准
2. prototyper           -- 使用 /prototype 搭建概念原型
3. prototyper           -- 构建最小实现 (1-3 天)
4. game-designer        -- 根据标准评估原型
5. prototyper           -- 在 REPORT.md 中记录发现
6. creative-director    -- PROCEED / PIVOT / KILL 决策 (仅完整模式)
7. game-designer        -- 如果 PROCEED，使用原型经验指导 GDD 编写
```

### 模式 8b: Vertical Slice (预生产 -- GDD 和架构之后)

```text
1. game-designer        -- 确认 slice 范围与 GDD 一致
2. prototyper           -- 使用 /vertical-slice 构建生产质量端到端构建
3. prototyper           -- 进行内部试玩会话 (至少 1 次)
4. prototyper           -- 在 REPORT.md 中记录发现
5. creative-director    -- 进行/停止决策，决定是否进行生产 (完整模式)
6. producer             -- 如果 PROCEED，安排生产 Epic/sprint
```

### 模式 9: 在线活动 / 赛季发布

```text
1. live-ops-designer     -- 设计活动/赛季内容、奖励、时间表
2. game-designer         -- 验证活动玩法机制
3. economy-designer      -- 平衡活动经济和奖励值
4. narrative-director    -- 提供赛季叙事主题
5. writer                -- 创建活动描述和背景故事
6. producer              -- 安排实现工作
7. [相关程序员实现]
8. qa-lead               -- 端到端测试活动流程
9. community-manager     -- 草拟活动公告和补丁说明
10. release-manager      -- 部署活动内容
11. analytics-engineer   -- 监控活动参与度和指标
12. live-ops-designer    -- 活动后分析和经验教训
```

## 跨域通信协议

### 设计变更通知

当 design document 更改时，game-designer 必须通知:
- lead-programmer (实现影响)
- qa-lead (需要更新测试计划)
- producer (进度影响评估)
- 根据变更相关的专家 agent

### 架构变更通知

当 ADR 创建或修改时，technical-director 必须通知:
- lead-programmer (需要的代码更改)
- 所有受影响的专家程序员
- qa-lead (测试策略可能改变)
- producer (进度影响)

### 资源标准变更通知

当 art bible 或资源标准更改时，art-director 必须通知:
- technical-artist (管道变更)
- 所有使用受影响资源的内容创作者
- devops-engineer (如果构建管道受影响)

## 需要避免的反模式

1. **绕过层级**: 专家 agent 不应在未咨询主管的情况下做出属于主管的决策。
2. **跨域实现**: 未经相关负责人的明确授权，agent 不应修改其指定区域之外的文件。
3. **暗箱决策**: 所有决策必须记录在案。没有书面记录的口头协议会导致矛盾。
4. **单体任务**: 分配给 agent 的每个任务应在 1-3 天内完成。如果更大，必须先分解。
5. **基于假设的实现**: 如果规范不明确，实现者必须询问规范制定者，而不是猜测。错误的猜测比提问更昂贵。
