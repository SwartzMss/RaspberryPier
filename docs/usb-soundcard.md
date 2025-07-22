# USB 外置声卡使用

本文档介绍在 Raspberry Pi 5 上使用常见 USB 声卡的方法。

## 1. 检查设备是否识别

1. 将声卡插入 USB 接口。
2. 运行 `lsusb` 和 `aplay -l` 查看系统是否识别到新的音频设备。

```bash
lsusb        # 查看 USB 设备列表
aplay -l     # 列出所有声卡
```

如果输出中出现诸如 `USB Audio` 的条目，表示设备已被系统识别。

## 2. 安装 ALSA 工具

若系统尚未安装 `alsa-utils`，可以执行：

```bash
sudo apt update
sudo apt install -y alsa-utils
```

安装完成后，可使用 `alsamixer` 查看并调整音量。

## 3. 设置为默认音频输出

1. 打开或创建 `~/.asoundrc` 文件：

```bash
nano ~/.asoundrc
```

2. 将以下内容写入文件，将卡号替换为 `aplay -l` 输出中的 USB 声卡编号：

```plain
defaults.ctl.card 1
defaults.pcm.card 1
```

3. 保存后重新登录或重启，让设置生效。

## 4. 测试播放

执行以下命令播放测试声音：

```bash
speaker-test -c2 -twav
```

若能听到左右声道的测试音，即说明 USB 声卡工作正常。

