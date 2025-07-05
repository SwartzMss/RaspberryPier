# 树莓派专用命令

## 1. 硬件信息

```bash
vcgencmd measure_temp           # 查看CPU温度
vcgencmd get_mem arm           # 查看ARM内存分配
vcgencmd get_mem gpu           # 查看GPU内存分配
vcgencmd measure_volts core    # 查看核心电压
vcgencmd measure_volts sdram_c # 查看SDRAM电压
vcgencmd measure_clock arm     # 查看ARM时钟频率
vcgencmd measure_clock core    # 查看核心时钟频率
vcgencmd measure_clock h264    # 查看H264时钟频率
vcgencmd measure_clock isp     # 查看ISP时钟频率
vcgencmd measure_clock v3d     # 查看V3D时钟频率
vcgencmd measure_clock uart    # 查看UART时钟频率
vcgencmd measure_clock pwm     # 查看PWM时钟频率
vcgencmd measure_clock emmc    # 查看EMMC时钟频率
vcgencmd measure_clock pixel   # 查看像素时钟频率
vcgencmd measure_clock vec     # 查看VEC时钟频率
vcgencmd measure_clock hdmi    # 查看HDMI时钟频率
vcgencmd measure_clock dpi     # 查看DPI时钟频率
```

## 2. 树莓派配置

```bash
raspi-config                    # 树莓派配置工具
sudo raspi-config              # 以管理员身份运行配置工具
```

## 3. GPIO 操作

```bash
gpio readall                   # 查看GPIO引脚状态（WiringPi）
gpio mode 18 out               # 设置GPIO18为输出模式
gpio write 18 1                # 设置GPIO18为高电平
gpio write 18 0                # 设置GPIO18为低电平
gpio read 18                   # 读取GPIO18状态
```

## 4. 摄像头相关

```bash
raspistill -o image.jpg        # 拍照并保存为image.jpg
raspistill -t 5000 -o image.jpg # 5秒后拍照
raspivid -o video.h264         # 录制视频
raspivid -t 10000 -o video.h264 # 录制10秒视频
```

## 5. 显示器配置

```bash
tvservice -s                   # 查看显示器状态
tvservice -o                   # 关闭显示器
tvservice -p                   # 打开显示器
tvservice -m CEA               # 列出CEA模式
tvservice -m DMT               # 列出DMT模式
```

## 6. 音频相关

```bash
amixer scontrols               # 列出音频控制
amixer sset 'Master' 80%       # 设置主音量80%
amixer sset 'PCM' 90%          # 设置PCM音量90%
```

## 7. 系统监控

```bash
vcgencmd get_throttled         # 查看节流状态
vcgencmd get_config str        # 查看配置字符串
vcgencmd get_config int        # 查看配置整数
``` 