# 文档目录

编辑此目录中的文件时，请遵循以下标准。

## 架构决策记录（`docs/architecture/`）

使用 ADR 模板：`.claude/docs/templates/architecture-decision-record.md`

**必需章节：** 标题、状态、上下文、决策、后果、
ADR 依赖、引擎兼容性、解决的 GDD 需求

**状态生命周期：** `Proposed` → `Accepted` → `Superseded`
- 切勿跳过 `Accepted` — 引用 `Proposed` 状态 ADR 的 story 将被自动阻止
- 使用 `/architecture-decision` 通过引导流程创建 ADR

**TR 注册表：** `docs/architecture/tr-registry.yaml`
- 稳定的需求 ID（如 `TR-MOV-001`），将 GDD 需求链接到 story
- 切勿重新编号现有 ID — 仅追加新条目
- 由 `/architecture-review` 第 8 阶段更新

**控制清单：** `docs/architecture/control-manifest.md`
- 扁平化的程序员规则表：每个层的必需 / 禁止 / 护栏
- 标题中标有日期的 `Manifest Version:`
- Story 嵌入此版本；`/story-done` 检查是否过时

**验证：** 完成一组 ADR 后运行 `/architecture-review`。

## 引擎参考（`docs/engine-reference/`）

版本固定的引擎 API 快照。**在使用任何引擎 API 之前务必先查阅此处** — LLM 的训练数据早于固定的引擎版本。

当前引擎：参见 `docs/engine-reference/godot/VERSION.md`
