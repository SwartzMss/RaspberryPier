# 旋转电位器 (Rotary Potentiometer)

旋转电位器是一种常见的可调电阻，可输出与旋钮角度成比例的电压。由于树莓派没有内建的模拟输入，通常需要借助 MCP3008 或 ADS1115 等外部 ADC 模块来读取其模拟值。

## 接线示例

以 MCP3008 为例，可按下列方式连接：

- 电位器的一端接 3.3V
- 另一端接 GND
- 中间滑动端接 MCP3008 的 CH0 (或其他通道)

### Python 读取示例

```python
from gpiozero import MCP3008
import time

pot = MCP3008(channel=0)  # 对应 MCP3008 的 CH0

while True:
    value = pot.value  # 0.0~1.0 之间的浮点数
    print(f"Potentiometer value: {value:.3f}")
    time.sleep(0.5)
```

此脚本每半秒打印一次旋钮当前的位置。`gpiozero` 会自动配置 SPI 接口，代码在树莓派 5 上与其他型号通用。

更多脚本与使用技巧可参考外部仓库 [pi5-potentiometer-tools](https://github.com/SwartzMss/pi5-potentiometer-tools)。
