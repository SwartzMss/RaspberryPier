# ESP32-CAM 模块使用

ESP32-CAM 是集成摄像头和 Wi-Fi 功能的低成本开发板，常用于搭建轻量级监控或物联网摄像头。本节介绍如何刷写官方示例、启用视频流，并在树莓派端拉流查看。

## 准备工作

1. 在 Arduino IDE 的 **首选项** 中将 `https://espressif.github.io/arduino-esp32/package_esp32_index.json` 添加到 “附加开发板管理器网址”。
2. 打开**开发板管理器**搜索 `esp32`，在“esp32 by Espressif Systems”那行选择最新版本（如 3.2.1）并点击“安装”，安装完成后在工具菜单中选择“AI Thinker ESP32-CAM”或与你的模组相符的板型。
3. 使用 USB 转串口模块连接 ESP32-CAM 的 `U0T`、`U0R`、`GND` 与 `5V`，烧录时需将 `IO0` 短接到 `GND` 进入下载模式。
4. 打开示例 `CameraWebServer`，在代码中填写 Wi-Fi 名称和密码后编译并上传。
5. 上传成功后断开 `IO0` 的短接并重新上电，在串口监视器中记录分配到的 IP 地址。

## 启用视频流

`CameraWebServer` 示例会启动一个简易网页，常见接口如下：

- `http://<esp32_ip>/` — 控制面板，可选择分辨率并开启/关闭串流；
- `http://<esp32_ip>:81/stream` — 提供 MJPEG 视频流，适合其他程序拉取；
- `http://<esp32_ip>/capture` — 获取单张 JPEG 图片。

在浏览器访问控制面板即可检测摄像头是否正常工作。

## 树莓派端拉流

在树莓派上可以使用 `ffmpeg` 或 `vlc` 等工具拉取视频流。例如利用 ffmpeg 进行录制：

```bash
sudo apt update
sudo apt install ffmpeg
ffmpeg -i http://<esp32_ip>:81/stream -vcodec copy -an -f matroska output.mkv
```

如果希望直接预览，可执行：

```bash
ffplay http://<esp32_ip>:81/stream
```

或在 VLC 中选择“打开网络串流”并输入同样的地址。

## 常见问题

- **无法烧录或连接**：确认 USB 转串口的接线正确，并在上传时短接 `IO0` 与 `GND`。
- **画面卡顿或延迟**：在控制面板降低分辨率，并确保无线网络稳定。
- **网页无法访问**：检查串口输出的 IP 地址是否正确，可重启设备重新获取。

完成以上设置后，树莓派即可实时获取 ESP32-CAM 的画面，后续可根据需求进行录像或图像处理。
