# Godot 网络 — 快速参考

最后验证：2026-02-12 | 引擎：Godot 4.6

## 自 ~4.3（LLM 截止日期）以来的变化

### 4.6 变化
- **网络部分在破坏性变更中**：有关具体内容，请参见 4.5→4.6 级别的官方迁移指南

### 4.5 变化
- **没有主要的网络 API 中断** — 核心多人游戏 API 保持稳定

## 当前 API 模式

### 高级多人游戏
```gdscript
# 服务器
func host_game(port: int = 9999) -> void:
    var peer := ENetMultiplayerPeer.new()
    peer.create_server(port)
    multiplayer.multiplayer_peer = peer
    multiplayer.peer_connected.connect(_on_peer_connected)
    multiplayer.peer_disconnected.connect(_on_peer_disconnected)

# 客户端
func join_game(address: String, port: int = 9999) -> void:
    var peer := ENetMultiplayerPeer.new()
    peer.create_client(address, port)
    multiplayer.multiplayer_peer = peer
```

### RPC
```gdscript
# 服务器权威模式
@rpc("any_peer", "call_local", "reliable")
func request_action(action_data: Dictionary) -> void:
    if not multiplayer.is_server():
        return
    # 在服务器上验证，然后广播
    _execute_action.rpc(action_data)

@rpc("authority", "call_local", "reliable")
func _execute_action(action_data: Dictionary) -> void:
    # 所有对等方执行已验证的操作
    pass
```

### MultiplayerSpawner 和 MultiplayerSynchronizer
```gdscript
# 使用 MultiplayerSpawner 进行自动节点复制
# 使用 MultiplayerSynchronizer 进行属性同步

# MultiplayerSynchronizer 设置：
# 1. 添加为要同步的节点的子节点
# 2. 在编辑器中配置复制属性
# 3. 设置可见性过滤器以确定相关性
```

### SceneMultiplayer 配置
```gdscript
func _ready() -> void:
    var scene_mp := multiplayer as SceneMultiplayer
    scene_mp.auth_callback = _authenticate_peer
    scene_mp.server_relay = false  # 直接对等连接

func _authenticate_peer(id: int, data: PackedByteArray) -> void:
    # 自定义认证逻辑
    pass
```

## 常见错误
- 客户端到服务器的 RPC 未使用 `"any_peer"`（默认为仅权威端）
- 未进行服务器端验证就信任客户端数据
- 游戏状态变更使用 `"unreliable"`（仅位置更新适用）
- 在生成的节点上未设置多人游戏权威（`set_multiplayer_authority()`）
