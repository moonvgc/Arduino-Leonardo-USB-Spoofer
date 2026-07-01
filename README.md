# Arduino Leonardo USB Spoofer (Interactive + Safe)

A Python tool that disguises Arduino Leonardo (ATmega32U4) boards as a real mouse by cloning its USB identity — perfect for hardware macros, aim assists, and anything that needs to stay under the radar.

## Features

- **Full descriptor cloning** — Not just VID/PID, but also manufacturer/product strings, bcdUSB, bcdDevice, bMaxPower, and packet sizes are cloned exactly.
- **Live probing** — Plug in your real mouse, pick it from a list, and clone it with one click.
- **Safety engine** — Built-in checks prevent values that could damage the bootloader or USB Host Shield.
- **Cross-platform** — Runs on Windows, Linux, and macOS.
- **Automatic backups** — Original files are backed up as `.spoof.bak` and can be restored at any time.
- **Interactive menu** — Arrow key navigation, colored terminal UI.
- **CLI mode** — For scripting and power users (see `--help`).

## Requirements

- **Python 3.8+** — [python.org](https://www.python.org/downloads/)
- **Arduino IDE** (installed) — [arduino.cc](https://www.arduino.cc/en/software)
- **Arduino Leonardo / Micro / any ATmega32U4 board**
- **A real mouse** (to clone its identity)

## Download & Setup

You don't need Git. Just download the files directly:

1. **Download** — Grab `main.py` and `README.md` and save them to a folder of your choice.
2. **Verify** — Your folder should contain:
   ```
   main.py
   README.md
   ```
3. **Run** — Open a terminal in that folder and start the tool:
   ```bash
   python main.py
   ```

No additional dependencies — the script only uses Python's standard library.

## Usage

### 1. Start the script

```bash
python main.py
```

The interactive menu opens:

```
╔══════════════════════════════════════════════════════════════╗
║  Arduino Leonardo USB Spoof Tool  v2.0                      ║
║  patches boards.txt + USBCore.cpp for mouse identity spoofing║
╚══════════════════════════════════════════════════════════════╝
```

### 2. Create a profile

Choose one of these methods:

| Option | Description |
|--------|-------------|
| **Create profile from connected device** | Plug your mouse into your PC (not the Arduino) and pick it from the list. |
| **Create profile from USB Device Tree Viewer dump** | Copy the dump from [USB Device Tree Viewer](https://www.uwe-sieber.de/usbtreeview_e.html) (Windows). |
| **Create profile from lsusb -v dump** | Use `lsusb -v -d VID:PID` on Linux. |
| **Create profile manually** | Enter all values by hand. |

**Recommended:** "Create profile from connected device" — fastest and easiest.

#### Alternatively: Use a built-in profile

The script includes three safe profiles:

- **Generic 3-Button Optical Mouse** (neutral, safest choice)
- **Microsoft Basic Optical Mouse** (very common, often whitelisted)
- **Dell MS116 Wired Mouse** (common OEM office mouse)

### 3. Save the profile

After creation you'll be asked whether to save the profile. Confirm with `y`.

### 4. Apply the profile

Select **"Apply a profile"** from the main menu and pick your profile from the list.

The script automatically patches:
- `<Arduino15>/packages/arduino/hardware/avr/<version>/boards.txt`
- `<Arduino15>/packages/arduino/hardware/avr/<version>/cores/arduino/USBCore.cpp`

### 5. Select the board in Arduino IDE

1. Open Arduino IDE.
2. Go to **Tools → Board → "Arduino Leonardo MOD 1 (Your Mouse Name)"**.
3. Flash your macro/sketch as usual.

### 6. Verify

Your Arduino will now show up as the cloned mouse. Check it with **USB Device Tree Viewer** (Windows) or `lsusb -v` (Linux).

## CLI Commands (Power Users)

```bash
# List profiles
python main.py list-profiles

# Apply a profile (non-interactive)
python main.py apply --profile ms-basic-optical

# Dry-run (shows diff without changes)
python main.py dry-run --profile dell-ms116

# Check status
python main.py check

# Restore backup
python main.py restore

# Export profile (JSON)
python main.py export-profile --profile custom-mouse > mouse.json

# Import profile (JSON)
type mouse.json | python main.py import-profile
```

## Restore

If something goes wrong:

1. Start the script: `python main.py`
2. Select **"Restore original files"**.
3. Or CLI: `python main.py restore`

The original files are kept as `.spoof.bak` and can be reverted at any time.

## Project Structure

```
arduino-leonardo-usb-spoofer/
├── main.py          # Main script (Python)
└── README.md        # This file
```

## How It Works

The script patches two files in your Arduino installation:

1. **boards.txt** — Adds a new board "Arduino Leonardo MOD 1 (...)" with the target VID/PID and USB strings.
2. **USBCore.cpp** — Overwrites USB descriptor values (bcdUSB, bMaxPower, D_DEVICE call) at compile time.

When flashing, the compiler uses the patched core files so the resulting firmware USB descriptor exactly matches the target mouse.

## Safety Notes

- The tool **automatically creates backups** of all modified files (`.spoof.bak`).
- The **safety engine** validates all values for plausibility before writing.
- The Leonardo bootloader is never touched.
- You can always restore the original files via the "Restore" function.

## Compatibility

- **OS:** Windows 10/11, Linux, macOS
- **Boards:** Arduino Leonardo, Arduino Micro, SparkFun Pro Micro, any ATmega32U4 board
- **Arduino IDE:** 1.8.x, 2.x

## Disclaimer

This tool modifies Arduino core files (boards.txt and USBCore.cpp). It creates automatic backups (`.spoof.bak`) that can be restored at any time via the script's "Restore" option. Use at your own risk.

## License

MIT
