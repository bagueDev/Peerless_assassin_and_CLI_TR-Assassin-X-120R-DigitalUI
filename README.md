# Digital Thermal Right LCD Controller

> **Fork note / Hinweis:** This fork adds support for the **Thermalright Assassin X 120 R Digital
> (TL-AX120 R Digital)** on Linux. / Dieser Fork ergänzt die Unterstützung für den
> **Thermalright Assassin X 120 R Digital** unter Linux.
> Based on [raffa0001/Peerless_assassin_and_CLI_UI](https://github.com/raffa0001/Peerless_assassin_and_CLI_UI)
> (fork of [MathieuxHugo/digital_thermal_right_lcd](https://github.com/MathieuxHugo/digital_thermal_right_lcd)).

This project allows you to control the Thermalright USB LCD screen on Linux.
It provides a Python controller to display system metrics (CPU/GPU temp/usage) and a shell script to configure the display.

## Features

- Display CPU/GPU temperature and usage.
- Multiple display modes.
- Configurable colors and gradients.
- Animated rainbow and wave patterns.
- Configuration via a shell script menu.
- GUI for live display preview and color configuration.

## Assassin X 120 R Digital (small layout)
<img width="320" height="320" alt="Bildschirmfoto vom 2026-10-09 12-27-54" src="https://github.com/user-attachments/assets/54341985-464c-4a7d-aaa7-f15cf927b899" />
<img width="320" height="320" alt="Bildschirmfoto vom 2026-10-09 12-26-27" src="https://github.com/user-attachments/assets/308c1fcf-330f-48b4-91fb-a21017e80529" />



- USB ID `0416:8001`, 31 LEDs (small layout). Set `"layout_mode": "small"` in `config.json`.
- Fix: `digit_mask` in `src/controller.py` was all ones, so every digit showed as `8`.
  The mask now follows the segment order `f, a, b, g, e, d, c` used by `leds_indexes_small["digit_frame"]`.
- Usable display modes in the small layout: `alternate_metrics`, `cpu_temp`, `gpu_temp`, `cpu_usage`, `gpu_usage`.
  The `peerless_*` modes use the big layout (84 LEDs) and do not fit this cooler.

## Prerequisites

- Python 3
- `jq` command-line JSON processor.
  - On Arch Linux: `sudo pacman -S jq`
  - On Debian/Ubuntu: `sudo apt-get install jq`
  - On Fedora: `sudo dnf install jq`
- `hidapi` library for your distribution.
  - On Arch Linux: `sudo pacman -S hidapi`
  - On Debian/Ubuntu: `sudo apt-get install libhidapi-dev libhidapi-hidraw0`
  - On Fedora: `sudo dnf install hidapi`
- `tkinter` for the GUI.
  - On Arch Linux: `sudo pacman -S tk`
  - On Debian/Ubuntu: `sudo apt-get install python3-tk`
  - On Fedora: `sudo dnf install python3-tkinter`
- Python dependencies can be installed via `pip`.

## Installation

1.  **Clone the repository:**
```bash
    git clone https://github.com/bagueDev/Peerless_assassin_and_CLI_TR-Assassin-X-120R-DigitalUI.git
    cd Peerless_assassin_and_CLI_TR-Assassin-X-120R-DigitalUI
```

2.  **Create a virtual environment and install dependencies:**
```bash
    python3 -m venv .venv
    source .venv/bin/activate
    pip install -r requirements.txt
```
    If `pip` cannot find `numpy==2.3.3` (older Python, e.g. 3.10), change the line in `requirements.txt` to plain `numpy`.

3.  **Run the installation script:**
    This script will set up the `udev` rule to allow running without `sudo` and create a `systemd` service to run the display controller on startup.
```bash
    sudo ./install.sh
```
    The service uses the absolute path of this folder and its `.venv`. If you move the project later,
    recreate the `.venv` and run `sudo ./install.sh` again.

## Usage

### Configuration

To configure the display, run the `led_control.sh` script:
```bash
./led_control.sh
```
This will open a menu where you can change display modes, colors, and other settings.

### GUI

A graphical interface is available for live preview and color customization.
To run the GUI:
```bash
.venv/bin/python src/led_display_ui.py
```
The GUI writes the settings to `config.json`. The service reads them after a restart:
```bash
sudo systemctl restart digital-thermal-right-lcd.service
```

## Troubleshooting

- **Display stays dark / service failed:** `journalctl -u digital-thermal-right-lcd.service -n 30 --no-pager`
- **`ValueError: invalid literal for int() with base 16: 'No'`:** a color entry in `config.json` contains `None`.
  Replace it with a hex value such as `0000ff-ff0000`.
- **GPU shows 0:** run `nvidia-smi`. A "Driver/library version mismatch" after an update needs a reboot.
- **`ImportError: Unable to load any of the following libraries`:** the system `hidapi` library is missing (see Prerequisites).

## Changes in this fork / Änderungen in diesem Fork

- **2026-10-09:** Fixed `digit_mask` in `src/controller.py` for the small layout
  (Thermalright Assassin X 120 R Digital, USB `0416:8001`). Digits were all shown as `8`.
- Verified on Linux (Ubuntu, Python 3.10, NVIDIA GPU): CPU/GPU temperature and usage display correctly.

## Uninstallation

To uninstall the service and udev rule, run the `uninstall.sh` script:
```bash
sudo ./uninstall.sh
```

## Credits

- Original controller: [MathieuxHugo/digital_thermal_right_lcd](https://github.com/MathieuxHugo/digital_thermal_right_lcd)
- Peerless Assassin adaption and CLI/GUI: [raffa0001/Peerless_assassin_and_CLI_UI](https://github.com/raffa0001/Peerless_assassin_and_CLI_UI)
- Assassin X 120 R Digital fix: [bagueDev](https://github.com/bagueDev)
