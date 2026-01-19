# How to Connect to CircuitPython Console

This guide explains how to view debug messages from your PropMaker lightsaber.

## What You'll See

The debug messages now include:
- **Startup sequence**: Component initialization (power, audio, LEDs, accelerometer, button)
- **Sound file loading**: List of all loaded WAV files with their indices
- **Mode transitions**: When entering startup, active, shutdown, and color change modes
- **Motion detection**: Clash and swing events with acceleration values
- **Button presses**: Short press and long press detection
- **Audio playback**: Which sound file is playing and whether it's looping
- **Color changes**: Current color index and RGB values

## Method 1: Using Mu Editor (Recommended for Beginners)

1. **Download and Install Mu Editor**
   - Go to https://codewith.mu/en/download
   - Download and install for Windows
   - Launch Mu Editor

2. **Connect to Your Device**
   - Plug in your PropMaker board via USB
   - Wait for CIRCUITPY drive to appear
   - In Mu, click the "Serial" button at the top toolbar
   - The serial console will open at the bottom showing debug output

3. **View Messages**
   - You should immediately see output from the running code
   - Messages will appear in real-time as the lightsaber operates
   - Press Ctrl+C to stop the running program
   - Press Ctrl+D to reload/restart the program

## Method 2: Using PuTTY (Windows)

1. **Find the COM Port**
   - Open Device Manager (Win+X, then select Device Manager)
   - Expand "Ports (COM & LPT)"
   - Look for "USB Serial Device (COMxx)" - note the COM port number (e.g., COM3, COM4)

2. **Install and Configure PuTTY**
   - Download from https://www.putty.org/
   - Launch PuTTY
   - Connection type: Select "Serial"
   - Serial line: Enter your COM port (e.g., COM3)
   - Speed: 115200
   - Click "Open"

3. **View Messages**
   - The console window will open showing output
   - To restart the program, press Ctrl+D in the console
   - To stop the program, press Ctrl+C

## Method 3: Using Windows Terminal with PowerShell

1. **Find the COM Port**
   - Same as Method 2 above

2. **Install Python and pyserial**
   ```bash
   pip install pyserial
   ```

3. **Connect Using Python**
   ```bash
   python -m serial.tools.miniterm COMx 115200
   ```
   Replace `COMx` with your actual COM port (e.g., COM3)

4. **Exit**
   - Press Ctrl+] to exit miniterm

## Method 4: Using Git Bash / MinGW (Your Current Environment)

1. **Install screen or picocom**
   - In Git Bash, you may need to use Windows Subsystem for Linux (WSL) or install additional tools
   - Alternatively, use one of the methods above which work better on Windows

2. **If you have WSL installed**:
   ```bash
   # Find the device (usually /dev/ttyACMx or /dev/ttyUSBx)
   ls /dev/tty*

   # Connect using screen
   sudo screen /dev/ttyACM0 115200

   # Or using picocom
   sudo picocom -b 115200 /dev/ttyACM0
   ```

## Troubleshooting

### No Output Appearing
1. Make sure the board is connected via USB
2. Verify CIRCUITPY drive is mounted
3. Try pressing Ctrl+D to reload the code
4. Check that code.py is on the CIRCUITPY drive

### "Access Denied" or "Port Busy" Error
- Close any other programs that might be using the serial port
- Close Mu Editor if running
- Unplug and replug the USB cable
- Try a different COM port or USB port

### Garbled Text
- Ensure baud rate is set to 115200
- Try disconnecting and reconnecting

### Device Not Found
- Install CircuitPython drivers if needed
- Check Device Manager to see if device is recognized
- Try a different USB cable (some cables are power-only)

## Understanding the Debug Output

### Startup Output Example:
```
=== LIGHTSABER STARTUP ===
Initializing external power...
External power enabled
Loading sound files...
Found 20 sound files:
  [0] /sounds/0_on.wav
  [1] /sounds/1_idle.wav
  ...
Initializing I2S audio...
Audio initialized
Initializing button...
Button initialized (long press = 1000ms)
...
=== INITIALIZATION COMPLETE ===
```

### Runtime Output Example:
```
--- MODE 0: STARTUP ---
Playing sound [0]: /sounds/0_on.wav (loop=False)
Igniting blade...

>>> SWING DETECTED <<< (accel: 156.3)
--- MODE SWING: Playing swing effect ---
Playing sound [12]: /sounds/swing2.wav (loop=False)

!!! CLASH DETECTED !!! (tap registered)
--- MODE HIT: Playing clash effect ---
Playing sound [5]: /sounds/clash5.wav (loop=False)
```

## Quick Reference

| Action | Keyboard Shortcut |
|--------|------------------|
| Stop program | Ctrl+C |
| Reload/Restart | Ctrl+D |
| Exit Mu serial | Click "Serial" button again |
| Exit PuTTY | Close window |
| Exit miniterm | Ctrl+] |
| Exit screen (WSL) | Ctrl+A then K |

## Recommended Workflow

1. **During Development**: Use Mu Editor for quick iteration
   - Edit code.py in Mu
   - Save (code auto-reloads)
   - Watch console for output
   - Fix any errors
   - Repeat

2. **For Extended Testing**: Use PuTTY
   - Leave console open while testing the lightsaber
   - Monitor for swings, clashes, and button presses
   - Log output if needed

3. **For Troubleshooting**: Review console output to see:
   - Which sensors are triggering
   - Acceleration values during motion
   - Which sound files are playing
   - Mode transitions and state changes
