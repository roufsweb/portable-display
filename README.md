# Portable Display Hardware Project - Driver Boards, Tools & Firmware Guide

This repository contains all documentation, technical datasheets, programming tools, and firmware binary files for DIY and commercial **Portable Monitor (Display) controller driver boards** based on Realtek RTD scaler architectures (such as SIXTHHD-HD12, RTD2556, RTD2550, RTD2795, etc.).

---

## 📁 Repository Structure Overview

```text
portable display/
├── 01_Documentation_and_Specs/       # Official hardware datasheets & programming guides
│   ├── Extracted_Diagrams/           # High-resolution extracted CAD layouts, pinouts & schematics
│   │   ├── page_6_img_1_30.jpeg      # Complete functional & port callout diagram (1058x702)
│   │   ├── page_7_img_1_33.jpeg      # Dimensioned CAD technical drawing (1500x1059)
│   │   ├── page_10_img_1_40.png      # Official eDP 30-Pin connector pinout table
│   │   ├── page_11_img_1_44.png      # Official eDP 40-Pin connector pinout table
│   │   └── page_14_img_1_54.jpeg     # Keypad & IR remote schematic diagram
│   ├── User_Session_Screenshots/     # Documented session photos & RTD Customer Tool setup
│   │   ├── 01_user_pcb800869_board.png           # User's PCB800869 board (89.5x47.5mm)
│   │   ├── 02_rtd_tool_newctrlreg_pageff.png     # RTD Customer Tool NewCtrlReg Page FF
│   │   ├── 03_rtd_tool_gfxi2c_nvidia_setup.png   # Nvidia GPU GFX I2C configuration dialog
│   │   ├── 04_rtd_tool_page_select_menu.png      # Scaler register Page Select dropdown
│   │   ├── 05_rtd_tool_scaler_dropdown_list.png  # Scaler IC selection dropdown
│   │   ├── 06_rtd_tool_isp_tab_overview.png      # RTD Tool ISP flashing tab and controls
│   │   ├── 07_rtd_tool_flash_error_0xa.png       # ErrorCode:0xA when auto-detecting multi-monitor
│   │   ├── 08_rtd_tool_gfxi2c_adjust_user_select.png # Solution: Setting Multi RT Monitor to User Select
│   │   ├── 09_rtd_tool_gfxi2c_select_display2_output256.png # Selecting \\.\DISPLAY2 Output ID:256
│   │   ├── 10_rtd_tool_flash_mismatch_error_0xd03.png # ErrorCode:0xD03 on Auto flash detect (whitelist mismatch)
│   │   └── 11_rtd_tool_read_total_progress_ok.png # Successful Read Total (8 Banks / 512KB) completed with OK!
│   ├── PCB800869_Images/             # Official high-resolution photos downloaded from manufacturer
│   │   ├── big_2023126133141.jpg     # PCB800869 30-Pin eDP board top view
│   │   └── big_2023126133825.jpg     # PCB800869 40-Pin eDP board top view
│   ├── pcb800860.pdf                 # PCB800860: Type-C + HDMI to eDP 30P/40P + LVDS 40P
│   ├── pcb800862.pdf                 # PCB800862: Type-C + HDMI to LVDS 30P + LVDS 40P
│   ├── pcb800863.pdf                 # PCB800863: Type-C + HDMI to 50PIN TTL LCD
│   └── RTD programmer instructions.pdf # RTD ISP programmer connection & flashing guide
│
├── 02_RTDCustomerTool_V3.8/          # Realtek RTD ISP Customer Tool (Flashing software)
│   ├── RTDTool.exe                   # Main Windows flashing and ISP utility (v3.8)
│   ├── Comm/ & Comm.dll              # Communication drivers (USB, FTDI, Type-C, I2C, Aux)
│   ├── PlugIn/                       # Flashing, EDID, HDCP, GPIO, OSD & Gamma plugins
│   ├── RTDScaler/                    # Scaler register databases (RTD2172, 2525, 2537, 2775, etc.)
│   └── IspDebugLog.txt               # Flashing and verification logs
│
├── 03_Firmware/                      # Screen & board firmware binaries (.bin files)
│   ├── Board_Firmware_Rouf/          # Board-specific firmware curated by Rouf
│   │   ├── PCB800860_eDP/            # Firmware for PCB800860 (1920x1280 3:2 eDP)
│   │   ├── PCB800862_LVDS/           # Firmware for PCB800862 (LVDS panels from 1024x600 to 1080p, iPad 2)
│   │   ├── PCB800869_eDP/            # Firmware for PCB800869 (eDP 30P/40P, up to 2.5K/4K, iPad 3/4/5 Retina)
│   │   └── PCB800873_TTL_50Pin/      # Firmware for PCB800873 (50-Pin TTL LCDs: AT070TN92, AT080TN52, etc.)
│   │
│   └── EDP_RTD2556_RTD2550/          # General eDP driver firmware
│       ├── RTD2550/                  # RTD2550 scaler firmware (14", 15.6", 17.3" FHD)
│       ├── RTD2556/                  # RTD2556 scaler firmware (1366x768 up to 1920x1200)
│       └── Panel_Specific_eDP/       # Named panel firmware (B140HAN, LP140WF6, NT156FHM, N133HSE)
│
└── _Original_Archives/               # Untouched original zip files (Backup)
    ├── EDP.zip
    ├── PCB800860-PCB800862-PCB800863-spec-programmer-instructions.zip
    ├── PCB800869_from_Rouf.zip
    └── RTDCustomerTool_V3.8_20221101.zip
```

