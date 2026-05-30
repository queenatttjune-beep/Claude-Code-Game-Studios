# CLAUDE.local.md 模板

将此文件复制到项目根目录并命名为 `CLAUDE.local.md`，用作个人覆盖配置。
该文件已加入 gitignore，不会被提交。

```markdown
# 个人偏好

## 模型偏好
- 复杂设计任务优先使用 Opus
- 快速查询和简单编辑使用 Haiku

## 工作流偏好
- 代码更改后始终运行测试
- 上下文使用率达到 60% 时主动压缩
- 不相关任务之间使用 /clear

## 本地环境
- Python 命令：python（或 py / python3）
- Shell：Windows 上的 Git Bash
- IDE：安装了 Claude Code 扩展的 VS Code

## 沟通风格
- 回复保持简洁
- 所有代码引用中显示文件路径
- 简要解释架构决策

## 个人快捷键
- 当我说 "review" 时，对最后更改的文件运行 /code-review
- 当我说 "status" 时，显示 git status 和 sprint 进度
```

## 设置

1. 将此模板复制到项目根目录：`cp .claude/docs/CLAUDE-local-template.md CLAUDE.local.md`
2. 根据你的偏好进行编辑
3. 确认 `CLAUDE.local.md` 已在 `.gitignore` 中（Claude Code 从项目根目录读取该文件）
