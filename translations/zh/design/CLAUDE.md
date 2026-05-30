# 设计目录

在此目录中编写或编辑文件时，请遵循以下标准。

## GDD 文件（`design/gdd/`）

每个 GDD 必须包含所有 **8 个必要章节**，按此顺序：
1. 概述 — 一段摘要
2. 玩家幻想 — 预期感受和体验
3. 详细规则 — 明确的机制
4. 公式 — 所有数学公式使用变量定义
5. 边界情况 — 处理异常情况
6. 依赖关系 — 列出其他系统
7. 调节旋钮 — 标识可配置值
8. 验收标准 — 可测试的成功条件

**文件命名：** `[system-slug].md`（例如 `movement-system.md`、`combat-system.md`）

**系统索引：** `design/gdd/systems-index.md` — 添加新 GDD 时更新。

**设计顺序：** Foundation → Core → Feature → Presentation → Polish

**验证：** 编写任何 GDD 后运行 `/design-review [path]`。
完成一组相关 GDD 后运行 `/review-all-gdds`。

## 快速规格（`design/quick-specs/`）

用于调优更改、次要机制或平衡调整的轻量级规格。使用 `/quick-design` 编写。

## UX 规格（`design/ux/`）

- 逐个界面规格：`design/ux/[screen-name].md`
- HUD 设计：`design/ux/hud.md`
- 交互模式库：`design/ux/interaction-patterns.md`
- 无障碍需求：`design/ux/accessibility-requirements.md`

使用 `/ux-design` 编写。在移交给 `/team-ui` 之前使用 `/ux-review` 验证。
