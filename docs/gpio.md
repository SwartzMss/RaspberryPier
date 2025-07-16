# GPIO 操作指南

本文件对 Raspberry Pi 上常用的 GPIO 库进行简要介绍，并说明它们的相互关系。

## gpiozero

`gpiozero` 是 Raspberry Pi Foundation 推荐的 Python 高层库。它通过“pin factory”编程模式集成了多种底层 GPIO 库，使得各种硬件接口的调用更加方便。

- 默认使用 `RPi.GPIO` 作为底层
- 可指定 `pigpio` 或 `lgpio` 作为 pin factory

## RPi.GPIO

`RPi.GPIO` 是比较早期的传统 Python GPIO 库，直接控制本地系统的 GPIO 设备。它在某些环境可能需要赋予超级权限才能正常使用，而且不支持远程控制。

## pigpio

`pigpio` 通过与同名服务器 (pigpiod) 配合使用。它支持硬件 PWM 和多个线程的高级功能，可以在本地或远程设备上启动。但需要注意，官方版本目前尚未支持 Raspberry Pi 5。

## lgpio

`lgpio` 是 `pigpio` 作者开发的新库，主要靠模块化的 `libgpiod` 控制 GPIO，方便程序与环境口的分离。它同样支持作为 gpiozero 的 pin factory。

## 它们的关系

- `gpiozero` 提供高级、简单的 API，底层可以选择 `RPi.GPIO`、`pigpio` 或 `lgpio`
- `RPi.GPIO` 是传统库，基础功能完备，但不支持远程、有限的高级功能
- `pigpio` 和 `lgpio` 提供更强的 GPIO 管理，可以远程操作，配合 `gpiozero` 可以更灵活