---

## 🛠️ Hardware Driver Board Specifications

All boards are produced by **Hong Kong Tianyu Longtong Technology Co., Ltd. (香港天宇朗通科技有限公司)**, Shenzhen Huaqiangbei.

### Common Architecture & Features across PCB800860 / PCB800862 / PCB800863:
- **Main Scaler IC**: `SIXTHHD-HD12` (Realtek RTD family)
- **Board Dimensions**: 99 mm × 66 mm × 8 mm
- **Power Input (`JP2`)**:
  - Standard DC 12V barrel jack (center positive, 2.0 mm / 2.1 mm inner pin).
  - Operating voltage: **10V – 16V** (Recommended: **12V**).
  - ⚠️ **CAUTION**: Exceeding 15V–16V will permanently destroy on-board DC-DC buck regulators and components.
- **Video Input Ports**:
  - **Mini HDMI 1.4 (`JH2`)**: Supports standard digital audio/video up to 1080p/2K/60Hz.
  - **Full-featured USB Type-C (`JD2`)**: 24-pin connector. Supports DisplayPort Alternate Mode (DP Alt-Mode) for laptops, PCs, Samsung DeX, Nintendo Switch, and smartphones. Provides **5V / 1A reverse charging** to the connected phone/device. Requires a full-featured USB-C 3.1 Gen 2 / Thunderbolt cable (standard charge-only cables will not transmit video).
  - Auto-switching between HDMI and Type-C based on the last connected active signal.
- **USB Hub & Peripherals**:
  - When the host device connects via Type-C, the board activates its internal USB hub:
    - 2× external USB Type-A ports (`JU11`, `JU13`) for mouse, keyboard, or flash drive.
    - 1× internal 6-pin 1.25mm connector (`JU1`) supporting 3.3V / 5V selectable touch digitizer input.
- **Audio System**:
  - **Headphone Jack (`JA2`)**: Standard 3.5 mm stereo audio output.
  - **Speaker Header (`CNA2`)**: 4-pin 1.25mm connector for 2× 2W (8Ω / 4Ω) stereo speakers.
