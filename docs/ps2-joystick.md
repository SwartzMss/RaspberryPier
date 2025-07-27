# PS2 摇杆与手柄模块

市面上常见的 "PS2 摇杆" 模块往往只有 `VRx` 与 `VRy` 两个模拟输出脚，
无需任何数字通信即可读取摇杆位置。本节先介绍这种简单模块的接线与读取方式，
随后补充带完整手柄接口的用法。

## 仅带 VRx/VRy 的摇杆

这些模块通常只引出四根线：`VCC`、`GND`、`VRx` 和 `VRy`。
可利用外置 ADC（如 ADS1115）读取其两个模拟量。

### 接线示例

| 模块引脚 | 功能      | 连接                |
|---------|---------|-------------------|
| VCC     | 3.3V    | Pin 1             |
| GND     | 地      | Pin 6             |
| VRx     | X 轴电位 | ADS1115 A0 (P0)  |
| VRy     | Y 轴电位 | ADS1115 A1 (P1)  |

```python
import board
import busio
from adafruit_ads1x15.ads1115 import ADS1115
from adafruit_ads1x15.analog_in import AnalogIn

i2c = busio.I2C(board.SCL, board.SDA)
ads = ADS1115(i2c)
x = AnalogIn(ads, ADS1115.P0)
y = AnalogIn(ads, ADS1115.P1)
print(x.value, y.value)
```

## 带 ATT/CLK/CMD/DAT 的手柄

部分转接板会额外提供 `ATT`、`CLK`、`CMD`、`DAT` 等引脚，
适用于连接完整的 PlayStation 2 手柄。
推荐参考 [`pi5-ps2-joystick-tools`](https://github.com/SwartzMss/pi5-ps2-joystick-tools)
仓库中的脚本进行调试，典型接线如下表所示：

| 手柄引脚 | 功能             | 树莓派引脚示例 |
|---------|----------------|-------------|
| VCC     | 3.3V 供电       | Pin 1       |
| GND     | 地             | Pin 6       |
| ATT     | 片选/ATTENTION | GPIO8 (CE0) |
| CLK     | 时钟           | GPIO11 (SCLK) |
| CMD     | 命令           | GPIO10 (MOSI) |
| DAT     | 数据           | GPIO9 (MISO) |

安装脚本：

```bash
# git clone https://github.com/SwartzMss/pi5-ps2-joystick-tools
cd pi5-ps2-joystick-tools
pip install -r requirements.txt
```
执行 `python read_ps2.py` 后即可查看按键与摇杆状态。

关于更多用法，请查阅上游仓库的文档。
