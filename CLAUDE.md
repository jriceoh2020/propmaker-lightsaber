# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a CircuitPython-based lightsaber project for the Adafruit PropMaker FeatherWing. The code runs on a microcontroller and controls NeoPixel LEDs, audio playback, and accelerometer-based motion detection to create an interactive lightsaber experience.

## Hardware Components

- **Board**: Adafruit PropMaker FeatherWing
- **Accelerometer**: LIS3DH (I2C, onboard)
- **Audio**: I2S output for WAV file playback
- **LEDs**:
  - External NeoPixel strip (configured for 100 pixels at C:\arduino\propmaker-lightsaber\code.py:72)
  - Onboard RGB LED (PWM on pins D10, D11, D12)
- **Input**: External button with debouncing support
- **External Power**: Controllable power rail for external components

## Code Architecture

### Main State Machine (code.py:96-177)

The application uses a mode-based state machine with the following states:

- **mode 0**: Startup - plays ignition sound and lights up blade pixel-by-pixel
- **mode 1**: Default/Active - monitors for taps, swings, and button presses
- **mode "hit"**: Clash detection - plays random clash sound (wavs 3-10) and flashes white
- **mode "swing"**: Motion detection - plays random swing sound (wavs 11-18)
- **mode 3**: Shutdown sequence - plays off sound and powers down blade
- **mode 4**: Off state - waits for button press to restart
- **mode 5**: Color selection - cycles through colors on short press, exits on long press

### Audio System (code.py:38-62)

WAV files in the `/sounds` directory are loaded into a sorted array at startup. Audio files are numbered/named according to their function:
- 0_on.wav: ignition sound
- 1_idle.wav: idle hum loop
- 2_off.wav: shutdown sound
- clash1-8.wav: impact sounds (indices 3-10)
- swing1-8.wav: motion sounds (indices 11-18)
- z_color.wav: color change mode loop (index 19)

### Motion Detection (code.py:18-19, 114-121)

Accelerometer thresholds can be adjusted:
- `HIT_THRESHOLD = 120`: Tap detection sensitivity (lower = more sensitive)
- `SWING_THRESHOLD = 130`: Motion detection sensitivity (lower = more sensitive)
- Detection uses combined X and Z axis acceleration: `accel_total = x * x + z * z`

### Color Configuration (code.py:21-30)

Seven predefined colors available. Indices:
- 0: RED, 1: YELLOW, 2: GREEN, 3: CYAN, 4: BLUE, 5: PURPLE, 6: WHITE
- `SABER_COLOR`: default blade color (index 3 = CYAN)
- `CLASH_COLOR`: clash flash color (index 6 = WHITE)

### Button Handling (code.py:64-69)

Uses Adafruit debouncer library with 1000ms threshold for long press detection:
- Short press in mode 1: triggers shutdown (mode 3)
- Long press in mode 1: enters color change mode (mode 5)
- Short press in mode 4 (off): powers on
- Short press in mode 5: cycles colors
- Long press in mode 5: exits color selection

## Development

### Running on Hardware

1. Connect the PropMaker FeatherWing via USB
2. Copy `code.py` to the CIRCUITPY drive that appears
3. Copy the `lib/` directory to CIRCUITPY/lib/
4. Copy the `sounds/` directory to CIRCUITPY/sounds/
5. The code will auto-reload and run immediately

### Modifying Sensitivity

Adjust threshold constants at code.py:18-19 then save to reload on device.

### Adding Sound Effects

Add WAV files to the `/sounds` directory following the naming convention. The code dynamically loads all .wav files (excluding those starting with '.') and sorts them alphabetically.

### Changing LED Count

Modify `num_pixels = 100` at code.py:72 to match your NeoPixel strip length.

## Dependencies (lib/ directory)

- adafruit_lis3dh.mpy: Accelerometer driver
- adafruit_debouncer.mpy: Button debouncing
- neopixel.mpy: LED control
- adafruit_bus_device/: I2C/SPI communication
- simpleio.mpy: Range mapping utilities
- adafruit_pixelbuf.mpy: NeoPixel buffer management
- adafruit_ticks.mpy: Timing utilities

## Key Behaviors

- Audio playback blocks during clash/swing effects (code.py:133-145)
- Color changes cycle through first 6 colors only (code.py:168, modulo 6)
- External power can be toggled to save battery when off (code.py:158, 163)
- Idle hum loops continuously in active mode (code.py:110, 138, 148)
- White flash overrides blade color during clashes (code.py:134)
