# ESP32 选型指南

本文档对比各型号 ESP32 的特性，帮助根据实际需求快速确定合适的芯片或模组。

## 一、选型关键要素

### 架构与性能
- CPU 核心数与频率（单/双核、最高 240 MHz）
- 指令集（Xtensa vs. RISC-V）
- AI 加速（矢量指令、Tensor 加速单元）

### 无线能力
- Wi-Fi（802.11 b/g/n vs. Wi-Fi 6）
- Bluetooth（经典蓝牙 vs. BLE 5.x）
- Thread/Zigbee/Proprietary（802.15.4 支持）

### 外设接口
- GPIO、ADC/DAC 通道数
- I²C、SPI、UART、I²S、CAN、PWM…
- USB-OTG、SDIO、Ethernet MAC 等

### 内存与存储
- Flash 大小（4 MB–16 MB）
- PSRAM（0–8 MB）

### 安全特性
- 硬件加密引擎（AES/SHA/PKC）
- 安全启动、Flash 加密

### 功耗管理
- 待机电流
- 多种深度睡眠模式

### 封装与模块
- 芯片裸片 vs. WROOM/WROVER/CAM 等封装模块
- 尺寸、天线形式、成本

### 生态与成本
- ESP-IDF／Arduino／MicroPython 支持
- 单价 vs. 量产成本

## 二、ESP32 系列对比

| 型号 | 架构／核心 | Wi-Fi | BLE/网状网络 | 外设亮点 | 内存/PSRAM | USB | 典型应用场景 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ESP32 (原版) | Xtensa 双核 240M | 802.11 b/g/n | BLE 4.2 + Classic | 丰富 GPIO、ADC/DAC、CAN、I²S… | Flash 4 MB | 无 | 通用 IoT、语音、小型网关 |
| ESP32-S2 | Xtensa 单核 240M | 802.11 b/g/n | 无 BLE | USB-OTG、更多安全特性 | Flash 4 MB | 有 | USB 外设、键盘鼠标、对安全要求高场景 |
| ESP32-C3 | RISC-V 单核 160M | 802.11 b/g/n | BLE 5.0 | 低功耗、硬件加密（ECDSA） | Flash 4 MB | 无 | 低成本传感器节点、简单遥测 |
| ESP32-S3 | Xtensa 双核 240M + AI 加速 | 802.11 b/g/n | BLE 5.0 | 矢量指令、RGB LCD、USB-OTG | Flash 8 MB | 有 | AI 边缘推理、摄像头、显示终端 |
| ESP32-C6 | RISC-V 单核 160M | Wi-Fi 6 (802.11 ax) | BLE 5.2, Thread | Mesh/Thread、低延迟 | Flash 4 MB | 无 | 智能家居、工业 IoT、低延迟网络 |
| ESP32-H2 | RISC-V 单核 96M | 无 | BLE 5.2, Zigbee | 802.15.4 专用、超低功耗 | Flash 2 MB | 无 | Zigbee/Thread 传感网络 |
| ESP32-CAM | Xtensa 双核 240M | 802.11 b/g/n | BLE 4.2 | 内建 OV2640 摄像头接口、SD 卡槽 | Flash 4 MB | 无 | 视频监控、人脸识别 |

## 三、典型选型指导

### 预算 & 规模化
- 数百节点：优先低成本 ESP32-C3
- 高性能需求：ESP32 双核或 S3

### 无线场景
- 需要最高吞吐：原版 ESP32 / S3
- 低延迟、大并发：C6（Wi-Fi 6）
- Thread/Zigbee：H2 或 C6

### 外设 & AI
- USB 外设：S2/S3
- AI 推理：S3（矢量加速）
- 摄像头：CAM 模块

### 安全 & OTA
- 强制安全：S2（安全启动 + Flash 加密）
- 轻量化：C3（硬件加密，足够常见场景）

### 功耗敏感
- 超低功耗：C3/C6/H2
- 复杂算力：S2/S3（休眠切换需优化）

## 小结
- 初学者/原型：ESP32 WROOM（原版）最成熟、文档全
- 成本 & 规模：ESP32-C3 模块
- AI/图形/USB：ESP32-S3
- Wi-Fi 6/Thread：ESP32-C6
- Zigbee/超低功耗：ESP32-H2
- 摄像头：ESP32-CAM

根据你的应用侧重点（成本、性能、无线协议、安全或功耗），对照上表迅速锁定目标型号，再进一步挑选具体封装（WROOM vs. WROVER vs. CAM）即可。
