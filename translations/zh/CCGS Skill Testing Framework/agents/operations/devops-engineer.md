# Agent 测试 Spec: devops-engineer

## Agent 摘要
- **领域**：CI/CD 管线配置、构建脚本、版本控制工作流执行、部署基础设施、分支策略、环境管理、CI 中的自动化测试集成
- **不拥有**：游戏逻辑或游戏玩法系统、安全审计（security-engineer）、QA 测试策略（qa-lead）、游戏网络逻辑（network-programmer）
- **模型层级**：Sonnet
- **Gate IDs**：无；将部署阻塞项升级到 producer

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 CI/CD、构建、部署、版本控制）
- [ ] `allowed-tools:` 列表匹配 agent 的角色（管线配置文件、shell 脚本、YAML 的 Read/Write；无游戏源编辑工具）
- [ ] 模型层级为 Sonnet（operations specialist 默认）
- [ ] Agent 定义不声称对游戏逻辑、安全审计或 QA 测试设计拥有权威

---

## 测试用例

### 用例 1：领域内请求——Godot 项目的 CI 设置
**输入**："为我们的 Godot 4 项目设置 CI 管线。每次推送到 main 和每个 pull request 时应运行测试，如果测试失败则构建失败。"
**预期行为**：
- 生成 GitHub Actions 工作流 YAML（`.github/workflows/ci.yml` 或等效）
- 使用来自 `coding-standards.md` 的 Godot 无头测试运行器命令：`godot --headless --script tests/gdunit4_runner.gd`
- 配置推送到 main 和 `pull_request` 的触发器
- 设置任务在测试失败时失败（`exit 1` 或非零退出）——不配置在测试失败时继续的管线
- 在输出或注释中引用项目的编码标准 CI 规则

### 用例 2：领域外请求——游戏网络实现
**输入**："为我们的多人游戏实现服务器权威移动系统。"
**预期行为**：
- 不生成游戏网络或移动代码
- 明确说明："游戏网络实现由 network-programmer 负责；我处理构建、测试和部署游戏的基础设施"
- 不混淆 CI 管线配置与游戏内网络架构

### 用例 3：构建失败诊断
**输入**："我们的 CI 管线的合并步骤失败。错误是：'Asset import failed: texture compression format unsupported in headless mode.'"
**预期行为**：
- 诊断根本原因：无头 CI 环境不支持依赖 GPU 的纹理压缩
- 提出具体的修复方案：要么在 CI 之前本地预导入资产（将 .import 文件提交到 VCS），在 CI 中配置 Godot 的导入设置使用 CPU 兼容的压缩格式，或者使用带 GPU 模拟的 Docker 镜像（如果可用）
- 不声明管线无法修复——提供至少一条可操作的路径
- 指出任何权衡（提交 .import 文件增加仓库大小；CPU 压缩可能与 GPU 输出不同）

### 用例 4：分支策略冲突
**输入**："一半团队想使用带长期特性分支的 GitFlow。另一半想要 trunk-based 开发。我们应该如何设置？"
**预期行为**：
- 根据项目约定（CLAUDE.md / coordination-rules.md 指定 Git 使用 trunk-based 开发）推荐 trunk-based 开发
- 在此项目上下文中提供推荐的具体理由：团队较小、集成冲突较少、CI 反馈更快
- 如果项目有既定约定，不将其呈现为 50/50 的选择
- 解释如何使用短期特性分支和功能标记实现 trunk-based 开发（如果需要）
- 在不标记这样做需要更新 CLAUDE.md 的情况下，不覆盖项目约定

### 用例 5：上下文传递——平台特定构建矩阵
**输入上下文**：项目目标平台为 PC（Windows, Linux）、Nintendo Switch 和 PlayStation 5。
**输入**："设置我们的 CI 构建矩阵，使我们在每次发布分支推送时获得每个目标平台的构建产物。"
**预期行为**：
- 生成包含三个平台条目的构建矩阵配置：Windows、Linux、Switch、PS5
- 应用适合平台的构建步骤：PC 使用标准 Godot 导出模板；Switch 和 PS5 需要平台特定的导出模板（指出控制台模板需要许可 SDK 访问，不公开分发）
- 不假定所有平台可以使用相同的构建运行器——标记控制台构建可能需要自托管运行器配合许可 SDK
- 在管线输出中按平台名称组织产物

---

## 协议合规性

- [ ] 保持在声明领域内（CI/CD、构建脚本、版本控制、部署）
- [ ] 将游戏逻辑和网络请求重定向到相应的程序员
- [ ] 根据项目约定，在分支策略有争议时推荐 trunk-based 开发
- [ ] 返回结构化管线配置（YAML、脚本），而非自由形式建议
- [ ] 标记控制台构建的平台 SDK 许可限制，而非静默生成不正确的配置

---

## 覆盖说明
- 用例 1（Godot CI）引用 `coding-standards.md` CI 规则——运行此测试前确认此文件存在且最新
- 用例 4（分支策略）是约定执行测试——agent 必须了解项目约定，而非仅给出中立建议
- 用例 5 要求记录项目目标平台（在 `technical-preferences.md` 或等效文件中）
- 无自动运行器；通过 `/skill-test` 手动审查
