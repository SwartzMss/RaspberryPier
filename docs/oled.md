# 1.3寸 SH1106 OLED 显示屏

1.3寸 OLED 屏幕常用 SH1106 控制芯片，可通过 I2C 与树莓派5 连接。
本文档简要说明接线方法与在 Python 中显示文本的示例代码。

## 接线

- VCC 接 3.3V（引脚 1）
- GND 接 GND（引脚 6）
- SCL 接 GPIO3（SCL，引脚 5）
- SDA 接 GPIO2（SDA，引脚 3）

确保在 `raspi-config` 中启用 I2C 接口。

## 安装依赖

推荐使用 `luma.oled` 库控制 SH1106 屏幕：

```bash
pip install luma.oled
```

## 示例代码

以下 Python 脚本在屏幕上显示一行文字：

```python
from luma.core.interface.serial import i2c
from luma.oled.device import sh1106
from luma.core.render import canvas
from PIL import ImageFont

serial = i2c(port=1, address=0x3C)
device = sh1106(serial)

font = ImageFont.load_default()

with canvas(device) as draw:
    draw.text((0, 0), "Hello, OLED!", font=font, fill=255)
```

运行脚本即可在 1.3 寸屏幕上看到文字。`luma.oled` 还支持绘制
图形、显示图片等功能，可根据需要进一步探索。
