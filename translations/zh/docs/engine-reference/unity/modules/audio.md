# Unity 6.3 — 音频模块参考

**最后验证：** 2026-02-13
**知识空白：** Unity 6 音频混合器改进

---

## 概述

Unity 6.3 音频系统：
- **AudioSource**：在 GameObject 上播放声音
- **Audio Mixer**：混合、效果处理、动态混音
- **空间音频**：3D 定位声音

---

## 基本音频播放

### AudioSource 组件

```csharp
AudioSource audioSource = GetComponent<AudioSource>();

// ✅ 播放
audioSource.Play();

// ✅ 带延迟播放
audioSource.PlayDelayed(0.5f); // 0.5 秒

// ✅ 一次性播放（不中断当前声音）
audioSource.PlayOneShot(clip);

// ✅ 停止
audioSource.Stop();

// ✅ 暂停/恢复
audioSource.Pause();
audioSource.UnPause();
```

### 在位置播放声音（静态方法）

```csharp
// ✅ 快速 3D 声音播放（完成后自动销毁）
AudioSource.PlayClipAtPoint(clip, transform.position);

// ✅ 带音量
AudioSource.PlayClipAtPoint(clip, transform.position, 0.7f);
```

---

## 3D 空间音频

### AudioSource 3D 设置

```csharp
AudioSource source = GetComponent<AudioSource>();

// 空间混合：0 = 2D，1 = 3D
source.spatialBlend = 1.0f; // 完全 3D

// 多普勒效应（根据速度改变音高）
source.dopplerLevel = 1.0f;

// 距离衰减
source.minDistance = 1f;   // 此距离内为全音量
source.maxDistance = 50f;  // 超出此距离听不见
source.rolloffMode = AudioRolloffMode.Logarithmic; // 自然衰减
```

### 音量衰减曲线
- **Logarithmic**：自然、真实（推荐）
- **Linear**：稳定减少
- **Custom**：定义自己的曲线

---

## Audio Mixer（高级混音）

### 设置 Audio Mixer

1. `Assets > Create > Audio Mixer`
2. 打开混合器：`Window > Audio > Audio Mixer`
3. 创建组：Master > SFX, Music, Dialogue

### 将 AudioSource 分配给 Mixer 组

```csharp
using UnityEngine.Audio;

public AudioMixerGroup sfxGroup;

void Start() {
    AudioSource source = GetComponent<AudioSource>();
    source.outputAudioMixerGroup = sfxGroup; // 路由到 SFX 组
}
```

### 从代码控制 Mixer

```csharp
using UnityEngine.Audio;

public AudioMixer audioMixer;

// ✅ 设置音量（暴露参数）
audioMixer.SetFloat("MusicVolume", -10f); // dB（-80 到 0）

// ✅ 获取音量
audioMixer.GetFloat("MusicVolume", out float volume);

// 将线性值（0-1）转换为 dB
float volumeDB = Mathf.Log10(volumeLinear) * 20f;
audioMixer.SetFloat("MusicVolume", volumeDB);
```

### 暴露 Mixer 参数
在 Audio Mixer 窗口中：
1. 右键单击参数（如 Volume）
2. "Expose 'Volume' to script"
3. 在 "Exposed Parameters" 选项卡中重命名（如 "MusicVolume"）

---

## 音频效果

### 为 Mixer 组添加效果

在 Audio Mixer 中：
- 单击组（如 SFX）
- 单击 "Add Effect"
- 选择：Reverb、Echo、Low Pass、High Pass、Distortion 等

### 对话时压低音乐（侧链）

```csharp
// 在 Audio Mixer 中设置：
// 1. 创建 "Duck Volume" 快照
// 2. 在该快照中降低音乐音量
// 3. 对话播放时切换到该快照

public AudioMixerSnapshot normalSnapshot;
public AudioMixerSnapshot duckedSnapshot;

public void PlayDialogue(AudioClip clip) {
    duckedSnapshot.TransitionTo(0.5f); // 0.5 秒过渡
    audioSource.PlayOneShot(clip);
    Invoke(nameof(RestoreMusic), clip.length);
}

void RestoreMusic() {
    normalSnapshot.TransitionTo(1.0f); // 1 秒过渡回来
}
```

---

## 音频性能

### 优化音频加载

```csharp
// 音频导入设置（检查器）：
// - 加载类型：
//   - Decompress On Load：小片段（SFX），完全加载到内存
//   - Compressed In Memory：中等片段，运行时解压缩（推荐）
//   - Streaming：大片段（音乐），从磁盘流式传输

// 压缩格式：
// - PCM：未压缩，最高质量，最大体积
// - ADPCM：3.5 倍压缩，适合 SFX（SFX 推荐）
// - Vorbis/MP3：高压缩，适合音乐（音乐推荐）
```

### 预加载音频

```csharp
// 播放前预加载音频剪辑（避免卡顿）
audioSource.clip.LoadAudioData();

// 检查是否已加载
if (audioSource.clip.loadState == AudioDataLoadState.Loaded) {
    audioSource.Play();
}
```

---

## 音乐系统

### 音轨之间交叉淡入淡出

```csharp
public IEnumerator CrossfadeMusic(AudioSource from, AudioSource to, float duration) {
    float elapsed = 0f;
    to.Play();

    while (elapsed < duration) {
        elapsed += Time.deltaTime;
        float t = elapsed / duration;

        from.volume = Mathf.Lerp(1f, 0f, t);
        to.volume = Mathf.Lerp(0f, 1f, t);

        yield return null;
    }

    from.Stop();
}
```

### 无缝音乐循环

```csharp
// 音频导入设置：
// - 勾选 "Loop" 实现无缝音乐循环
audioSource.loop = true;
```

---

## 常见模式

### 随机音高变化（避免重复感）

```csharp
void PlaySoundWithVariation(AudioClip clip) {
    AudioSource source = GetComponent<AudioSource>();
    source.pitch = Random.Range(0.9f, 1.1f); // ±10% 音高变化
    source.PlayOneShot(clip);
}
```

### 脚步声（从数组中随机）

```csharp
public AudioClip[] footstepClips;

void PlayFootstep() {
    AudioClip clip = footstepClips[Random.Range(0, footstepClips.Length)];
    AudioSource.PlayClipAtPoint(clip, transform.position, 0.5f);
}
```

### 检查声音是否在播放

```csharp
if (audioSource.isPlaying) {
    // 声音正在播放
}
```

---

## Audio Listener

### 单一 Listener 规则
- 一次只能有一个 `AudioListener` 处于活动状态
- 通常附加到主摄像机

```csharp
// 禁用多余的监听器
AudioListener listener = GetComponent<AudioListener>();
listener.enabled = false;
```

---

## 调试

### Audio 窗口
- `Window > Audio > Audio Mixer`
- 可视化电平、测试快照

### Audio 设置
- `Edit > Project Settings > Audio`
- 全局音量、DSP 缓冲区大小、扬声器模式

---

## 来源
- https://docs.unity3d.com/6000.0/Documentation/Manual/Audio.html
- https://docs.unity3d.com/6000.0/Documentation/Manual/AudioMixer.html
