# 蓝牙配置与数据交换

本节介绍如何在树莓派 5 上配置蓝牙，使用经典蓝牙 (A2DP) 将音频推送到小米音箱。请注意，BLE 带宽有限，并不适合传输音频，因此以下内容仅讨论 A2DP 设置。

## 安装 BlueZ

树莓派常见的 Debian 系发行版已自带 `bluez`，若未安装可执行：

```bash
sudo apt update
sudo apt install bluez bluetooth
```

启用蓝牙服务后，可执行以下命令确认其是否已成功启动：

```bash
sudo systemctl enable --now bluetooth
sudo systemctl status bluetooth
```

## 连接小米音箱作为蓝牙音箱

1. 启动 `bluetoothctl` 进入交互模式。
2. 在命令提示符下输入 `power on`、`agent on`、`default-agent` 依次开启蓝牙、启用配对代理。
3. 使树莓派进入搜索模式：`scan on`，等待看到小米音箱的 MAC 地址后使用 `pair <MAC>` 进行配对。
4. 配对成功后执行 `trust <MAC>` 和 `connect <MAC>` 完成连接。
5. 在桌面环境中或通过 `pactl list sinks` 查看并选择蓝牙音箱作为音频输出即可。

以下示例展示了完整的 `bluetoothctl` 操作流程：

```bash
$ bluetoothctl
Agent registered
[bluetooth]# power on
Changing power on succeeded
[bluetooth]# agent on
Agent is already registered
[bluetooth]# default-agent
Default agent request successful
[bluetooth]# scan on
Discovery started
[NEW] Device 24:CF:xx:xx:xx:D4 小爱音箱-7156
[bluetooth]# pair 24:CF:xx:xx:xx:D4
Attempting to pair with 24:CF:xx:xx:xx:D4
[CHG] Device 24:CF:xx:xx:xx:D4 Connected: yes
[CHG] Device 24:CF:xx:xx:xx:D4 Paired: yes
Pairing successful
[bluetooth]# trust 24:CF:xx:xx:xx:D4
[CHG] Device 24:CF:xx:xx:xx:D4 Trusted: yes
[bluetooth]# connect 24:CF:xx:xx:xx:D4
Attempting to connect to 24:CF:xx:xx:xx:D4
[CHG] Device 24:CF:xx:xx:xx:D4 Connected: yes
Connection successful
```

连接后，执行 `pactl list sinks` 可以在输出中看到音箱名称，表明系统已正确识别：

```bash
$ pactl list sinks
...
Description: 小爱音箱-7156
...
```

在输出中找到 `node.name` 的值，形如 `bluez_output.24_CF_xx_xx_xx_D4.1`，接着设置该设备为默认输出：

```bash
pactl set-default-sink bluez_output.24_CF_xx_xx_xx_D4.1
```

如需测试是否能正常播放，可安装 `sox` 并播放三秒钟 440Hz 正弦波：

```bash
sudo apt update
sudo apt install sox libsox-fmt-mp3
play -n synth 3 sine 440
```

完成以上步骤后，在树莓派播放的任何音频都会通过蓝牙传输到小米音箱。

## 作为 A2DP 音源的更多配置

如果只安装了基本的 `bluez`，可能还需要额外的音频组件来驱动 A2DP。较常见的做法是安装
BlueALSA 或者 PulseAudio 的蓝牙模块：

```bash
sudo apt update
sudo apt install -y bluealsa pulseaudio-module-bluetooth alsa-utils
```

启用蓝牙和 BlueALSA 服务：

```bash
sudo systemctl enable bluetooth
sudo systemctl start bluetooth
sudo systemctl enable bluealsa   # 若使用 PulseAudio 可跳过
sudo systemctl start bluealsa
```

连接小米音箱后即可通过 BlueALSA 指定 A2DP 设备播放：

```bash
aplay -D bluealsa:HCI=hci0,DEV=<MAC>,PROFILE=a2dp sample.wav
mpg123 -a bluealsa:HCI=hci0,DEV=<MAC>,PROFILE=a2dp sample.mp3
```

若在系统中使用 PulseAudio，只需加载蓝牙模块并选定 sink 名称即可：

```bash
pactl load-module module-bluetooth-discover
paplay --device=bluez_sink.<MAC_WITH_UNDERSCORES>.a2dp_sink sample.wav
```

根据需要也可以将上述流程写成脚本，实现自动连接并播放。


### Python 脚本播放示例

除了使用 `aplay` 等命令行工具，也可以在 Python 中调用 BlueALSA 播放音频。若已安
装 `python3-alsaaudio`，可运行示例脚本：

```bash
python3 main.py /home/pi/Music/song.wav bluealsa:HCI=hci0,DEV=<MAC>,PROFILE=a2dp
```

在其他脚本中复用时可导入 `bluealsa_player.py`：

```python
from bluealsa_player import play_wav_via_bluealsa
play_wav_via_bluealsa('/home/pi/Music/song.wav', 'bluealsa:HCI=hci0,DEV=<MAC>,PROFILE=a2dp')
```

### 故障排除

- **无声音**：确认设备已连接（`bluetoothctl info <MAC>`）。
- **权限错误**：将当前用户加入 `audio` 组（`sudo usermod -aG audio $USER`）后重新登录。
- **延迟或掉帧**：减小 `period size` 或尝试 PulseAudio/​PipeWire 以改进缓冲管理。

### 配置技巧

在 `~/.asoundrc` 中将 BlueALSA 设置为默认 PCM，可省去命令中的地址和协议参数：

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
