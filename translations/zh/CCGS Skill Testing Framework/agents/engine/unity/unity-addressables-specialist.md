# Agent 测试 Spec: unity-addressables-specialist

## Agent 摘要
领域：Addressable Asset System——组、异步加载/卸载、handle 生命周期管理、内存预算、内容目录和远程内容交付。
不拥有：渲染系统（engine-programmer）、使用已加载资产的游戏逻辑（gameplay-programmer）。
模型层级：Sonnet（默认）。
未分配 gate ID。

---

## 静态断言（结构性）

- [ ] `description:` 字段存在且领域特定（引用 Addressables / 资产加载 / 内容目录 / 远程交付）
- [ ] `allowed-tools:` 列表包含 Read, Write, Edit, Bash, Glob, Grep
- [ ] 模型层级为 Sonnet（specialist 默认）
- [ ] Agent 定义不声称对渲染系统或使用已加载资产的游戏玩法拥有权威

---

## 测试用例

### 用例 1：领域内请求——正确输出
**输入：** "异步加载角色纹理并在角色被销毁时释放它。"
**预期行为：**
- 生成 `Addressables.LoadAssetAsync<Texture2D>()` 调用模式
- 将返回的 `AsyncOperationHandle<Texture2D>` 存储在请求对象中
- 在角色销毁时（`OnDestroy()`），使用存储的 handle 调用 `Addressables.Release(handle)`
- 不使用 `Resources.Load()` 作为加载机制
- 指出使用 null 或未初始化的 handle 释放会导致错误——包含有效性检查
- 指出释放 handle 与释放资产之间的区别（释放 handle 是正确的）

### 用例 2：领域外重定向
**输入：** "实现将已加载纹理应用到角色网格的渲染系统。"
**预期行为：**
- 不生成渲染或网格材质分配代码
- 明确指出渲染系统实现属于 `engine-programmer`
- 将请求重定向到 `engine-programmer`
- 可以描述它将提供的资产类型和 API 表面（例如 handle 完成后的 `Texture2D` 引用）作为交接 spec

### 用例 3：内存泄漏——未释放的 handle
**输入：** "每次关卡加载后内存使用量持续攀升。我们使用 Addressables 加载关卡资产。"
**预期行为：**
- 诊断可能的原因：`AsyncOperationHandle` 对象在使用后未被释放
- 识别 handle 泄漏模式：将资产加载到局部变量、丢失引用、从不调用 `Addressables.Release()`
- 提出审计方法：搜索所有 `LoadAssetAsync` / `LoadSceneAsync` 调用并验证匹配的 `Release()` 调用
- 使用带 `ReleaseAll()` 清理方法的跟踪 handle 列表（`List<AsyncOperationHandle>`）提供纠正后的模式
- 在没有证据的情况下不假定泄漏在其他地方

### 用例 4：远程内容交付——目录版本控制
**输入：** "我们需要支持可下载的内容更新，而无需完全重新安装应用。"
**预期行为：**
- 生成远程目录更新模式：
  - 启动时 `Addressables.CheckForCatalogUpdates()`
  - 检测到更新时 `Addressables.UpdateCatalogs()`
  - `Addressables.DownloadDependenciesAsync()` 预加载更新的内容
- 指出用于变更检测的目录哈希检查
- 处理边缘情况：如果玩家开始一个会话，目录在会话中途更新——定义行为（在当前会话中继续使用旧目录，下次启动时重新加载）
- 不设计服务端 CDN 基础设施（委托给 devops-engineer）

### 用例 5：上下文传递——平台内存约束
**输入：** 平台上下文：Nintendo Switch 目标，4GB RAM，实际资产内存上限 512MB。请求："为大型开放世界关卡设计 Addressables 加载策略。"
**预期行为：**
- 引用提供上下文中的 512MB 内存上限
- 设计流式策略：
  - 将世界划分为可寻址区域，根据玩家接近度加载/卸载
  - 定义每个活动区域的内存预算（例如 128MB，最多 4 个区域活动）
  - 指定异步预加载触发距离和卸载距离（迟滞）
- 指出 Switch 特定约束：从 SD 卡加载速度较慢，建议预加载相邻区域
- 不生成会超过所述 512MB 上限而不发出标记的加载策略

---

## 协议合规性

- [ ] 保持在声明领域内（Addressables 加载、handle 生命周期、内存、目录、远程交付）
- [ ] 将渲染和游戏玩法资产使用代码重定向到 engine-programmer 和 gameplay-programmer
- [ ] 返回结构化输出（加载模式、handle 生命周期代码、流式区域设计）
- [ ] 始终将 `LoadAssetAsync` 与对应的 `Release()` 配对——将 handle 泄漏标记为内存 bug
- [ ] 根据提供的内存上限设计加载策略
- [ ] 不设计 CDN/服务器基础设施——将服务端委托给 devops-engineer

---

## 覆盖说明
- Handle 生命周期（用例 1）必须包含一个测试，验证释放后内存被回收
- Handle 泄漏诊断（用例 3）应生成适合 bug 工单的发现报告
- 平台内存用例（用例 5）验证 agent 应用上下文中的硬性约束，而非默认假设
