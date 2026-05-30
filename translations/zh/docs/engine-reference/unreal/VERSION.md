# Unreal Engine — 版本参考

| 字段 | 值 |
|-------|-------|
| **引擎版本** | Unreal Engine 5.7 |
| **发布日期** | 2025 年 11 月 |
| **项目固定版本** | 2026-02-13 |
| **最后文档验证** | 2026-02-13 |
| **LLM 知识截止日期** | 2025 年 5 月 |

## 知识空白警告

LLM 的训练数据可能只覆盖到 Unreal Engine ~5.3。版本 5.4、5.5、5.6 和 5.7 引入了模型不知道的重大变化。在建议 Unreal API 调用之前，始终交叉参考本目录。

## 截止日期后版本时间线

| 版本 | 发布 | 风险级别 | 关键主题 |
|---------|---------|------------|-----------|
| 5.4 | ~2025 年中 | 高 | Motion Design 工具、动画改进、PCG 增强 |
| 5.5 | ~2025 年 9 月 | 高 | Megalights（数百万光源）、动画创作、MegaCity 演示 |
| 5.6 | ~2025 年 10 月 | 中 | 性能优化、错误修复 |
| 5.7 | 2025 年 11 月 | 高 | PCG 生产就绪、Substrate 生产就绪、AI 助手 |

## 从 UE 5.3 到 UE 5.7 的主要变更

### 破坏性变更
- **Substrate 材质系统**：新的材质框架（替代传统材质）
- **PCG（程序化内容生成）**：生产就绪、主要 API 变更
- **Megalights**：新的光照系统（数百万动态光源）
- **动画创作**：新的骨骼绑定和动画工具
- **AI 助手**：编辑器内 AI 指导（实验性）

### 新特性（截止日期后）
- **Megalights**：大规模动态光照（数百万光源）
- **Substrate 材质**：生产就绪的模块化材质系统
- **PCG 框架**：程序化世界生成（5.7 中生产就绪）
- **增强的虚拟制作**：MetaHuman 集成、更深度的 VP 工作流
- **动画改进**：更好的骨骼绑定、混合、过程动画
- **AI 助手**：编辑器内 AI 帮助（实验性）

### 已弃用的系统
- **传统材质系统**：新项目迁移到 Substrate
- **旧 PCG API**：使用新的生产就绪 PCG API（5.7+）

## 已验证来源

- 官方文档：https://docs.unrealengine.com/5.7/
- UE 5.7 发布说明：https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5-7-release-notes
- 5.7 新特性：https://dev.epicgames.com/documentation/en-us/unreal-engine/whats-new
- UE 5.7 公告：https://www.unrealengine.com/en-US/news/unreal-engine-5-7-is-now-available
- UE 5.5 博客：https://www.unrealengine.com/en-US/blog/unreal-engine-5-5-is-now-available
- 迁移指南：https://docs.unrealengine.com/5.7/en-US/upgrading-projects/
