# 蜂鸣器（Buzzer）使用

蜂鸣器是常见的声音输出设备，分为主动和被动两种。主动蜂鸣器只需给定高低电平即可响，
被动蜂鸣器则需要以 PWM 方式产生不同频率的方波才能发声。

## 硬件连接

- VCC 接 3.3V（主动蜂鸣器）或经晶体管接 5V
- GND 接 GND
- 控制脚接任意 GPIO，以下示例假设使用 GPIO18

## gpiozero 示例

```python
from gpiozero import Buzzer
from time import sleep

bz = Buzzer(18)

bz.on()
sleep(1)
bz.off()

# 循环蜂鸣
for _ in range(3):
    bz.beep(on_time=0.2, off_time=0.2, n=5, background=False)
```

## RPi.GPIO PWM 示例（适合被动蜂鸣器）

```python
import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BCM)
BUZZ_PIN = 18
GPIO.setup(BUZZ_PIN, GPIO.OUT)

pwm = GPIO.PWM(BUZZ_PIN, 1000)  # 1 kHz
pwm.start(50)  # 50% 占空比

time.sleep(1)
pwm.ChangeFrequency(2000)
time.sleep(1)

pwm.stop()
GPIO.cleanup()
```

更多脚本与说明可参考外部仓库 [pi5-buzz-tools](https://github.com/SwartzMss/pi5-buzz-tools)。

### 使用仓库示例

仓库中提供的 `main.py` 可以让蜂鸣器按照设定次数和间隔鸣叫：

```bash
python3 main.py --times 3 --interval 0.5 --pin 16
```
