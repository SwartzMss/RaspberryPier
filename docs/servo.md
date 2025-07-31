# MG90S 舵机使用

MG90S 是常见的 9 克金属齿轮舵机，适合在树莓派上进行角度控制实验。本文件介绍基本接线和 Python 控制示例。

## 控制原理

舵机通过脉宽调制（PWM）信号来设定转动角度。树莓派通常以约 50Hz 的频率（周期约20ms）输出控制波形，改变高电平持续时间即可改变位置：

- 脉宽约 0.5ms 表示转到极限一端（约 0°）
- 脉宽约 1.5ms 为中间位置（约 90°）
- 脉宽约 2.5ms 表示另一端（约 180°）

舵机内部带有电位器反馈，控制电路会根据脉宽自动调节马达，直到输出轴停在相应角度。实际数值可能因品牌型号略有差异，可在该范围内微调。
## 硬件连接

| MG90S 引脚 | Raspberry Pi 引脚 |
|------------|-------------------|
| 棕线 (GND) | GND (任意针脚)    |
| 红线 (VCC) | 5V (Pin 2 或 4)   |
| 橙线 (信号) | GPIO18 (Pin 12)   |

建议单独给舵机供电，并与树莓派共地，避免电流过大造成系统不稳定。

## 安装所需库

```bash
sudo apt update
sudo apt install python3-gpiozero
```

## gpiozero + lgpio 示例

若已安装 `lgpio` 库（可执行 `sudo apt install python3-lgpio`），可以将
`gpiozero` 的 pin factory 切换为 `LGPIOFactory`，以使用 `lgpio` 的 PWM 实现：

```python
from gpiozero import Device, Servo
from gpiozero.pins.lgpio import LGPIOFactory
from time import sleep

Device.pin_factory = LGPIOFactory()
servo = Servo(18)

while True:
    servo.mid()
    sleep(1)
    servo.min()
    sleep(1)
    servo.max()
    sleep(1)
```

更多脚本与示例可在 [pi5-mg90s-tools](https://github.com/SwartzMss/pi5-mg90s-tools) 仓库获取。

