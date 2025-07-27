# PS2 手柄连接与测试

使用 PlayStation 2 手柄可以在树莓派上实现游戏控制或简单的机器人遥控。
本项目推荐使用 [`pi5-ps2-joystick-tools`](https://github.com/SwartzMss/pi5-ps2-joystick-tools) 仓库中的脚本进行调试。

## 接线方式

PS2 手柄转接板通常提供以下引脚：

| 手柄引脚 | 功能             | 树莓派引脚示例 |
|---------|----------------|-------------|
| VCC     | 3.3V 供电       | Pin 1       |
| GND     | 地             | Pin 6       |
| ATT     | 片选/ATTENTION | GPIO8 (CE0) |
| CLK     | 时钟           | GPIO11 (SCLK) |
| CMD     | 命令           | GPIO10 (MOSI) |
| DAT     | 数据           | GPIO9 (MISO) |

此外，部分转接板还会引出模拟摇杆的 `VRx` 和 `VRy` 两个尖脚，
若需要读取摇杆的电位，可将它们接到外置 ADC（如 ADS1115）：

```
VRx -> ADS1115 A0 (P0)
VRy -> ADS1115 A1 (P1)
```

随后即可利用 `adafruit-circuitpython-ads1x15` 等库在 Python 中获取摇杆的电压数值。

> 各转接板可能标注 `CS`、`SEL` 等不同名称，可按说明书接线。

## 安装脚本

脚本仓库提供读取手柄数据的 Python 示例：

```bash
# 克隆仓库（需能访问 GitHub）
# git clone https://github.com/SwartzMss/pi5-ps2-joystick-tools
cd pi5-ps2-joystick-tools
pip install -r requirements.txt
```

安装完成后可运行 `python read_ps2.py` 查看按键状态。

## 示例输出

运行脚本后，终端会持续打印按钮与摇杆的数值，例如：

```
LX: 127  LY: 128  RX: 127  RY: 128  Buttons: 0x0000
```

各位值分别表示左右摇杆的 X/Y 位置与按键位掩码。

更多用法与进阶示例，请访问上游仓库查看文档。