- **Keypad & OSD Control (`JK2`)**:
  - 10-pin 1.25mm connector supporting keypad board (PCB800023 with 5 keys: Power, Menu, +, -, Exit/Source) and IR remote control receiver.

---

### Board Model Differences:

| Model | Output Interface | Max Resolution | Target Screen Types |
| :--- | :--- | :--- | :--- |
| **PCB800860** | eDP 30-Pin + eDP 40-Pin + LVDS 40-Pin | 2560×1440 (eDP 40P)<br>1920×1200 (eDP 30P)<br>1366×768 (LVDS 40P) | Modern laptop IPS/OLED screens (11.6"–17.3"), tablet screens |
| **PCB800862** | Standard LVDS 30-Pin (Dual Channel) + LVDS 40-Pin | 1920×1080 | Older laptop LCDs, desktop monitor panels, iPad 2 (LP097) |
| **PCB800863** | 50-Pin TTL | 1024×600 | 7.0"–8.0" small industrial/automotive LCDs (e.g., AT070TN92) |
| **PCB800869** | High-performance eDP (2-Lane 30P & 4-Lane 40P) | 2560×1600 (2.5K), 2160×1440 (3:2), iPad Retina (2048×1536) | High-res gaming displays (144Hz B156HAN13.1), iPad 3/4/5th Retina |
| **PCB800873** | 50-Pin TTL | 1024×600 / 800×480 | AT070TN92, AT080TN52 TTL panels |

---

## 🔌 Key Connector Pinout Reference

### Internal Touch Screen Connector (`JU1` - 1.25mm 6-Pin):
| Pin | Name | Description |
| :---: | :---: | :--- |
| 1 | **5V** | Touchscreen VCC (5V power) |
| 2 | **GND** | Ground |
| 3 | **D+** | USB Data Plus |
| 4 | **D-** | USB Data Minus |
| 5 | **NC** | Not Connected |
| 6 | **3.3V** | Touchscreen VCC (3.3V power) |

### Keypad & IR Remote Connector (`JK2` - 1.25mm 10-Pin):
| Pin | Name | Description |
| :---: | :---: | :--- |
| 1 | **IR-VCC** | 5V power for IR Receiver |
| 2 | **GND** | Ground |
| 3 | **IR** | IR Signal Input |
| 4 | **POWER** | Power Key Button |
| 5 | **MENU** | Menu / Confirm (Also Screen scaling toggle on Android) |
| 6 | **+ (UP)** | Volume / Brightness Up (Left in OSD) |
| 7 | **- (DOWN)**| Volume / Brightness Down (Right in OSD) |
| 8 | **EXIT** | Exit / Source switch (HDMI <-> Type-C) |
| 9 | **LED-R** | Red LED (Standby / No signal) |
| 10 | **LED-G** | Green LED (Active Signal / Working) |

### Speaker Output Connector (`CNA2` - 1.25mm 4-Pin):
| Pin | Name | Description |
| :---: | :---: | :--- |
| 1 | **LOUT+** | Left Speaker Positive (+) |
| 2 | **LOUT-** | Left Speaker Negative (-) |
| 3 | **ROUT+** | Right Speaker Positive (+) |
| 4 | **ROUT-** | Right Speaker Negative (-) |

---

## ⚡ Firmware Flashing & ISP Programming Guide

To change resolution, adapt a new LCD panel, or repair a bricked board:

### 1. Hardware Needed:
- Realtek RTD ISP Programmer Dongle (USB to I2C/ISP).
- HDMI cable or VGA cable connecting programmer to driver board ISP port.
- 12V DC power supply powering the driver board.

