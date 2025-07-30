# ESP32 选型指南


| 型号 | 架构／核心 | Wi-Fi | BLE/网状网络 | 外设亮点 | 内存/PSRAM | USB | 典型应用场景 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ESP32 (原版) | Xtensa 双核 240M | 802.11 b/g/n | BLE 4.2 + Classic | 丰富 GPIO、ADC/DAC、CAN、I²S… | Flash 4 MB | 无 | 通用 IoT、语音、小型网关 |
| ESP32-S2 | Xtensa 单核 240M | 802.11 b/g/n | 无 BLE | USB-OTG、更多安全特性 | Flash 4 MB | 有 | USB 外设、键盘鼠标、对安全要求高场景 |
| ESP32-C3 | RISC-V 单核 160M | 802.11 b/g/n | BLE 5.0 | 低功耗、硬件加密（ECDSA） | Flash 4 MB | 无 | 低成本传感器节点、简单遥测 |
| ESP32-S3 | Xtensa 双核 240M + AI 加速 | 802.11 b/g/n | BLE 5.0 | 矢量指令、RGB LCD、USB-OTG | Flash 8 MB | 有 | AI 边缘推理、摄像头、显示终端 |
| ESP32-C6 | RISC-V 单核 160M | Wi-Fi 6 (802.11 ax) | BLE 5.2, Thread | Mesh/Thread、低延迟 | Flash 4 MB | 无 | 智能家居、工业 IoT、低延迟网络 |
| ESP32-H2 | RISC-V 单核 96M | 无 | BLE 5.2, Zigbee | 802.15.4 专用、超低功耗 | Flash 2 MB | 无 | Zigbee/Thread 传感网络 |
| ESP32-CAM | Xtensa 双核 240M | 802.11 b/g/n | BLE 4.2 | 内建 OV2640 摄像头接口、SD 卡槽 | Flash 4 MB | 无 | 视频监控、人脸识别 |
