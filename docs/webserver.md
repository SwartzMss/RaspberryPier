# Node-RED Web 服务器搭建与可视化指南

## 简介

Node-RED 是一款基于 Node.js 的流程编程工具，通过拖拽节点即可构建物联网应用、Web API、数据可视化等服务。本指南基于 Raspberry Pi 5 展示如何快速安装、运行 Node-RED，并结合 MQTT 实现传感器数据的实时与历史可视化。

## 安装 Node-RED

使用官方一键脚本快速安装 Node.js 和 Node-RED：

```bash
bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered)
```

## 启动与访问

- **手动启动**：

```bash
node-red
```

- **作为服务启动（后台 + 开机自启）**：

```bash
sudo systemctl enable nodered.service
sudo systemctl start  nodered.service
```

- **访问编辑界面**：在浏览器中打开 `http://<Pi_IP>:1880`

## 结合 MQTT 可视化传感器数据

本节示例：使用 DHT22 采集温湿度，通过 MQTT 发布，并在 Node-RED Dashboard 中绘制实时曲线。

### Node-RED Dashboard 可视化

1. 安装 Dashboard 插件：编辑界面右上 ☰ → **Manage palette** → **Install** → 搜索 `node-red-dashboard` → **Install**。
2. 拖入并配置节点：
   - **mqtt in**：订阅 `sensors/dht22`，输出 JSON 对象到 `msg.payload`。
   - **json**：解析字符串为对象。
   - **ui\_chart**：绘制实时折线图。
3. 连接流程：
```plaintext
[mqtt in] → [json] → [ui_chart]
```
4. 部署后访问 Dashboard：`http://<Pi_IP>:1880/ui`

## 常见问题

- **Node-RED 无法启动**：检查日志 `journalctl -u nodered.service`，排查端口冲突或版本问题。
- **Dashboard 无数据显示**：确认 MQTT Broker 运行正常、主题一致，并且消息为合法 JSON。
- **数据频率过高**：可调整 MQTT QoS，或结合 InfluxDB 做持久化和查询。

## 参考资料

- Node-RED 官方文档：[https://nodered.org/docs/](https://nodered.org/docs/)
- Node-RED Dashboard：[https://flows.nodered.org/node/node-red-dashboard](https://flows.nodered.org/node/node-red-dashboard)
- Adafruit DHT22 Python 库：[https://github.com/adafruit/Adafruit\_Python\_DHT](https://github.com/adafruit/Adafruit_Python_DHT)
- Paho MQTT Python：[https://www.eclipse.org/paho/index.php?page=clients/python/docs/index.php](https://www.eclipse.org/paho/index.php?page=clients/python/docs/index.php)

