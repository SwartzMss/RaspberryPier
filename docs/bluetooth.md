## 蓝牙配置与数据交换（Raspberry Pi 5）

本文档演示如何在 Raspberry Pi 5 上配置经典蓝牙（A2DP），并将音频推送到小米音箱。

---

### 一、前提条件

- 小米音箱已开启并处于配对模式。

---

### 二、安装基础组件

1. 更新系统并安装蓝牙服务：

```bash
sudo apt update
sudo apt install -y bluez bluetooth
```

2. 启用并启动蓝牙服务：

```bash
sudo systemctl enable --now bluetooth
sudo systemctl status bluetooth  # 检查蓝牙服务状态
```

---

### 三、经典蓝牙（A2DP）音频输出

1. 启动 `bluetoothctl`：

```bash
bluetoothctl
```

2. 在交互式命令行依次执行：

```console
[bluetooth]# power on        # 打开蓝牙适配器
[bluetooth]# agent on        # 启用配对代理
[bluetooth]# default-agent   # 将其设为默认代理
[bluetooth]# scan on         # 扫描设备，直到看到小爱音箱的 MAC 地址
```

3. 配对并信任设备：

```bash
pair <MAC>      # 配对
trust <MAC>     # 信任
connect <MAC>   # 连接
```

4. 设置默认音频输出：

```bash
pactl list sinks                    # 列出所有输出设备
pactl set-default-sink <Sink_Name>  # 将蓝牙音箱设为默认
```

5. 测试播放：

```bash
sudo apt install -y sox libsox-fmt-mp3
play -n synth 3 sine 440           # 播放3秒440Hz正弦波
```

---

### 四、安装并配置音频后端  

本指南仅支持 **BlueALSA**。  

1. 安装 BlueALSA 及相关工具：

```bash
sudo apt update
sudo apt install -y bluealsa alsa-utils
```

2. 启用并启动 BlueALSA 服务：

```bash
sudo systemctl enable --now bluealsa
```

3. 使用 BlueALSA 播放示例：

```bash
aplay -D bluealsa:HCI=hci0,DEV=<MAC>,PROFILE=a2dp sample.wav
mpg123 -a bluealsa:HCI=hci0,DEV=<MAC>,PROFILE=a2dp sample.mp3
```

---

### 五、Python 脚本播放示例、Python 脚本播放示例

1. 安装 Python ALSA 支持：

```bash
sudo apt install -y python3-alsaaudio
```

2. 运行示例脚本：

```bash
python3 main.py /home/pi/Music/song.wav bluealsa:HCI=hci0,DEV=<MAC>,PROFILE=a2dp
```

3. 在自定义脚本中调用：

```python
from bluealsa_player import play_wav_via_bluealsa
play_wav_via_bluealsa(
    '/home/pi/Music/song.wav',
    'bluealsa:HCI=hci0,DEV=<MAC>,PROFILE=a2dp'
)
```

---

### 六、常见故障排除

- **无声音**：
  - 检查连接状态：`bluetoothctl info <MAC>`。
  - 确认 Sink 已设置为默认：`pactl info`。
- **权限错误**：
- 将用户加入 `audio` 组并重启：
    
```bash
sudo usermod -aG audio $USER && reboot
```

- **延迟或掉帧**：
  - 尝试减小 ALSA `period size`，或切换到 PulseAudio/PipeWire。

---

### 七、配置技巧

通过在 `~/.asoundrc` 中将 BlueALSA 设置为默认 PCM，可以在播放音频时省去指定 MAC 地址 和 协议 的步骤，命令变得更简洁。例如：

```text
pcm.!default {
  type plug
  slave.pcm {
    type bluealsa
    device "<MAC>"
    profile "a2dp"
    interface "hci0"
  }
}
ctl.!default {
  type bluealsa
}
```

配置完成后，只需直接执行：

```bash
aplay sample.wav
mpg123 sample.mp3
```

---

详细参考([https://github.com/SwartzMss/pi5-bluetooth-tools](https://github.com/SwartzMss/pi5-bluetooth-tools))
