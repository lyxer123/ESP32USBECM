| 支持的目标芯片 | ESP32-P4 | ESP32-S2 | ESP32-S3 |
| -------------- | -------- | -------- | -------- |

**语言 / Language:** [中文](readmecn.md) | [English](README.md)

# USB Host ECM 示例

本示例演示如何通过 [iot_usbh_ecm](https://components.espressif.com/components/espressif/iot_usbh_ecm) 在 ESP32 **USB 主机** 侧连接 **ECM 协议网络模块**。常见硬件为在 ESP32 与上网侧之间使用 **外接 WCH CH397A USB 转以太网（ECM）模块**，主机侧枚举为标准 ECM 网卡（例如 VID `1A86`、PID `5397`）。

支持的设备见：https://docs.espressif.com/projects/esp-iot-solution/zh_CN/latest/usb/usb_host/usb_ecm.html

若 ECM **不在**配置描述符 **1** 上（不少 CH397A 板子在配置 **2**），请在 menuconfig 中启用 `CONFIG_USB_HOST_ENABLE_ENUM_FILTER_CALLBACK`。本示例在「设备有多套 configuration」时会选用配置 **2**。

## 使用说明

**硬件连接（外接 CH397A 模块）**

将 ESP32 的 USB Host 信号（D+、D−、地线；5 V 供电按开发板与模块要求）接到 **CH397A 模块**的 USB 设备口；**4G 模组/调制解调器**的供电与天线按其手册连接。逻辑关系如下：

```
┌──────────────┐   USB D+/D−    ┌─────────────┐ 
│   ESP32-xx   ├───────────────►│   CH397A    │
│ (USB 主机)   │    GND / 5V*   │ (USB ECM)   │  
└──────────────┘                └─────────────┘    
* 电源需符合 CH397A 与 ESP32 开发板要求。
```

ECM 链路建立且 ESP 通过 DHCP 拿到 IP 后，本示例会打开用于共享网络的 **Wi‑Fi 热点**。

**USB 主机接口选择（仅 ESP32-P4）：**

ESP32-P4 默认使用 USB Host 高速（HS）接口。若需通过 GPIO26/27 使用全速（FS），在 menuconfig 中启用 `Example Configuration → Use USB Host Full Speed`，将使用 FS_PHY2（D+ GPIO27，D− GPIO26）。

**Wi‑Fi 名称与密码：**

在 `menuconfig` 的 `4G Modem WiFi Config` 中修改。

1. 默认热点名：`ESP-USB-4G`
2. 默认无密码

## 启用 AT 命令

在 menuconfig 中开启 `Example Configuration → USB ECM AT Command`，填写正确的接口号，重新编译烧录。程序会周期性打印模组信号质量等信息。

## 启用下载测速

在 menuconfig 中开启 `Example Configuration → USB ECM Download Speed Test`。ECM 侧 DHCP 成功后，程序会从远端下载测试文件并统计速率。

## 示例输出（CH397A / 多 configuration）

```
I (475) iot_usbh_ecm: USB ECM network interface init success
I (485) ECM_4G_MODULE: IOT_ETH_EVENT_START
W (856) ENUM: Device has more than 1 configuration
I (856) ECM_4G_MODULE: USB device configuration value set to 2
I (862) iot_usbh_ecm: ECM interface found: VID: 1A86, PID: 5397, IFNUM: 0
I (880) iot_usbh_ecm: Parsed MAC address: 3C:AB:72:59:A0:D4
I (2708) ECM_4G_MODULE: IOT_ETH_EVENT_CONNECTED
I (3734) ECM_4G_MODULE: GOT_IP
I (3748) iot_eth.netif_glue: ETHIP:192.168.137.139
I (3759) iot_eth.netif_glue: ETHGW:192.168.137.1
I (3765) iot_eth.netif_glue: Main DNS: 192.168.137.1
```

具体 IP 与上游网络有关（如 PC 共享上网、路由器或运营商）。
