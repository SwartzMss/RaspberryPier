# 模数转换器 (ADC)

模数转换器用于在树莓派等数字系统上读取模拟信号。下文介绍两款常见的模块，并给出连接和使用示例。

## MCP3008

树莓派本身没有模拟输入，需要借助外部 ADC 才能读取光敏电阻等模拟传感器。MCP3008 是常见的 10 位、8 通道 ADC，可通过 SPI 与树莓派5 连接。它会输出 0~1023 的数字值，`gpiozero` 读取时会缩放为 0.0~1.0 的浮点数。

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

## ADS1115

ADS1115 是一款基于 I²C 接口的 16 位 ADC，提供 4 个差分/单端输入通道。其分辨率更高，并允许通过地址引脚在同一总线上连接多达四个 ADS1115 模块。

### 接线示例

- VDD 接 3.3V
- GND 接 GND
- SDA 接 SDA（GPIO2，引脚 3）
- SCL 接 SCL（GPIO3，引脚 5）
- ADDR 可接 GND、VDD 或其他地址引脚以设置 I²C 地址

在 Python 中可以使用 `adafruit-circuitpython-ads1x15` 库读取数据：

```python
import board
import busio
from adafruit_ads1x15.ads1115 import ADS1115
from adafruit_ads1x15.analog_in import AnalogIn

i2c = busio.I2C(board.SCL, board.SDA)
ads = ADS1115(i2c)
chan = AnalogIn(ads, ADS1115.P0)
print(chan.value, chan.voltage)
```

ADS1115 支持可编程增益放大和单次/连续采样模式，适合需要更高精度或多路差分测量的场景。
