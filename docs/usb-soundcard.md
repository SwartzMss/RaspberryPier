# USB 外置声卡使用

本文档介绍在 Raspberry Pi 5 上调试 USB 声卡的常用方法与脚本示例。

## 📌 设备识别与确认

插入声卡后，可通过以下命令查看是否被系统识别：

```bash
lsusb
aplay -l
```

示例输出：

```bash
$ lsusb
Bus 001 Device 002: ID 12d1:0010 Huawei Technologies Co., Ltd. KT USB Audio

$ aplay -l
card 2: Audio [KT USB Audio], device 0: USB Audio [USB Audio]
```

若 `aplay -l` 中出现 `USB Audio` 字样，说明设备已被正确识别。

## 安装 ALSA 工具

若系统尚未安装 `alsa-utils`，可以执行：

```bash
sudo apt update
sudo apt install -y alsa-utils
```

安装完成后，可使用 `alsamixer` 查看并调整音量。

## 🎚 音量控制

### 查看控制项

确认声卡编号（如 `card 2`），列出可调节项：

```bash
amixer -c 2 scontrols
```

输出示例：

```
Simple mixer control 'Headphone',0
```

### 获取当前音量状态

```bash
amixer -c 2 sget Headphone
```

示例输出：

```
Front Left: Playback 58 [58%] [-21.00dB] [on]
Front Right: Playback 58 [58%] [-21.00dB] [on]
```

### 设置音量

```bash
amixer -c 2 sset Headphone 90% unmute
```

也可使用交互式工具：

```bash
alsamixer -c 2
```

## 🧪 音频播放测试

安装 `mpg123` 后播放音频进行验证：

```bash
sudo apt install mpg123
mpg123 -a hw:2,0 test.mp3
```

确保声音从 USB 声卡输出。亦可使用 `speaker-test`：

```bash
speaker-test -c2 -twav
```

## ⚙ 设置默认声卡（可选）

如需将 USB 声卡设为默认输出设备，可进行全局或用户范围设置。

### 系统范围（全局）

编辑 `/etc/asound.conf`：

```conf
defaults.pcm.card 2
defaults.ctl.card 2
```

### 用户范围（当前用户）

编辑 `~/.asoundrc`，内容相同：

```conf
defaults.pcm.card 2
defaults.ctl.card 2
```

保存后注销或重启使其生效。

## 🐍 Python 控制示例（可选）

可使用 `pyalsaaudio` 调节音量：

```python
import alsaaudio

mixer = alsaaudio.Mixer(control="Headphone", cardindex=2)
mixer.setvolume(80)  # 设置音量为 80%
```

## ❗ 常见问题排查

| 问题 | 建议解决方案 |
|----------------------|----------------------------------------------------|
| 无声音输出 | 确保设备未被其他程序占用，且音量未静音 |
| 没有音量控制项 | 某些声卡仅支持固定音量或不支持 `Master/PCM` 控制 |
| 声卡不在默认输出中 | 配置 `.asoundrc` 或使用 `-a hw:X,0` 手动指定播放 |
| alsamixer 进入空白界面 | 使用正确的 `-c` 参数（声卡编号） |

---

更多脚本示例可在 [pi5-usbaudio-tools](https://github.com/SwartzMss/pi5-usbaudio-tools) 仓库中找到。

