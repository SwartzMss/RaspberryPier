# MG90S 舵机使用

MG90S 是常见的 9 克金属齿轮舵机，适合在树莓派上进行角度控制实验。本文件介绍基本接线和 Python 控制示例。

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

如需更高精度的 PWM，可以改用 `pigpio`：

```bash
sudo apt install pigpio python3-pigpio
sudo systemctl start pigpiod
```

## gpiozero 示例

```python
from gpiozero import Servo
from time import sleep

servo = Servo(18)

while True:
    servo.mid()  # 90°
    sleep(1)
    servo.min()  # 0° 左右
    sleep(1)
    servo.max()  # 180° 左右
    sleep(1)
```

## RPi.GPIO PWM 示例

```python
import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BCM)
GPIO.setup(18, GPIO.OUT)

pwm = GPIO.PWM(18, 50)  # 50 Hz
pwm.start(0)

try:
    while True:
        for angle in range(0, 181, 30):
            duty = 2.5 + angle / 18.0
            pwm.ChangeDutyCycle(duty)
            time.sleep(0.5)
finally:
    pwm.stop()
    GPIO.cleanup()
```

更多脚本与示例可在 [pi5-mg90s-tools](https://github.com/SwartzMss/pi5-mg90s-tools) 仓库获取。

