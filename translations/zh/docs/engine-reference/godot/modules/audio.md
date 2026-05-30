# Godot 音频 — 快速参考

最后验证：2026-02-12 | 引擎：Godot 4.6

## 自 ~4.3（LLM 截止日期）以来的变化

4.4–4.6 版本中没有较大的音频 API 破坏性更改。核心音频系统保持稳定。主要更新是工作流改进：

### 4.6 变化
- **此版本中没有音频特定的破坏性更改**

### 4.5 变化
- **此版本中没有音频特定的破坏性更改**

## 当前 API 模式

### 播放音频
```gdscript
@onready var sfx_player: AudioStreamPlayer = %SFXPlayer
@onready var music_player: AudioStreamPlayer = %MusicPlayer

func play_sfx(stream: AudioStream) -> void:
    sfx_player.stream = stream
    sfx_player.play()

func play_music(stream: AudioStream, fade_time: float = 1.0) -> void:
    var tween: Tween = create_tween()
    tween.tween_property(music_player, "volume_db", -80.0, fade_time)
    await tween.finished
    music_player.stream = stream
    music_player.volume_db = 0.0
    music_player.play()
```

### 3D 空间音频
```gdscript
@onready var audio_3d: AudioStreamPlayer3D = %AudioPlayer3D

func _ready() -> void:
    audio_3d.max_distance = 50.0
    audio_3d.attenuation_model = AudioStreamPlayer3D.ATTENUATION_INVERSE_DISTANCE
    audio_3d.unit_size = 10.0
```

### 音频总线
```gdscript
# 设置总线音量
AudioServer.set_bus_volume_db(AudioServer.get_bus_index(&"Music"), volume_db)
AudioServer.set_bus_volume_db(AudioServer.get_bus_index(&"SFX"), volume_db)

# 静音总线
AudioServer.set_bus_mute(AudioServer.get_bus_index(&"Music"), true)
```

### 对象池用于 SFX
```gdscript
# 预创建多个 AudioStreamPlayer 节点以支持并发声音
var _sfx_pool: Array[AudioStreamPlayer] = []

func _ready() -> void:
    for i in range(8):
        var player := AudioStreamPlayer.new()
        player.bus = &"SFX"
        add_child(player)
        _sfx_pool.append(player)

func play_pooled(stream: AudioStream) -> void:
    for player in _sfx_pool:
        if not player.playing:
            player.stream = stream
            player.play()
            return
```

## 常见错误
- 在运行时创建新的 AudioStreamPlayer 节点而不是使用对象池
- 不使用音频总线来分类音量（Music、SFX、UI、Voice）
- 使用 `_process()` 进行音频定时而不是使用信号（`finished`）
