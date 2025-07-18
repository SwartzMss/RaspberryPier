# 传感器监控脚本作为系统服务

本节示例如何将自定义的传感器监控脚本配置为 `systemd` 服务，使其在开机后自动运行。

假设脚本路径为 `/home/pi/sensor-monitor.py`，内容可以是定期读取传感器并通过日志记录或 MQTT 发布的 Python 程序。

如果脚本依赖第三方库，建议使用 [Python 虚拟环境](https://docs.python.org/zh-cn/3/library/venv.html) 进行管理，例如：

```bash
python3 -m venv /home/pi/sensor-env
source /home/pi/sensor-env/bin/activate
pip install -r requirements.txt  # 根据需要安装依赖
```

下面的服务文件示例同样使用该虚拟环境中的解释器。

## 创建服务文件

1. 使用 `sudo` 权限在 `/etc/systemd/system/` 下创建服务单元，如 `sensor-monitor.service`：

```ini
[Unit]
Description=Sensor monitor service
After=network.target

[Service]
ExecStart=/home/pi/sensor-env/bin/python /home/pi/sensor-monitor.py
Restart=always
User=pi

[Install]
WantedBy=multi-user.target
```

2. 保存文件后重新加载 `systemd` 配置：

```bash
sudo systemctl daemon-reload
```

## 启用并启动服务

```bash
sudo systemctl enable sensor-monitor.service
sudo systemctl start sensor-monitor.service
```

使用以下命令查看运行状态：

```bash
sudo systemctl status sensor-monitor.service
```

这样设置后，树莓派每次开机都会自动启动该脚本。如果需要停止或禁用服务，可分别执行：

```bash
sudo systemctl stop sensor-monitor.service
sudo systemctl disable sensor-monitor.service
```

