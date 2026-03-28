| Supported Targets | ESP32-P4 | ESP32-S2 | ESP32-S3 |
| ----------------- | -------- | -------- | -------- |

**Language / 语言:** [English](README.md) | [中文](readmecn.md)

# USB Host ECM Example

This example demonstrates how to use [iot_usbh_ecm](https://components.espressif.com/components/espressif/iot_usbh_ecm) to connect a **4G module (ECM)** to the ESP32 **USB Host**. A common setup is an **external WCH CH397A USB‑to‑Ethernet (ECM) module** between the ESP32 and the cellular side, so the host sees a standard ECM NIC (e.g. VID `1A86`, PID `5397`).

Supported devices refer to https://docs.espressif.com/projects/esp-iot-solution/zh_CN/latest/usb/usb_host/usb_ecm.html

For devices where ECM is **not** on configuration descriptor **1** (many CH397A boards expose ECM on configuration **2**), enable `CONFIG_USB_HOST_ENABLE_ENUM_FILTER_CALLBACK` in menuconfig. The example filter sets configuration **2** when the device reports more than one configuration.

## Usage

**Hardware wiring (external CH397A module)**

Connect the ESP32 USB Host port (D+/D− and ground; supply 5 V per your board design) to the **CH397A module** USB device port.  Typical logical path:

```
┌──────────────┐   USB D+/D−    ┌─────────────┐
│   ESP32-xx   ├───────────────►│   CH397A    │  
│ (USB Host)   │    GND / 5V*   │ (USB ECM)   │        
└──────────────┘                └─────────────┘       
* Power rails must match your CH397A and ESP32 board requirements.
```

Once the ECM link is up and the ESP obtains an IP address (DHCP), the **Wi‑Fi hotspot** for sharing is enabled by this example.

**USB Host Interface Selection (ESP32-P4 only):**

For ESP32-P4, by default the USB Host uses High Speed (HS) interface. If you want to use Full Speed (FS) interface via GPIO26/27, enable `Example Configuration → Use USB Host Full Speed` in menuconfig. This configures USB Host to use FS_PHY2 (D+ on GPIO27, D− on GPIO26) instead of the default HS interface.

**Wi‑Fi name and password:**

You can modify Wi‑Fi configuration in `menuconfig` under `4G Modem WiFi Config`.

1. Default Wi‑Fi name: `ESP-USB-4G`
2. No password by default

## Enable AT commands

Enable AT commands with `Example Configuration → USB ECM AT Command` in menuconfig. Enter the correct interface number, recompile, and run. The program will periodically print the module's signal quality.

## Enable Download Speed Test

Enable `Example Configuration → USB ECM Download Speed Test` in menuconfig. After a successful DHCP on ECM, the program downloads a test file and measures throughput.

## Example Output (CH397A / multi-configuration)

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

IP addresses depend on your upstream (PC ICS, router, or carrier).
