# Installation Guide

## Prerequisites

- Raspberry Pi (3/4/Zero with GPIO header)
- ZJY240S0800TG02 display with ILI9341 controller
- Proper GPIO connections
- Root/sudo privileges

## Installation

### Step 1: Choose Overlay

Select based on connector position:
- **Pins BOTTOM**: `ili9341-zjy240-pins-bottom.dtbo`
- **Pins RIGHT**: `ili9341-zjy240-pins-right.dtbo` (RASPDAC MINI)
- **Pins TOP**: `ili9341-zjy240-pins-top.dtbo`
- **Pins LEFT**: `ili9341-zjy240-pins-left.dtbo`

### Step 2: Copy Overlay

```bash
sudo cp overlay/ili9341-zjy240-pins-right.dtbo /boot/overlays/
```

### Step 3: Edit Configuration

```bash
# For Volumio
sudo nano /boot/userconfig.txt

# For Raspberry Pi OS
sudo nano /boot/firmware/config.txt
```

Add:
```
dtoverlay=ili9341-zjy240-pins-right
```

### Step 4: Reboot

```bash
sudo reboot
```

## Verification

```bash
# Check framebuffer
fbset -fb /dev/fb1 -i

# Test display
dd if=/dev/zero of=/dev/fb1 bs=153600 count=1
```

## For RASPDAC MINI LCD

Use either:
```
dtoverlay=raspdac-mini-lcd
```
OR
```
dtoverlay=ili9341-zjy240-pins-right
```

Both provide landscape 320x240 with pins RIGHT.

## Building from Source

```bash
cd sources/
dtc -@ -I dts -O dtb -o ../overlay/ili9341-zjy240-pins-right.dtbo ili9341-zjy240-pins-right.dts
sudo cp ../overlay/ili9341-zjy240-pins-right.dtbo /boot/overlays/
```

## Uninstallation

Edit boot configuration:
```bash
# For Volumio
sudo nano /boot/userconfig.txt

# For Raspberry Pi OS
sudo nano /boot/firmware/config.txt
```

Comment out overlay line:
```
# dtoverlay=ili9341-zjy240-pins-right
```

Reboot.
