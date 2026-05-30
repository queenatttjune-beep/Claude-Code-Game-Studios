# Hook: post-sprint-retrospective

## 触发时机

每个 sprint 结束时手动触发（通常由 producer agent 或人类开发者调用）。

## 目的

通过分析 sprint 数据自动生成回顾起点：计划与完成情况对比、速度变化、Bug 趋势和常见阻塞项。这不是一个 git hook，而是通过 `producer` agent 调用的工作流 hook。

## 实现

这是一个工作流 hook，而非 git hook。通过运行以下命令调用：

```
@producer Generate sprint retrospective for Sprint [N]
```

producer agent 应执行以下操作：

1. **读取 sprint 计划** 从 `production/sprints/sprint-[N].md`
2. **计算指标**：
   - 计划任务 vs 完成任务
   - 计划故事点数 vs 完成故事点数（如使用）
   - 从上个 sprint 结转的项目
   - 在 sprint 中期新增的任务
   - 平均任务完成时间
3. **分析模式**：
   - 最常见的阻塞项
   - 哪个 Agent/领域有最多未完成工作
   - 哪些估算最不准确
4. **生成回顾**：

```markdown
# Sprint [N] 回顾

## 指标
| 指标 | 数值 |
|--------|-------|
| 计划任务数 | [N] |
| 完成任务数 | [N] |
| 完成率 | [X%] |
| 从上个 sprint 结转 | [N] |
| 新增任务数 | [N] |
| 发现 Bug 数 | [N] |
| 修复 Bug 数 | [N] |

## 速度趋势
[Sprint N-2]: [X] | [Sprint N-1]: [Y] | [Sprint N]: [Z]
趋势：[Improving / Stable / Declining]

## 做得好的方面
- [自动检测：提前于估算完成的任务]
- [引导者添加团队观察]

## 做得不好的方面
- [自动检测：被结转或裁剪的任务]
- [自动检测：估算严重超标的领域]
- [引导者添加团队观察]

## 阻塞项
| 阻塞项 | 频率 | 解决时间 | 预防措施 |
|---------|-----------|----------------|-----------|

## 下个 Sprint 的行动项
| # | 行动 | 负责人 | 优先级 |
|---|--------|-------|----------|

## 估算准确性
| 领域 | 平均计划 | 平均实际 | 准确性 |
|------|------------|-----------|----------|
```

5. **保存** 到 `production/sprints/sprint-[N]-retro.md`
