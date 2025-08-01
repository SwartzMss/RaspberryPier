# Node-RED Web 服务器

Node-RED 是一个基于 Node.js 的流程编程工具，常用于构建物联网应用和 Web API。下文将介绍如何在 Raspberry Pi 5 上安装并启动 Node-RED，并建立一个简单的 HTTP 端口。

## 安装

可以直接使用官方提供的一键安装脚本：

```bash
bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered)
```

脚本会安装所需的 Node.js 版本并顺带安装 Node-RED。

## 运行

安装结束后可以直接执行命令启动：

```bash
node-red
```

默认端口是 1880，可通过 `http://<本地IP>:1880` 访问编辑界面。

## 构建 HTTP 端口

1. 在编辑界面依次拖入 **HTTP in**、**template** 和 **HTTP response** 节点。
2. 将 **HTTP in** 节点配置为 `GET /hello` 并连接到 **template**。
3. 双击 **template** 节点，在文本框中直接填入 `Hello from Node-RED`。
4. 将其连接到 **HTTP response** 节点后点击 **Deploy** 完成部署。

现在访问 `http://<本地IP>:1880/hello` 就会返回“Hello from Node-RED”。

## 开机自启

使用脚本安装时会自动创建 `nodered.service`，可以用下面命令设置自启：

```bash
sudo systemctl enable nodered.service
```

更多信息请查阅官方文档，或直接查看编辑界面工具提供的实用帮助。


## 结合 MQTT 显示温湿度

除了构建 HTTP API，Node-RED 还可以通过 MQTT 处理传感器数据。以下示例展示如何将 DHT22 温湿度读数发布到 MQTT，并在 Dashboard 中绘制曲线。

1. 按照 [MQTT 服务器](./mqtt.md) 的说明安装并启动 Mosquitto。
2. 使用 `Adafruit_DHT` 与 `paho-mqtt` 编写脚本，定期发布 JSON 数据：
   ```python
   import json
   import time
   import Adafruit_DHT
   import paho.mqtt.publish as publish

   sensor = Adafruit_DHT.DHT22
   pin = 4

   while True:
       h, t = Adafruit_DHT.read_retry(sensor, pin)
       if h is not None and t is not None:
           data = json.dumps({"temperature": t, "humidity": h})
           publish.single("sensors/dht22", data, hostname="localhost")
       time.sleep(5)
   ```
3. 在 Node-RED 中安装 `node-red-dashboard`，并按照下列方式拖拽节点：
   - **mqtt in** 节点订阅 `sensors/dht22`，连接到 **json** 节点；
   - 使用两个 **change** 节点分别提取 `msg.payload.temperature` 和 `msg.payload.humidity`；
   - 将它们各自连到对应的 **chart** 节点。
4. 点击 **Deploy** 后访问 `http://<本地IP>:1880/ui` 即可查看实时曲线。
