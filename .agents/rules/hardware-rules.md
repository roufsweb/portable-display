# Hardware Electrical & Pinout Rules

## 1. Power Supply Voltage Constraints
* **`PCB800869`**: **STRICTLY 5V USB ONLY!**
  * Do NOT connect 12V DC barrel jacks to PCB800869. The on-board boost converters step 5V up to panel backlight voltages.
* **`PCB800860` / `PCB800862` / `PCB800863`**: **12V DC Standard Barrel Input** (2.1mm center-positive, 10V–15V range). Never exceed 15V.

## 2. eDP Ribbon Cable & Connector Integrity
* **eDP 30-Pin Connector**:
  * Carries 1-lane or 2-lane eDP signals, limited to resolutions up to 1920×1200 @ 60Hz.
  * Pin 20/21: `LED_VCC`, Pin 22: `BL_EN`, Pin 23: `BL_PWM`, Pin 24/25: `GND`.
* **eDP 40-Pin Connector**:
  * ⚠️ **Danger**: Distinguish between **4-lane eDP (High-res 2K/2.5K/4K)** vs **2-lane eDP + Touchscreen**.
  * Plugging a 4-lane high-res panel into a touch pinout can route 12V backlight power into the GPU data pairs, destroying the scaler IC or GPU.
* **Hot-Plugging Prohibition**:
  * NEVER connect or disconnect the flat flexible cable (FFC) to the LCD panel or board while power is applied. Always disconnect USB 5V first.

## 3. Communication Over I2C
* For GFX I2C through GPUs, limit DDC clock speed to **50 kHz** or **100 kHz** to prevent packet loss over long or unshielded HDMI cables.
