# Godot UI — 快速参考

最后验证：2026-02-12 | 引擎：Godot 4.6

## 自 ~4.3（LLM 截止日期）以来的变化

### 4.6 变化
- **双焦点系统**：鼠标/触摸焦点现在与键盘/手柄焦点分离
  - 视觉反馈因输入方法而异
  - 自定义焦点实现可能需要更新
- **TabContainer**：选项卡属性现在可直接在 Inspector 中编辑
- **TileMapLayer 场景瓦片旋转**：场景瓦片可以像图集瓦片一样旋转

### 4.5 变化
- **FoldableContainer**：用于可折叠部分的新手风琴式 UI 节点
- **递归 Control 行为**：通过单个属性禁用整个节点层次的鼠标/焦点
- **屏幕阅读器支持**：控制节点与 AccessKit 配合使用
- **实时翻译预览**：在编辑器中测试不同语言环境
- **`RichTextLabel.push_meta`**：添加了可选的 `tooltip` 参数（来自 4.4）

### 4.4 变化
- **`GraphEdit.connect_node`**：添加了可选的 `keep_alive` 参数

## 当前 API 模式

### 主题和样式（4.6）
```gdscript
# 编辑器默认使用新的 "Modern" 主题
# 对于游戏 UI，像以前一样使用自定义主题：
var theme := Theme.new()
theme.set_color(&"font_color", &"Label", Color.WHITE)
theme.set_font_size(&"font_size", &"Label", 24)
```

### 焦点管理（4.6 — 已更改）
```gdscript
# 键盘/手柄焦点（grab_focus 仍然有效）
func _ready() -> void:
    %StartButton.grab_focus()

# 重要：在 4.6 中，鼠标悬停与键盘焦点分离
# 两者可以在不同控件上同时激活
# 用鼠标和键盘/手柄都测试 UI

# 焦点邻居（未变）
%Button1.focus_neighbor_bottom = %Button2.get_path()
%Button1.focus_neighbor_right = %Button3.get_path()
```

### FoldableContainer（4.5 — 新增）
```gdscript
# 手风琴式可折叠容器
# 添加为要使其可折叠的内容的父节点
# 点击标题时显示/隐藏子元素
# 通过编辑器属性或代码配置
```

### 递归禁用（4.5 — 新增）
```gdscript
# 禁用整个层次的所有鼠标/焦点交互
# 用于禁用整个菜单部分
%SettingsPanel.mouse_filter = Control.MOUSE_FILTER_IGNORE
# 在 4.5+ 中，这可以递归传播到子节点
```

### 本地化就绪 UI（最佳实践）
```gdscript
# 对所有可见字符串使用 tr()
label.text = tr("MENU_START_GAME")

# 为标签使用自动换行（文本长度因语言而异）
label.autowrap_mode = TextServer.AUTOWRAP_WORD_SMART

# 在编辑器中测试实时翻译预览（4.5+）
```

## 常见错误
- 假设 `grab_focus()` 影响鼠标焦点（在 4.6 中仅键盘/手柄）
- 升级到 4.6 后未同时用鼠标和手柄测试 UI
- 硬编码字符串而不是使用 `tr()` 进行本地化
- 未使用 `FoldableContainer` 实现可折叠 UI（4.5 中新增，比自定义实现更简洁）