### 2. Flashing Procedure (Using `RTDTool.exe` in `02_RTDCustomerTool_V3.8`):
1. **Connect Programmer**: Plug the USB programmer into your Windows PC, and connect its HDMI/VGA output cable to the driver board. Power on the driver board with 12V DC.
2. **Launch Software**: Run [RTDTool.exe](file:///e:/rouf/hardware-project/portable%20display/02_RTDCustomerTool_V3.8/RTDTool.exe).
3. **Verify Drivers & Communication**:
   - Check the bottom left status icons in RTDTool. Both communication and device icons must show green checkmarks (✔️).
   - If red (❌), reinstall drivers via the `Comm/` folder or check USB connection.
4. **Load Firmware**:
   - Click the Load / Browse button (Step 3 in manual).
   - Navigate to `03_Firmware/` and select the `.bin` firmware matching your board and screen resolution/panel.
5. **Burn / Program**:
   - Click the Burn / ISP Auto button (Step 4).
   - Wait for the progress bar to complete. A green **"OK"** notification will appear when flashing succeeds.
6. **Reboot**: Power-cycle the 12V supply to reboot the driver board with the new firmware.

---

## 🖥️ Flashing & Reading Firmware via GPU (GFX I2C - No Programmer Needed!)

On systems equipped with dedicated GPUs (e.g. NVIDIA GeForce), RTD Customer Tool can read and flash the scaler board **directly over standard HDMI** using DDC/CI I2C commands (`Comm_GFXI2C.dll`).

### Setup & Multi-Monitor Configuration:
1. In the top toolbar, select **Access method**: `GFXI2C`.
2. Scaler: `RTD2537T-CG`.
3. Open **Communication Option** -> **GFXI2C Adjust Option**:
   - **Graphics Detect Type**: `Manual`
   - **Graphics Type**: `Nvidia`
   - **Multi Monitor Select**: `Auto Detect Realtek Monitor`
   - **Multi Realtek Monitor Select**: `User Select` (⚠️ **CRITICAL**)
4. In the `Multi RT Monitor Select` dialog, select your portable display:
   - Select `Device Name: \\.\DISPLAY2, Output ID:256` (or whichever output ID corresponds to the HDMI port).

### Troubleshooting `Enter Isp Mode fail! ErrorCode:0xA`:
- **The Problem**: When `Auto Detect Realtek Monitor` or `First Realtek Monitor` is chosen, the graphics driver probes `DISPLAY1` first (the laptop's internal eDP screen or desktop primary monitor). Since `DISPLAY1` is not a Realtek scaler, the ISP handshake command times out and fails with `ErrorCode:0xA`.
- **The Fix**: Setting `Multi Realtek Monitor Select` -> `User Select` and explicitly picking `\\.\DISPLAY2` forces the GPU to communicate directly with the RTD2556 board over HDMI.

---

## 💾 Factory Firmware Backup & Deep Binary Analysis

### 1. Complete Physical Board Backup (512 KB / 8 Banks):
- **Root Backup**: [full-backup.bin](file:///e:/rouf/hardware-project/portable%20display/full-backup.bin) (524,288 bytes)
- **Archive Copy**: [PCB800869_FACTORY_FULL_BACKUP_512KB.bin](file:///e:/rouf/hardware-project/portable%20display/03_Firmware/PCB800869_FACTORY_FULL_BACKUP_512KB.bin)
- **Status**: 100% complete SPI flash read across all 8 memory banks (Bank 0 – Bank 7), verified with `OK` status.

### 2. Extracted Hardware Identification & EDIDs:
- **Type-C Port EDID** (Offset `0x25792` in Bank 2):
  - Model Name: `RTK FHD`
  - Manufacturer Code: `RTK` (Realtek), Product Code: `0xA1BF`
  - Serial Number: `L56051794302`
  - Native Resolution: **1920 × 1080 @ 59.98 Hz** (Pixel Clock: 138.60 MHz, Frame: 2080×1111)
- **HDMI Port EDID** (Offset `0x25892` in Bank 2):
  - Model Name: `RTK FHD`
  - Manufacturer Code: `RTK` (Realtek), Product Code: `0x2A3B`
  - Serial Number: `J257M96B00FL`
  - Native Resolution: **1920 × 1080 @ 60.00 Hz** (Pixel Clock: 148.50 MHz, Frame: 2200×1125 CEA-861)

### 3. Root Cause of the OSD Brightness Bug:
- **Analysis of Bank 2 PWM Engine (`0x2CFE8`)**:
  - In `full-backup.bin`, the MCU loads registers `R7=#0x58` and `R6=#0x02` before calling the PWM scaling math routine `0x03B4`. This configures a PWM divisor of **`0x0258` (decimal 600)**.
  - Standard laptop 30-pin FHD eDP panels (BOE NT156FHM, NV156FHM, AUO B156HAN) expect standard 0–100% duty cycle scaling.
  - When the scaler divides duty cycles over 600 instead of 100, the OSD slider either barely changes brightness, jumps erratically at the very end of the range, or fails to dim smoothly!
- **Proof in the Official Firmware**:
  - In [PCB800869-EDP30PIN-NT156FHMN41-1920X1080.bin](file:///e:/rouf/hardware-project/portable%20display/03_Firmware/Board_Firmware_Rouf/PCB800869_eDP/PCB800869-EDP30PIN-NT156FHMN41-1920X1080.bin), the manufacturer specifically fixed this exact bug by:
    1. Patching the PWM divisor at `0x2CFFC` from `0x0258` (600) to **`0x0064` (100)**!
    2. Patching Bank 7 at `0x7FE9A` from `0x0258` (600) to **`0x0064` (100)**!
    3. Tuning the default brightness preset at `0x7FE8C` from `0x32` (50) to `0x0A` (10)!

### 4. Custom Patched Firmware Created:
- **Patched Binary**: [PCB800869_BRIGHTNESS_FIXED.bin](file:///e:/rouf/hardware-project/portable%20display/PCB800869_BRIGHTNESS_FIXED.bin) (524,288 bytes)
- **Archive Copy**: [03_Firmware/PCB800869_BRIGHTNESS_FIXED.bin](file:///e:/rouf/hardware-project/portable%20display/03_Firmware/PCB800869_BRIGHTNESS_FIXED.bin)
- **Modifications**:
  - `0x2CFEB` (Bank 2): `0x58` ➔ `0x64`
  - `0x2CFED` (Bank 2): `0x02` ➔ `0x00` (PWM scaling divisor changed from 600 to 100)
  - `0x7FE96` (Bank 7): `0x58` ➔ `0x64`
  - `0x7FE98` (Bank 7): `0x02` ➔ `0x00` (OSD brightness range changed from 600 to 100)
- **Integrity**: 100% of all other bytes (EDID, serial numbers, panel timings, audio settings) remain untouched.

---

## 📊 Complete Firmware Cross-Reference Matrix

### 1. `PCB800869_eDP` (High-Resolution eDP Collection):
| Resolution | Aspect Ratio | Interface | Panel / Device Target | Firmware File Name |
| :--- | :---: | :---: | :--- | :--- |
| **2560×1600** | 16:10 | eDP 30P (2-Lane) | BOE NE140QDM-N6A (14.0" 2.5K) | `PCB800869-EDP30PIN-2LAN-2560X1600-NE140QDM_N6A.bin` |
| **2560×1600** | 16:10 | eDP 40P (4-Lane) | Universal 2.5K 16:10 | `PCB800869-EDP40PIN-4LAN-2560X1600.bin` |
| **2560×1440** | 16:9 | eDP 40P (4-Lane) | Sharp IGZO LQ133T1JW19 (13.3" 2K) | `PCB800869-EDP40PIN-4LAN-2560X1440-LQ133T1JW19.bin` |
| **2560×1440** | 16:9 | eDP 40P (4-Lane) | BOE NV133QHM-A51 (13.3" 2K) | `PCB800869-EDP40PIN-4LAN-2560X1440-NV133QHM_A51 .bin` |
| **2560×1440** | 16:9 | eDP 40P (4-Lane) | Universal 2K 16:9 | `PCB800869-EDP40PIN-4LAN-2560X1440.bin` |
| **2256×1504** | 3:2 | eDP 40P (4-Lane) | BOE NE135GXM-N61 (Surface Laptop 13.5") | `PCB800869-EDP40PIN--4LAN-NE135GXMN61-2256X1504.bin` |
| **2240×1400** | 16:10 | eDP 40P (4-Lane) | M140NWH1 R0 (14.0" 2.2K) | `PCB800869-EDP40PIN-4LAN-2240X1400-M140NWH1R0.bin` |
| **2160×1440** | 3:2 | eDP 40P (4-Lane) | Surface Pro 3 (12.0" 2K) | `PCB800869-EDP 40PIN -4LAN-2160X1440.bin` |
| **2048×1536** | 4:3 | eDP 40P (4-Lane) | Apple iPad 3 & 4 Retina Panel (9.7") | `PCB800869.7-3-4代-EDP40PIN-4LAN-2048X1536.bin` |
| **1536×2048** | 3:4 | eDP | Apple iPad 5th Gen (2017 9.7") | `PCB800869-IPAD5TH-EDP 1536X2048.bin` |
| **1920×1200** | 16:10 | eDP 30P (2-Lane) | Universal 16:10 FHD+ | `PCB800869-EDP30PIN-2LAN-1920X1200.bin` |
| **1920×1080** | 16:9 | eDP 40P (4-Lane) | AUO B156HAN13.1 (144Hz High Refresh) | `PCB800869-EDP40PIN-4LAN-1920X1080-B156HAN13.1.bin` |
| **1920×1080** | 16:9 | eDP 30P (2-Lane) | BOE NV156FHM-N22 | `PCB800869-EDP 30PIN 1920X1080-NV156FHM-N22.bin` |
| **1920×1080** | 16:9 | eDP 30P (2-Lane) | BOE NT156FHM-N41 | `PCB800869-EDP30PIN-NT156FHMN41-1920X1080.bin` |
| **1920×1080** | 16:9 | eDP 30P (2-Lane) | M156OMN237-M870 | `PCB800869-EDP 30PIN 1920X1080-M156OMN237-M870.bin` |
| **1920×1080** | 16:9 | eDP 30P (2-Lane) | SL156BFHM40D | `PCB800869-EDP1920X1080-SL156BFHM40D.bin` |
| **1920×1080** | 16:9 | eDP 30P (2-Lane) | Universal 1080p | `PCB800869-EDP30PIN-2LAN-1920X1080.bin` |
| **1600×900** | 16:9 | eDP 30P (2-Lane) | Universal HD+ | `PCB800869-EDP30PIN-2LAN-1600X900.bin` |
| **1366×768** | 16:9 | eDP 30P (2-Lane) | Universal WXGA | `PCB800869-EDP30PIN-2LAN-1366X768.bin` |
| **1280×800** | 16:10 | eDP 30P (2-Lane) | Universal WXGA | `PCB800869-EDP30PIN-2LAN-1280X800.bin` |

---

### 2. `PCB800862_LVDS` (LVDS Panels Collection):
| Resolution | Interface Channel | Target Screen / Panel | Firmware File Name |
| :--- | :---: | :--- | :--- |
| **1920×1080** | 2-Ch 8-Bit | Standard 1080p LVDS | `PCB800862-LVDS-2CH-8BIT-1920X1080.bin` |
| **1680×1050** | 2-Ch 8-Bit | 20"–22" WSXGA+ | `PCB800862-LVDS-2CH-8BIT-1680X1050.bin` |
| **1680×1050** | 2-Ch 6-Bit | WSXGA+ (6-bit) | `PCB800862-LVDS-2CH-6BIT-1680X1050.bin` |
| **1600×900** | 2-Ch 6-Bit | 17.3" / 20" HD+ | `PCB800862-LVDS1600X900-2CH-6BIT.bin` |
| **1440×900** | 2-Ch 6-Bit | 19" 16:10 WXGA+ | `PCB800862-LVDS1440X900-2CH-6BIT.bin` |
| **1366×768** | 1-Ch 8-Bit | 15.6" / 18.5" WXGA | `PCB800862-LVDS1366X768-1CH-8BIT.bin` |
| **1366×768** | 1-Ch 6-Bit | Laptop 1-Ch 6-Bit | `PCB800862-LVDS1366X768-1CH-6BIT.bin` |
| **1280×800** | 1-Ch 8-Bit | 12.1" / 14.1" WXGA | `PCB800862-LVDS1280X800-1CH-8BIT.bin` |
| **1280×800** | 1-Ch 6-Bit | Laptop 1-Ch 6-Bit | `PCB800862-LVDS1280X800-1CH-6BIT.bin` |
| **1024×768** | 1-Ch 8-Bit | Innolux EJ080NA-01D (8.0") | `PCB800862-LVDS1024X768-1CH-8BIT-EJ080NA-01D.bin` |
| **1024×768** | 1-Ch 6-Bit | LG LP097X02 (Apple iPad 2 Panel) | `PCB800862-LVDS-LP097-2代.bin` |
| **1024×768** | 1-Ch 6-Bit | Standard 4:3 XGA | `PCB800862-LVDS-1024X768-1CH-6BIT.bin` |
| **1024×600** | 1-Ch 8-Bit | Innolux EJ070NA-01J (7.0" 40P) | `PCB800862-40PIN-1024X600-1CH-8BIT-EJ070NA-01J.bin` |
| **1024×600** | Dual 8-Bit | 7.0"–10.1" WSVGA | `PCB800862-LVDS1024X600-D8BIT.bin` |

---

### 3. `PCB800873_TTL_50Pin` (TTL Small Display Collection):
| Resolution | Interface | Target Screen | Firmware File Name |
| :--- | :---: | :--- | :--- |
| **800×480** | 50-Pin TTL | Innolux AT070TN92 (7.0" WVGA) | `PCB800873-50PIN-AT070TN92-800X480.bin` |
| **800×600** | 50-Pin TTL | Innolux AT080TN52 (8.0" SVGA) | `PCB800873-50PIN-AT080TN52-800X600.bin` |
| **1024×600** | 50-Pin TTL | Generic 50P WSVGA Config 1 | `PCB800873-50PIN-1024X600-1.bin` |
| **1024×600** | 50-Pin TTL | Generic 50P WSVGA Config 2 | `PCB800873-50PIN-1024X600-2.bin` |

---

### 4. `EDP_RTD2556_RTD2550` (RTD Scaler Generic & Panel Bins):
- **RTD2550 Subfolder**: Contains firmware for RTD2550 boards across 14.0", 15.6", and 17.3" FHD panels (LG LP140WF6-SPB2, BOE NV156FHM, 270 nits brightness preset).
- **RTD2556 Subfolder**: Contains firmware for RTD2556 boards (HDMI/VGA inputs) for resolutions from 1366×768 up to 1920×1200, including no-logo boot, 5-key OSD configs, and audio presets.
- **Panel_Specific_eDP Subfolder**: Tested firmware bins for popular laptop panels:
  - `b140hHAN01.1.bin`: AU Optronics B140HAN01.1 (14.0" FHD AHVA/IPS)
  - `LP140WF6-SPB1-A9D9.bin` / `lp140wf6-spd1.bin`: LG Display LP140WF6 series (14.0" FHD IPS)
  - `NT156FHM-N41.bin` / `发达液晶专用N42.bin`: BOE NV156FHM-N42 (15.6" FHD IPS)
  - `N133HSE.bin`: Innolux N133HSE (13.3" FHD IPS)
