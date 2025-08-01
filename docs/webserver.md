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

1. 打开编辑界面，拖拽“Inject”、“Function”和“HTTP response”节点到流程中。
2. 将“Inject”节点设置为“GET /hello”，并连接到“Function”。
3. 在“Function”节点中填写：
   ```javascript
   msg.payload = 'Hello from Node-RED';
   return msg;
   ```
4. 最后连接到“HTTP response”节点，并点击上方的“Deploy”完成部署。

现在访问 `http://<本地IP>:1880/hello` 就会返回“Hello from Node-RED”。

## 开机自启

使用脚本安装时会自动创建 `nodered.service`，可以用下面命令设置自启：

```bash
sudo systemctl enable nodered.service
```

更多信息请查阅官方文档，或直接查看编辑界面工具提供的实用帮助。

