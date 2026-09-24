# Realtek RTD Portable Display Project - Agent Context & Rules

## Project Identity & Hardware Baseline
This repository contains firmware, tools, documentation, schematics, and reverse-engineering utilities for DIY and commercial **Portable Displays / Monitors** powered by Realtek RTD display controller scaler boards.

### Primary Target Hardware: PCB-800869
* **Board Model**: `PCB-800869` (Manufacturer: Hong Kong Tianyu Longtong Technology Co., Ltd. / airdisp.com).
* **Dimensions**: 89.5 mm × 47.5 mm × 5 mm.
* **Main Scaler IC**: Realtek **RTD2556** (8051 MCU core with proprietary display pipeline).
* **Target Scaler in RTD Customer Tool**: **`RTD2537T-CG`** (Shares identical 8051 EMCU core, Page 00, Page 10 Pin Share, and Page FF PWM Backlight registers).
* **CRITICAL POWER CONSTRAINT**: **USB 5V ONLY!**
  * `PCB800869` is powered via the USB Type-C 5V port.
  * ⚠️ **NEVER** recommend or apply 12V DC to `PCB800869`. (12V DC is only for PCB800860 / PCB800862 / PCB800863).
* **Ports**:
  * Dual Type-C: 1× 5V Power Input, 1× Full-featured Type-C (DP Alt-Mode Video + USB Data).
  * 1× Mini-HDMI 1.4 video/audio input.
  * 1× 3.5mm headphone audio output jack.
  * 1× 3-in-1 rotary thumbwheel / jog dial (OSD Menu click, Volume/Brightness rotary).
  * 1× eDP connector (30-pin or 40-pin FPC).
  * 1× `CNA2` header (1.25mm 4-Pin): 2× 2W stereo speaker output.
  * 1× `JU1` header (1.25mm 4-Pin): 5V, GND, D+, D- touchscreen digitizer expansion.

---

## Flashing & Hardware Communication Rules
1. **GFX I2C via GPU (Verified Working)**:
   * The board CAN be flashed directly through an NVIDIA GPU over a standard HDMI cable using `Comm_GFXI2C.dll`.
   * Setup in RTD Customer Tool:
     * Access Method: `GFXI2C`
     * Graphics Detect Type: `Manual`
     * Graphics Type: `Nvidia`
     * Multi Monitor Select: `Auto Detect Realtek Monitor`
     * Multi Realtek Monitor Select: `First Realtek Monitor`
2. **Tool Functions in `RTDCustomerTool_V3.8`**:
   * **`NewCtrlReg`**: Live register read/write. Page selection via register `0x9F`.
     * `Page 00`: Input capture, sync, video pipeline.
     * `Page 10`: Pin Share / Pin Multiplexing (maps PWM timers to physical IC pins).
     * `Page FF`: EMCU (8051) PWM backlight duty cycle & frequency registers.
   * **`ISP` Tab**: Flashing tool.
     * Bank 0 row `...` button: Loads a complete `.bin` firmware file (automatically splits into Banks 0–7 and computes checksum).
     * Wrench & Hammer icon (🛠️): ISP options and settings.
     * Hand on chip icon (💾): Executes the Erase/Program/Verify cycle.
   * **`Flash` Tab** (7th icon on left sidebar): Direct SPI flash read/write tool used to dump/backup current firmware.
   * **`DPCD` Tab**: Reads and writes eDP panel TCON configuration data over the AUX channel.

---

## Firmware Architecture & Reverse-Engineering Ground Truth
* **Firmware File Size**: 589,824 bytes (576 KB).
* **Memory Architecture**: 8051 MCU core with **Bank Switching across 9 banks** (64 KB address window).
  * `0x00000 - 0x0FFFF`: Bank 0 (Common root code, reset vectors, interrupt table, dispatcher).
  * `0x10000 - 0x7FFFF`: Switched banks (DisplayPort stack, HDMI receiver, OSD font maps, panel tables).
  * `0x80000 - 0x90000`: Tail common code and OSD graphics.
* **Exact Known Offsets in `PCB800869-EDP30PIN-2LAN-1920X1080.bin`**:
  * `0x257A6`: Type-C Port EDID block (128 / 256 bytes).
  * `0x258A6`: HDMI Port EDID block (128 / 256 bytes).
  * `0x7FE80`: Preset brightness tables (`0x32` = 50% default, `0x64` = 100% max).
  * `0x2CFF0`: PWM scaling and duty cycle configuration routine (`0x0258` divisor).
  * `0x2D016`: Memory-mapped scaler register `0xFE23`.

---

## Anti-Hallucination Guidelines for Agents
1. **Never guess panel pinouts**: Always refer to the extracted tables in `01_Documentation_and_Specs/Extracted_Diagrams/`.
2. **Never claim RTD firmware can be built from C source code from scratch**: Realtek has not open-sourced the RTD2556 SDK. Modification is done via binary patching, register injection, and EDID editing.
3. **Always prioritize user safety**: Emphasize creating a backup (`my_backup.bin`) using the `Flash` tab before writing to SPI flash.
4. **Use local tools**: Utilize [tools/rtd_firmware_tool.py](file:///e:/rouf/hardware-project/portable%20display/tools/rtd_firmware_tool.py) for any 8051 disassembly or binary diffing.
