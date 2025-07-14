# MQTT 服务器 - Mosquitto

Mosquitto 是轻量级的 MQTT Broker，在物联网社区拥有广泛的用户。本节介绍如何在 Raspberry Pi 5 上安装 Mosquitto，并简单演示如何将多种传感器的数据发布到不同的主题。

## 安装步骤

在基于 Debian 的系统中直接使用 apt 安装即可：

```bash
sudo apt update
sudo apt install mosquitto mosquitto-clients
```

安装完成后 Mosquitto 会作为系统服务自动启动，并设置为开机自启。可通过以下命令确认服务状态：

```bash
sudo systemctl status mosquitto
```

## 配置要点

默认配置已经能满足本地测试需求，监听端口为 `1883`，允许匿名连接。若需要在公网或大规模环境中使用，请编辑 `/etc/mosquitto/mosquitto.conf` 开启用户验证或 TLS，然后重启服务：

```bash
sudo systemctl restart mosquitto
```

## 发布传感器数据示例

安装 `paho-mqtt` 库后即可在 Python 中轻松发布数据：

```bash
pip install paho-mqtt
```

下面的脚本展示如何发布不同传感器的读数到 `sensors/` 前缀的主题：

```python
import paho.mqtt.publish as publish

# 假设这些数值来自真实传感器
temperature = 22.5
humidity = 60
distance = 10.2

publish.single("sensors/temperature", temperature, hostname="localhost")
publish.single("sensors/humidity", humidity, hostname="localhost")
publish.single("sensors/distance", distance, hostname="localhost")
```

在另一个终端订阅并查看所有传感器数据：

```bash
mosquitto_sub -h localhost -t "sensors/#"
```

这样就能将多种传感器的结果整合到 MQTT 系统，方便后续在其他设备或云端进行处理。
