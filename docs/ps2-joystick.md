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

