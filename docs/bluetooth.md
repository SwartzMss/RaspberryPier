# 蓝牙配置与数据交换

本节介绍如何在树莓派 5 上配置蓝牙，并使用其内置蓝牙向小米音箱发送音频或作为 BLE GATT 服务器提供数据。

## 安装 BlueZ

树莓派常见的 Debian 系发行版已自带 `bluez`，若未安装可执行：

```bash
sudo apt update
sudo apt install bluez bluetooth
```

启用蓝牙服务：

```bash
sudo systemctl enable --now bluetooth
```

## 连接小米音箱作为蓝牙音箱

1. 启动 `bluetoothctl` 进入交互模式。
2. 在命令提示符下输入 `power on`、`agent on`、`default-agent` 依次开启蓝牙、启用配对代理。
3. 使树莓派进入搜索模式：`scan on`，等待看到小米音箱的 MAC 地址后使用 `pair <MAC>` 进行配对。
4. 配对成功后执行 `trust <MAC>` 和 `connect <MAC>` 完成连接。
5. 在桌面环境中或通过 `pactl list sinks` 查看并选择蓝牙音箱作为音频输出即可。

完成以上步骤后，在树莓派播放的任何音频都会通过蓝牙传输到小米音箱。

## 树莓派作为 BLE GATT 服务器示例

下面的示例脚本基于 `dbus-python`，实现一个简单的 GATT 服务器，它向外暴露一个可读取的特征值。脚本改编自 BlueZ 示例 `example-gatt-server`。

```python
#!/usr/bin/env python3
import dbus
import dbus.mainloop.glib
from gi.repository import GLib

BLUEZ_SERVICE = 'org.bluez'
GATT_MANAGER_IFACE = 'org.bluez.GattManager1'
LE_ADVERTISING_MANAGER_IFACE = 'org.bluez.LEAdvertisingManager1'

SERVICE_UUID = '12345678-1234-5678-1234-56789abcdef0'
CHAR_UUID = '12345678-1234-5678-1234-56789abcdef1'

class Application(dbus.service.Object):
    def __init__(self, bus):
        self.path = '/'
        self.services = []
        dbus.service.Object.__init__(self, bus, self.path)
        self.add_service(TestService(bus, 0))

    def get_path(self):
        return dbus.ObjectPath(self.path)

    def add_service(self, service):
        self.services.append(service)

    @dbus.service.method(dbus_interface='org.freedesktop.DBus.ObjectManager', out_signature='a{oa{sa{sv}}}')
    def GetManagedObjects(self):
        response = {}
        for service in self.services:
            response[service.get_path()] = service.get_properties()
            for char in service.get_characteristics():
                response[char.get_path()] = char.get_properties()
        return response

class Service(dbus.service.Object):
    PATH_BASE = '/org/bluez/example/service'

    def __init__(self, bus, index, uuid, primary):
        self.path = self.PATH_BASE + str(index)
        self.bus = bus
        self.uuid = uuid
        self.primary = primary
        self.characteristics = []
        dbus.service.Object.__init__(self, bus, self.path)

    def get_properties(self):
        return {
            'org.bluez.GattService1': {
                'UUID': self.uuid,
                'Primary': self.primary,
                'Characteristics': dbus.Array([c.get_path() for c in self.characteristics], signature='o')
            }
        }

    def get_path(self):
        return dbus.ObjectPath(self.path)

    def add_characteristic(self, characteristic):
        self.characteristics.append(characteristic)

    def get_characteristics(self):
        return self.characteristics

class Characteristic(dbus.service.Object):
    def __init__(self, bus, index, uuid, flags, service):
        self.path = service.path + '/char' + str(index)
        self.bus = bus
        self.uuid = uuid
        self.flags = flags
        self.service = service
        dbus.service.Object.__init__(self, bus, self.path)

    def get_properties(self):
        return {
            'org.bluez.GattCharacteristic1': {
                'UUID': self.uuid,
                'Service': self.service.get_path(),
                'Flags': self.flags,
            }
        }

    def get_path(self):
        return dbus.ObjectPath(self.path)

    @dbus.service.method(dbus_interface='org.bluez.GattCharacteristic1', in_signature='a{sv}', out_signature='ay')
    def ReadValue(self, options):
        value = [dbus.Byte(c) for c in b'Pi5 GATT server']
        return value

class TestService(Service):
    def __init__(self, bus, index):
        Service.__init__(self, bus, index, SERVICE_UUID, True)
        self.add_characteristic(TestCharacteristic(bus, 0, self))

class TestCharacteristic(Characteristic):
    def __init__(self, bus, index, service):
        Characteristic.__init__(self, bus, index, CHAR_UUID, ['read'], service)


def register_app_cb():
    print('GATT application registered')


def register_app_error_cb(error):
    print('Failed to register application:', error)
    mainloop.quit()


def main():
    dbus.mainloop.glib.DBusGMainLoop(set_as_default=True)
    bus = dbus.SystemBus()

    adapter = bus.get_object(BLUEZ_SERVICE, '/org/bluez/hci0')
    service_manager = dbus.Interface(adapter, GATT_MANAGER_IFACE)

    app = Application(bus)
    service_manager.RegisterApplication(app.get_path(), {}, reply_handler=register_app_cb, error_handler=register_app_error_cb)

    global mainloop
    mainloop = GLib.MainLoop()
    mainloop.run()

if __name__ == '__main__':
    main()
```

保存为 `gatt_server.py` 后运行：

```bash
sudo python3 gatt_server.py
```

此时树莓派会暴露出名为 `Pi5 GATT server` 的特征值，可被支持 BLE 的手机或其他设备读取。

以上即为在树莓派 5 上配置蓝牙并作为 GATT 服务器的基本示例。
