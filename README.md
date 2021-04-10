# Asus B460i / i7 10700 / 5500xt / Intel AX200

**Thanks:** [@jalalabdulaziz](https://github.com/jalalabdulaziz/ROG-Strix-B460-I)

**WARNING: You must add your own serial number in `EFI/OC/config.plist`.** Related fields are: PlatformInfo - MLB/SystemProductName/SystemSerialNumber/SystemUUID. You can generate some random numbers by [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). See [official guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html#platforminfo) for details.

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

- **OS:** Mac OS Big Sur 11.2.2 
- **Bootloader:** OpenCore 0.6.8

## What's working

- [x] iGPU and dGPU
- [x] Restart / Shutdown
- [x] Sleep / Wake
- [x] WiFi & Bluetooth (AX200) **See notice below.**
- [x] USB

## Notice

### SSD

Samsung 970 Evo Plus's firmware must >= 2B2QEXm7 which fixes serious problem on hackintosh. You can check or upgrade it by [Samsung Magician](https://www.samsung.com/semiconductor/minisite/ssd/product/consumer/magician/) on Windows.

### USB

1. Use [Sniki's version](https://github.com/Sniki/OS-X-USB-Inject-All) of `USBInjectAll` since the [original one](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/) is no longer maintained.
2. This board usb device id is `0xa3af` which is not supported by default in `USBInjectAll.kext` and `XHCI-unsupported.kext`, hence rename is required in both kext `8086_a2af` → `8086_a3af`, `0xa2af8086` → `0xa3af8086`.

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
