# Sensors

Raspberry Pi 5 supports a variety of sensors to extend its capabilities. This document provides an overview of how to connect and read common sensors.

## DHT22 温湿度传感器

DHT22 是常见的温湿度传感器，适合直接连接到树莓派的 GPIO 引脚。推荐接线如下：

- VCC 接 3.3V（引脚 1）
- GND 接 GND（引脚 6）
- DATA 接 GPIO4（引脚 7）
- 在 VCC 与 DATA 之间串联一个 4.7k～10kΩ 的上拉电阻

使用 `Adafruit_DHT` 库可以读取温度和湿度，示例代码如下：

```python
import Adafruit_DHT

sensor = Adafruit_DHT.DHT22
pin = 4  # DATA 对应的 BCM 编号

humidity, temperature = Adafruit_DHT.read_retry(sensor, pin)
if humidity is not None and temperature is not None:
    print(f"Temp: {temperature:.1f}°C  Humidity: {humidity:.1f}%")
else:
    print("Sensor failure. Check wiring.")
```

更多高级用法可以参考外部仓库 [pi5-dht22-tools](https://github.com/SwartzMss/pi5-dht22-tools)。

## HC-SR04 超声波测距模块

HC-SR04 用于测量距离，需要 TRIG 与 ECHO 两个控制引脚，推荐接线如下：

- VCC 接 5V（引脚 2 或 4）
- GND 接 GND（引脚 6）
- TRIG 接 GPIO23（引脚 16）
- ECHO 经电阻分压后接 GPIO24（引脚 18）

下面是基于 `lgpio` 的示例代码：

```python
import lgpio
import time

TRIG = 23
ECHO = 24

# 打开第 0 号 gpiochip
h = lgpio.gpiochip_open(0)
lgpio.gpio_claim_output(h, TRIG)
lgpio.gpio_claim_input(h, ECHO)

def read_distance():
    lgpio.gpio_write(h, TRIG, 0)
    time.sleep(0.05)

    lgpio.gpio_write(h, TRIG, 1)
    time.sleep(0.00001)
    lgpio.gpio_write(h, TRIG, 0)

    while lgpio.gpio_read(h, ECHO) == 0:
        start = time.time()

    while lgpio.gpio_read(h, ECHO) == 1:
        end = time.time()

    duration = end - start
    distance = duration * 17150
    return distance

try:
    while True:
        dist = read_distance()
        print(f"Distance: {dist:.2f} cm")
        time.sleep(1)
except KeyboardInterrupt:
    lgpio.gpiochip_close(h)
```

更多脚本与说明请参考 [pi5-ultrasonic-tools](https://github.com/SwartzMss/
pi5-ultrasonic-tools)，该仓库已改用 `lgpio` 实现。


## MCP3008 模数转换器

树莓派本身没有模拟输入，需要借助外部 ADC 才能读取光敏电阻等模拟传感器。MCP3008 是常见的 10 位、8 通道 ADC，可通过 SPI 与树莓派5 连接。
它会输出 0~1023 的数字值，`gpiozero` 读取时会缩放为 0.0~1.0 的浮点数。

### 接线示例

- VDD 与 VREF 接 3.3V
- AGND、DGND 接 GND
- CLK 接 SCLK（GPIO11，引脚 23）
- DOUT 接 MISO（GPIO9，引脚 21）
- DIN 接 MOSI（GPIO10，引脚 19）
- CS 接 CE0（GPIO8，引脚 24）或 CE1（GPIO7，引脚 26）
- CH0~CH7 用于连接模拟信号

开启树莓派的 SPI 介面後，即可在 Python 中使用 `gpiozero` 读取 MCP3008 的数据。

### 示例代码

```python
from gpiozero import MCP3008, LightSensor

adc = MCP3008(channel=0)
print(adc.value)  # 0.0~1.0 尺度的模拟值

light = LightSensor(channel=0)
light.when_dark = lambda: print("It's dark")
light.when_light = lambda: print("It's light")
```

上述代码展示了直接读取通道 0 的模拟值，以及用 `LightSensor` 这类高级封装读取光敏电阻。`gpiozero` 会选择配置好的 pin factory，因此 Pi5 上的用法与旧款树莓派一致。


## 光敏电阻（数字检测）

如果暂时没有 ADC 模块，可以将光敏电阻当作二值开关使用。将传感器一端接 3.3V，另一端接到 GPIO17，并在程序中启用下拉电阻。亮度高时电压被拉高，读取到逻辑 1；亮度低时下拉电阻使其变为逻辑 0。以下示例使用 `lgpio` 读取电平：

```python
import lgpio
import time

LDR_PIN = 17

# 打开第 0 号 gpiochip
h = lgpio.gpiochip_open(0)
lgpio.gpio_claim_input(h, LDR_PIN, lgpio.SET_BIAS_PULL_DOWN)

try:
    while True:
        if lgpio.gpio_read(h, LDR_PIN):
            print("Bright")
        else:
            print("Dark")
        time.sleep(1)
except KeyboardInterrupt:
    lgpio.gpiochip_close(h)
```

这种方法只能粗略地区分明暗，但足以在没有模数转换器的情况下测试光敏电阻。

更多脚本与说明请参考 [pi5-photoresistor-tools](https://github.com/SwartzMss/pi5-photoresistor-tools)。

## 声音传感器 (KY-038)

KY-038 或 KY-037 等声音传感器可侦测环境中的噪音强度，通常同时提供模拟 (A0) 与数字 (D0) 输出。如果只需判断是否有较大的声音，可直接读取 D0；若要量化声音幅度，则需将 A0 接至 MCP3008 等 ADC 模块。

### 接线示例

- VCC 接 3.3V
- GND 接 GND
- A0 接 MCP3008 的 CH0
- D0 可接任意 GPIO，用于阈值触发 (可选)

### 示例代码

```python
from gpiozero import SoundSensor

mic = SoundSensor(channel=0)  # 读取 MCP3008 通道 0 的模拟值

while True:
    mic.wait_for_sound()
    print("Sound detected!")
```

上述示例在声音达到阈值时打印提示，可通过 `threshold` 属性调整灵敏度。

更多脚本与说明请参考 [pi5-soundsensor-tools](https://github.com/SwartzMss/pi5-soundsensor-tools)。

### D0 数字输出示例

如果没有 ADC，仅需侦测是否出现噪音，可直接读取模块的 D0 引脚：

```python
import lgpio
import time

MIC_PIN = 17  # D0 所连接的 BCM 编号

# 打开第 0 号 gpiochip，并声明输入
h = lgpio.gpiochip_open(0)
lgpio.gpio_claim_input(h, MIC_PIN)

try:
    while True:
        if lgpio.gpio_read(h, MIC_PIN):
            print("Noise detected!")
        time.sleep(0.1)
except KeyboardInterrupt:
    lgpio.gpiochip_close(h)
```


