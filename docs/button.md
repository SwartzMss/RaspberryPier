# 按键与开关模块

本文档介绍如何在树莓派 5 上连接并读取常见的三引脚按键/开关模块（例如 KY-004）。

## 接线

- 模块 `+` → 3.3V（引脚 1 或 17）
- 模块 `-` → GND（任意接地引脚）
- 模块 `S` → GPIO17（本文示例使用的引脚）

确保不要把模块 `+` 接到 5V，以免把 5V 直接送进 GPIO。

## 安装所需工具

```bash
sudo apt update
sudo apt install -y pinctrl gpiod
```

## 使用 shell 命令测试

可以先在终端将 GPIO17 设为输入并启用上拉：

```bash
sudo pinctrl set 17 ip pu
```

随后读取电平：

```bash
gpioget 0 17  # 松开时输出 1，按下时输出 0
```

若要持续监测状态，可使用：

```bash
gpiomon --num-events=0 0 17
```

## Python 示例

也可以在程序中直接配置引脚，无需预先执行 `pinctrl` 命令。下面的脚本使用 `gpiod` 库读取按键状态：

```python
import gpiod
import time

CHIP = gpiod.Chip('gpiochip0')
LINE = CHIP.get_line(17)

config = gpiod.line_request()
config.consumer = 'button'
config.request_type = gpiod.line_request.DIRECTION_INPUT
config.flags = gpiod.line_request.FLAG_BIAS_PULL_UP
LINE.request(config)

try:
    while True:
        if LINE.get_value() == 0:
            print('Pressed')
        else:
            print('Released')
        time.sleep(0.5)
except KeyboardInterrupt:
    pass
finally:
    LINE.release()
```

脚本会持续打印 `Pressed` 或 `Released`，可以根据需要改为触发其他操作。
