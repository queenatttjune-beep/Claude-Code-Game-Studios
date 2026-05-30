# 引擎参考文档

本目录包含本项目使用的一个或多个游戏引擎的精选、版本固定的文档快照。之所以存在这些文件，是因为 **LLM 知识有截止日期**，而游戏引擎更新频繁。

## 为何存在此目录

Claude 的训练数据有知识截止日期（当前为 2025 年 5 月）。Godot、Unity 和 Unreal 等游戏引擎会发布更新，引入破坏性 API 更改、新特性和弃用模式。如果没有这些参考文件，agent 会建议过时的代码。

## 结构

每个引擎有自己的目录：

```
<engine>/
├── VERSION.md              # 固定版本、验证日期、知识空白窗口
├── breaking-changes.md     # 版本间的 API 更改，按风险级别组织
├── deprecated-apis.md      # "不要用 X → 改用 Y" 查找表
├── current-best-practices.md  # 模型训练数据中没有的新实践
└── modules/                # 每个子系统的快速参考（每文件最多 150 行）
    ├── rendering.md
    ├── physics.md
    └── ...
```

## Agent 如何使用这些文件

引擎专业 agent 被指示：

1. 读取 `VERSION.md` 确认当前引擎版本
2. 在建议任何引擎 API 之前检查 `deprecated-apis.md`
3. 查阅 `breaking-changes.md` 了解特定版本的问题
4. 读取相关的 `modules/*.md` 了解子系统特定工作

## 维护

### 何时更新

- 升级引擎版本后
- LLM 模型更新后（新知识截止日期）
- 运行 `/refresh-docs` 后（如果可用）
- 当你发现模型搞错了某个 API 时

### 如何更新

1. 用新的引擎版本和日期更新 `VERSION.md`
2. 向 `breaking-changes.md` 添加版本过渡的新条目
3. 将新弃用的 API 移到 `deprecated-apis.md`
4. 用新模式更新 `current-best-practices.md`
5. 用 API 更改更新相关的 `modules/*.md`
6. 在所有修改的文件上设置 "Last verified" 日期

### 质量标准

- 每个文件必须有 "Last verified: YYYY-MM-DD" 日期
- 保持模块文件在 150 行以内（上下文预算）
- 包含正确 / 错误模式的代码示例
- 链接到官方文档 URL 以供验证
- 仅记录与模型训练数据不同的内容
