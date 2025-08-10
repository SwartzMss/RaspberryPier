# Pi5 人脸识别工具

基于 Raspberry Pi 5 的实时人脸检测与识别示例，使用 **OpenCV** 与官方 **Picamera2** 接口。

## 快速开始

1. 安装依赖
   ```bash
   sudo apt update && sudo apt upgrade -y
   sudo apt install -y build-essential cmake libcamera-apps libcap-dev python3-libcamera python3-picamera2
   ```

2. 获取示例项目
   ```bash
   git clone https://github.com/SwartzMss/pi5-face-recognition-tools.git
   cd pi5-face-recognition-tools
   python3 -m venv venv --system-site-packages
   source venv/bin/activate
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

3. 采集人脸数据
   ```bash
   python capture.py   # 在终端回车拍照
   ```

4. 实时识别
   ```bash
   python recognize.py
   ```

## 说明

- `capture.py` 用于采集数据并保存到 `dataset/` 目录。
- `recognize.py` 使用 Picamera2 多线程捕获图像并在窗口中标出识别结果。

更多细节及性能优化建议请参考[项目 README](https://github.com/SwartzMss/pi5-face-recognition-tools)。
