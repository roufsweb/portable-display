---
name: rtd-firmware-engineer
description: >-
  Expert engineering runbook for Realtek RTD display scalers (RTD2556, RTD2550, SIXTHHD-HD12, PCB800869).
  Use when disassembling, patching, flashing, configuring GFX I2C via GPU, or troubleshooting brightness and panel timings.
---

# Realtek RTD Scaler Firmware & Hardware Engineering Skill

This skill contains technical procedures for working with Realtek display controllers (especially the **RTD2556 / PCB-800869** platform), flashing over GPU GFX I2C, live register tuning, and reverse-engineering 8051 bank-switched firmware.

---

## 1. Live GPU Flashing & Diagnostics (GFX I2C via NVIDIA GPU)

### Communication Prerequisites
* Board must be powered by **5V USB** on its dedicated power port.
* HDMI cable connects directly between PC's NVIDIA GPU output and the board's Mini-HDMI input.
* `RTD Customer Tool V3.8` located at `02_RTDCustomerTool_V3.8/RTDTool.exe`.

### Configuration Steps in RTDTool
1. Top Bar: Set `Access method:` to **`GFXI2C`**.
2. Top Bar: Set `Scaler:` to **`RTD2537T-CG`** (the compatible 25xx profile for RTD2556).
3. Menu: Go to **Communication Option** -> **GFXI2C Adjust Option**:
   * `Graphics Detect Type`: **Manual**
   * `Graphics Type`: **Nvidia**
   * `Multi Monitor Select`: **Auto Detect Realtek Monitor**
   * `Multi Realtek Monitor Select`: **First Realtek Monitor**
4. Bottom-left status indicator must display green **`OK`**.

---

## 2. Firmware Flashing Procedure (`ISP` Tab)

1. Navigate to the **`ISP`** tab on the left sidebar.
2. In the **`Bank 0`** row, click the browse button (**`...`**) on the far right.
3. Select the target `.bin` firmware file (576 KB / 589,824 bytes).
   * *The tool automatically populates Banks 0–7 and calculates the Big Bin Checksum.*
4. Verify Settings:
   * `Function`: **Auto**
   * `ISP Type`: **1 To 1**
5. Click the **Burn / Program** button (icon of a hand pressing down on an IC).
6. Wait for the erase, write, and verification cycle to complete (indicated by green OK in the log).
7. Power-cycle the 5V USB power to reboot the board with the new firmware.

---

## 3. Backing Up Existing SPI Flash (`Flash` Tab)

1. Click the **`Flash`** tab on the left sidebar (7th icon down, square chip icon).
2. Click **`Read Flash`** (or `Read All`).
3. Save the resulting file with a timestamped or descriptive name (e.g., `backup_working_firmware.bin`).

---

## 4. Live Register Diagnostics (`NewCtrlReg` Tab)

### Understanding Pages:
Register `0x9F` controls the Page Select register. In the `Page select:` dropdown:
* **`Page 00`**: Core video capture, input resolution, sync detector.
* **`Page 10 (Pin Share Register)`**: Pin multiplexing matrix. Determines whether physical pins output PWM, GPIO, or audio signals.
* **`Page FF (EMCU Embedded MCU Function)`**: Internal 8051 PWM Backlight controller:
  * Rows `FF30`, `FF40`, `FF50`: PWM duty cycle registers (`PWM0H_DUT`, `PWM0L_DUT`, `PWM1H_DUT`, `PWM1L_DUT`).
  * Click **`Run`** in the top-right corner to poll registers in real time while turning the physical jog dial.

---

## 5. Brightness Troubleshooting & Patching

### Diagnostic Matrix:
1. **PWM Duty Registers Change Live, Screen Stays Full Brightness**:
   * The panel is a VESA eDP 1.2+ screen requiring **DPCD AUX Channel brightness**.
   * Check the **`DPCD`** tab: inspect address `0x00720` - `0x00724`.
2. **PWM Duty Registers Do NOT Change When Turning Dial**:
   * OSD event handler disconnected or clamped.
   * Patch the 13-byte scale and divisor limits at `0x2CFF0` and `0x7FE80` using `tools/rtd_firmware_tool.py`.
   * Test the pre-patched profile: `PCB800869-EDP30PIN-NT156FHMN41-1920X1080.bin`.

---

## 6. Disassembly & Static Analysis (`tools/rtd_firmware_tool.py`)

Run the internal 8051 disassembly engine:
```bash
python tools/rtd_firmware_tool.py
```
* **Memory Map**:
  * Bank 0: `0x00000 - 0x0FFFF`
  * Data Partition: `0x25700` (EDID blocks: `0x257A6` for Type-C, `0x258A6` for HDMI)
  * Preset Tables: `0x7FE80`
  * PWM Routine: `0x2CFF0`
