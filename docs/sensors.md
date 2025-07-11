# Sensors

Raspberry Pi 5 supports a variety of sensors to extend its capabilities. This document provides an overview of how to connect and read common sensors.

## TODO

- [x] Add wiring instructions for temperature and humidity sensors
- [x] Provide sample Python code for reading sensor data

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
