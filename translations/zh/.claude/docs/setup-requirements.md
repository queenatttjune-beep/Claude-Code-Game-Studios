# 设置要求

本模板需要安装一些工具才能使用完整功能。如果缺少工具，所有 hook 都会优雅降级 — 不会出现故障，但你会失去验证功能。

## 必需

| 工具 | 用途 | 安装方式 |
| ---- | ---- | ---- |
| **Git** | 版本控制、分支管理 | [git-scm.com](https://git-scm.com/) |
| **Claude Code** | AI Agent CLI | `npm install -g @anthropic-ai/claude-code` |

## 推荐

| 工具 | 被以下使用 | 用途 | 安装方式 |
| ---- | ---- | ---- | ---- |
| **jq** | Hook（12 个中的 7 个） | 提交/推送/资源/Agent hook 中的 JSON 解析 | 见下方 |
| **Python 3** | Hook（12 个中的 2 个） | 数据文件的 JSON 验证 | [python.org](https://www.python.org/) |
| **Bash** | 所有 Hook | Shell 脚本执行 | 随 Git for Windows 附带 |

### 安装 jq

**Windows**（任选一种）：
```
winget install jqlang.jq
choco install jq
scoop install jq
```

**macOS**：
```
brew install jq
```

**Linux**：
```
sudo apt install jq     # Debian/Ubuntu
sudo dnf install jq     # Fedora
sudo pacman -S jq       # Arch
```

## 平台说明

### Windows
- Git for Windows 包含 **Git Bash**，提供 `bash` 命令，供 `settings.json` 中所有 hook 使用
- 确保 Git Bash 在 PATH 中（如果通过 Git 安装程序默认安装则会自动配置）
- Hook 使用 `bash .claude/hooks/[name].sh` — 这在 Windows 上有效，因为 Claude Code 通过一个能找到 `bash.exe` 的 shell 调用命令

### macOS / Linux
- Bash 原生可用
- 通过包管理器安装 `jq` 以获得完整 hook 支持

## 验证你的设置

运行以下命令检查先决条件：

```bash
git --version          # 应显示 git 版本
bash --version         # 应显示 bash 版本
jq --version           # 应显示 jq 版本（可选）
python3 --version      # 应显示 python 版本（可选）
```

## 缺少可选工具的影响

| 缺少的工具 | 影响 |
| ---- | ---- |
| **jq** | 提交验证、推送保护、资源验证和 Agent 审计 hook 静默跳过检查。提交和推送仍然正常工作。 |
| **Python 3** | 提交和资源 hook 中的 JSON 数据文件验证被跳过。无效 JSON 可以被提交而不发出警告。 |
| **两者都缺** | 所有 hook 仍然无错误执行（exit 0），但不提供任何验证。你将在没有安全网的情况下工作。 |

## 推荐 IDE

Claude Code 可与任何编辑器配合使用，但本模板针对以下工具进行了优化：
- **VS Code** 及 Claude Code 扩展
- **Cursor**（兼容 Claude Code）
- 基于终端的 Claude Code CLI
