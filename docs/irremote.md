# IR38K 红外接收模块

IR38K 是常见的 38kHz 红外接收头，可用于读取各种遥控器的红外信号。本节介绍在树莓派 5 上连接该模块并配合 `pi5-ir38k-tools` 仓库解析按键代码的基本方法。

## 硬件连接

- VCC 接 3.3V
- GND 接 GND
- OUT 接 GPIO18（或任意可用 GPIO）

## 示例脚本

`pi5-ir38k-tools` 仓库提供了基于 `lgpio` 的示例脚本。克隆仓库后运行 `main.py` 即可在终端显示接收到的按键码：

```bash
git clone https://github.com/SwartzMss/pi5-ir38k-tools
cd pi5-ir38k-tools
python3 main.py --pin 18
```

脚本会持续输出检测到的十六进制编码，可据此编写自定义控制逻辑。

更多脚本与说明可参考外部仓库 [pi5-ir38k-tools](https://github.com/SwartzMss/pi5-ir38k-tools)。
