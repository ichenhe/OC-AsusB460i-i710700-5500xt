# Asus B460i / i7 10700 / 5500xt / Intel AX200

**Thanks:** [@jalalabdulaziz](https://github.com/jalalabdulaziz/ROG-Strix-B460-I), [@roederja](https://github.com/roederja/asus-rog-strix-b460I-hackintosh)

⚠️ **WARNING1: You must add your own serial number in `EFI/OC/config.plist`.** Related fields are: `PlatformInfo - MLB/SystemProductName/SystemSerialNumber/SystemUUID`. You can generate some random numbers by [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). See [official guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html#platforminfo) for details.

⚠️ **WARNING2:  Aura and part of the front USB is blocked.** See USB section for details.

## Hardware

- **Motherboard:** Asus ROG B460-i
  - Ethernet: Intel I219-V 1Gb
  - Audio Codec: Realtek ALCS1220A (Layout 7)
  - Wireless (Bluetooth): Intel Wi-Fi 6 AX200
- **CPU:** Intel Core i7 10700
- **dGPU:** Sapphire RX 5500XT 8GB
- **Storage:** Samsung 970 Evo Plus 2TB
- **RAM:** CRUCIAL DDR4 3200 8GB*2

## Software

- **OS:** Mac OS Big Sur 11.3
- **Bootloader:** OpenCore 0.6.8

## What's working

- [x] iGPU and dGPU
- [x] Restart / Shutdown
- [x] Sleep / Wake
- [x] WiFi & Bluetooth (AX200) **See notice below.**
- [x] USB

## USB

1. This board usb device id is `0xa3af` which is not supported by default in `USBInjectAll.kext`  (If you decide to use) and `XHCI-unsupported.kext`, hence rename is required in both kext `8086_a2af` → `8086_a3af`, `0xa2af8086` → `0xa3af8086`.
2. Since Big Sur 11.3, the patch that removes 15 usb interfaces limitation [has been invalidated and the author no longer maintain](https://github.com/acidanthera/bugtracker/issues/1514).

**So we cannot continue to use USBInjectAll.kext, instead, we must create a usb map to choose which interfaces to enable, and no more than 15.** If you prefer to do this work by your self, please refer to the [documents](https://dortania.github.io/OpenCore-Post-Install/usb/#macos-and-the-15-port-limit).

According to my actual situation, this repo shut down Aura and the second front USB. Built-in Bluetooth and other interfaces are fine. If you want to make a little change, please edit `Kexts/USB-Map.kext/Contents/Info.plist`. These are the parameters of some interfaces:

| Interface    | Type           | port     |
| ------------ | -------------- | -------- |
| Aura         | 255 (internal) | 0B000000 |
| Front2 (2.0) | 0              | 02000000 |
| Front2 (3.0) | 3              | 12000000 |

## Notice

### SSD

Samsung 970 Evo Plus's firmware must >= 2B2QEXm7 which fixes serious problem on hackintosh. You can check or upgrade it by [Samsung Magician](https://www.samsung.com/semiconductor/minisite/ssd/product/consumer/magician/) on Windows.

### BIOS

[RTCMemoryFixup](https://github.com/acidanthera/RTCMemoryFixup/releases/tag/1.0.6) was added to fix BIOS reset issue as described [here](https://dortania.github.io/OpenCore-Post-Install/misc/rtc.html#finding-our-bad-rtc-region).

### WiFi

Thanks to [AirportItlwm](https://github.com/OpenIntelWireless/itlwm/releases) now we can enable on-board Wireless NIC card (intel AX200) on hackintosh, **but only at a very low speed (about 50Mbps) so far.** This issue may be improved in the future, please follow the kext project motioned above.

## BIOS Settings

See [official guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html#intel-bios-settings) for details.

## Debug

If you encounter any error, please edit the `config.plist` to enable log outputs so that you will get more information about what happened.

Add `-v debug=0x100 ` to `NARAM - Add - 7C436110-AB2A-4BBB-A880-FE41995C9F82 - boot-args`, see [official guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html#nvram) for details.

## Kexts

| Item                                                         | Version |
| ------------------------------------------------------------ | ------- |
| [AirportItlwm](https://github.com/OpenIntelWireless/itlwm/releases) | 1.2.0   |
| [AppleALC](https://github.com/acidanthera/AppleALC/releases) | 1.5.9   |
| [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases) | 1.1.2   |
| [IntelMausi](https://github.com/acidanthera/IntelMausi/releases) | 1.0.5   |
| [Lilu](https://github.com/acidanthera/Lilu/releases)         | 1.5.2   |
| [NVMeFix](https://github.com/acidanthera/NVMeFix/releases)   | 1.0.6   |
| [RTCMemoryFixup](https://github.com/acidanthera/RTCMemoryFixup/releases) | 1.0.7   |
| [USBInjectAll / XHCI-unsupported @Sniki](https://github.com/Sniki/OS-X-USB-Inject-All/releases) | 0.7.5   |
| [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases) | 1.2.2   |
| [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases) | 1.4.9   |

## Multiboot

Things have changed since OpenCore 0.6.6. Please see the [docs](https://dortania.github.io/OpenCore-Multiboot/) for a complete tutorial. 

Core step: Add `\EFI\Microsoft\Boot\bootmgfw.efi` to `Misc -> BlessOverride` in config.plist and ensure the existence of the above file.

> You can press `Ctrl+Enter` on the OpenCore's boot screen to set the default boot option.

---

**⚠️ Legacy only**

For simplicity, I recommend you use `DiskGenius` and `EasyUEFI` on Windows to configure UEFI multiboot.

Just:

1. Copy `EFI/OC` folder from your USB drive to HDD's EFI partition (`ESP`). Do NOT delete other folders e.g. `Microsoft`.
2. Add a new UEFI bootstrap by `EasyUEFI` and put it first.
   - Type: Linux or other systems
   - Describe: e.g. OpenCore
   - Target partition: Your HDD's EFI partition  (`ESP`)

## OpenCore's GUI

For debugging and speed, current version doesn't contain GUI configurations. You can add them yourself easily.

See [docs](https://dortania.github.io/OpenCore-Post-Install/cosmetic/gui.html) for details.
