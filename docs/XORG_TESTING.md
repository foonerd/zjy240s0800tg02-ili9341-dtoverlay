# X11/Xorg Testing Guide

This guide covers testing the display with X11/Xorg using different drivers and configurations.

## Prerequisites

- Display working at framebuffer level (verified with `fbset -fb /dev/fb1 -i`)
- Console mapped to display (see INSTALLATION.md)
- X11 server installed

**Note:** Volumio uses a kiosk-based architecture with Chromium browser, not a traditional desktop manager. The `volumio-kiosk.service` manages the X server and browser.

## Driver Options

### Option 1: fbdev (Generic Framebuffer Driver)

The `fbdev` driver is the standard generic framebuffer driver included with most X11 installations.

**Install (if needed):**
```bash
sudo apt-get install xserver-xorg-video-fbdev
```

**Configuration:**
```bash
sudo nano /etc/X11/xorg.conf.d/99-fbdev.conf
```

Add:
```
Section "Device"
    Identifier "ILI9341 LCD"
    Driver "fbdev"
    Option "fbdev" "/dev/fb1"
EndSection

Section "Screen"
    Identifier "LCD Screen"
    Device "ILI9341 LCD"
EndSection
```

**Pros:**
- Universal compatibility
- No additional packages needed
- Stable

**Cons:**
- No hardware acceleration
- Slower rendering
- Higher CPU usage

---

### Option 2: fbturbo (Optimized ARM Framebuffer Driver)

The `fbturbo` driver provides hardware-accelerated 2D operations optimized for ARM devices.

**Install:**
```bash
sudo apt-get install xserver-xorg-video-fbturbo
```

**Configuration:**
```bash
sudo nano /etc/X11/xorg.conf.d/99-fbturbo.conf
```

Add:
```
Section "Device"
    Identifier "ILI9341 LCD"
    Driver "fbturbo"
    Option "fbdev" "/dev/fb1"
    Option "SwapbuffersWait" "true"
EndSection

Section "Screen"
    Identifier "LCD Screen"
    Device "ILI9341 LCD"
EndSection
```

**Remove the fbdev config if switching:**
```bash
sudo rm /etc/X11/xorg.conf.d/99-fbdev.conf
```

**Pros:**
- Hardware-accelerated drawing operations
- Much faster rendering
- Lower CPU usage
- Better for GUI applications

**Cons:**
- ARM-specific
- Additional package required

**Recommended:** Use fbturbo for better performance with GUI applications.

---

## Verification

### Check X11 is using the display

**For Volumio (kiosk-based on Chrome):**

Restart the kiosk service:
```bash
sudo systemctl stop volumio-kiosk.service
sudo systemctl start volumio-kiosk.service
```

Or restart via Touch Display plugin:
1. Go to Volumio UI: **Plugins** > **Touch Display**
2. Click **Disable**
3. Wait for service to stop
4. Click **Enable**

**For other systems with display manager:**
```bash
sudo systemctl restart lightdm
# or
sudo systemctl restart gdm3
```

Check logs:
```bash
grep -i "fbdev\|fbturbo" /var/log/Xorg.0.log
```

**Expected output for fbdev:**
```
(II) LoadModule: "fbdev"
(II) FBDEV(0): using /dev/fb1
(II) FBDEV(0): hardware: fb_ili9341 (video memory: 150kB)
(II) FBDEV(0): Virtual size is 320x240 (pitch 320)
```

**Expected output for fbturbo:**
```
(II) LoadModule: "fbturbo"
(II) FBTURBO(0): using /dev/fb1
(II) FBTURBO(0): hardware: fb_ili9341 (video memory: 150kB)
```

### Test applications

**Simple test:**
```bash
DISPLAY=:0 xterm &
DISPLAY=:0 xclock &
```

**Test with image viewer:**
```bash
DISPLAY=:0 feh /path/to/image.png
```

---

## Volumio Touch Display Plugin (TESTING ONLY)

**Warning:** The Volumio Touch Display plugin is designed for larger touchscreens. This configuration is **for testing purposes only** and may not be suitable for production use.

### Installation

1. Access Volumio web UI: `http://volumio.local`
2. Navigate to: **Plugins** > **User Interface** > **Touch Display**
3. Install the plugin
4. Enable the plugin

### Configuration for 320x240 Display

After enabling the plugin:

1. Go to plugin settings
2. Configure the following:

**Display Settings:**
- **Screen Size:** Custom
- **Resolution:** 320x240
- **Rotate Display:** No (if using pins-right overlay)

**Scaling Settings:**
- **UI Scale:** 50-55%
- **Font Scale:** 50-55%

**Performance Settings:**
- **GPU Acceleration:** Enabled
- **Reduced Animations:** Enabled (recommended for small display)

### Why These Settings?

The Volumio UI is designed for tablets (7-10 inches, 800x480 or higher). At 320x240:
- **50-55% scaling** makes the interface elements readable
- Text and buttons will still be very small
- Touch targets may be difficult to hit accurately
- Some UI elements may be cut off

### Testing Checklist

- [ ] UI loads and displays
- [ ] Can navigate menus
- [ ] Playback controls visible
- [ ] Album art displays
- [ ] Text is readable
- [ ] Touch input responsive (if touchscreen)

### Limitations

**Screen Real Estate:**
- Very limited space for UI elements
- Album art will be tiny
- Long text will be truncated
- May need frequent scrolling

**Touch Input (if applicable):**
- Small touch targets
- May require stylus for accuracy
- Gestures may be difficult

**Performance:**
- UI rendering may be slow
- Consider disabling animations
- Album art scaling adds CPU load

### Recommended Use Cases

This small display works better for:
- **Status display** (now playing, time, etc.)
- **Simple playback controls** (play/pause, skip)
- **Album art viewer**
- **VU meters** or visualizers

For full Volumio UI experience, consider a larger display (4-7 inches minimum).

---

## Troubleshooting

### Display is blank

**Check X is running:**
```bash
ps aux | grep Xorg
```

**Check display is detected:**
```bash
DISPLAY=:0 xrandr
```

**Verify framebuffer:**
```bash
fbset -fb /dev/fb1 -i
```

### Wrong driver loaded

**Check which driver is active:**
```bash
grep "LoadModule.*fb" /var/log/Xorg.0.log
```

**Ensure only one config exists:**
```bash
ls -l /etc/X11/xorg.conf.d/*fb*.conf
```

Remove conflicting configs.

### Performance issues

**If using fbdev:**
- Switch to fbturbo for better performance
- Reduce UI complexity
- Disable animations

**If using fbturbo:**
- Check for error messages in Xorg log
- Verify GPU memory allocation in `/boot/config.txt`:
  ```
  gpu_mem=128
  ```

### UI elements too small

**For Volumio Touch Display:**
- Increase scaling to 60-70% (may cause clipping)
- Increase system DPI in Xorg config:
  ```
  Section "Screen"
      Identifier "LCD Screen"
      Device "ILI9341 LCD"
      DefaultDepth 16
      SubSection "Display"
          Depth 16
          Modes "320x240"
      EndSubSection
  EndSection
  ```

### Colors look wrong

Verify BGR mode is enabled in overlay (it should be by default).

If colors are still wrong, check:
```bash
fbset -fb /dev/fb1 -i
```

Should show:
```
rgba 5/11,6/5,5/0,0/0
```

---

## Advanced Configuration

### Set DPI

For better text rendering at small sizes:

```bash
sudo nano /etc/X11/xorg.conf.d/99-fbturbo.conf
```

Add DPI setting:
```
Section "Screen"
    Identifier "LCD Screen"
    Device "ILI9341 LCD"
    DefaultDepth 16
    Option "DPI" "96 x 96"
EndSection
```

Try values: 72, 96, 120 depending on readability.

### Disable Screen Blanking

Prevent display from turning off:

```bash
sudo nano /etc/X11/xorg.conf.d/99-fbturbo.conf
```

Add to Device section:
```
Option "BlankTime" "0"
Option "StandbyTime" "0"
Option "SuspendTime" "0"
Option "OffTime" "0"
```

### Hardware Cursor

Disable if cursor looks wrong:

Add to Device section:
```
Option "HWcursor" "false"
Option "SWcursor" "true"
```

---

## Performance Benchmarks

### fbdev vs fbturbo

Typical performance on Raspberry Pi 4:

**fbdev:**
- Simple window operations: ~15 FPS
- Image rendering: Slow
- CPU usage: 40-60% for UI updates

**fbturbo:**
- Simple window operations: ~25-30 FPS
- Image rendering: Fast
- CPU usage: 10-20% for UI updates

**Conclusion:** fbturbo is strongly recommended for any graphical applications.

---

## Alternative Approaches

### Framebuffer Console Only

If X11 performance is inadequate, consider:
- **fbv** for image display: `fbv -d 1 image.jpg`
- **mplayer** for video: `mplayer -vo fbdev2:/dev/fb1 video.mp4`
- **cava** for audio visualization
- Custom framebuffer applications

### SDL Applications

SDL2 can render directly to framebuffer:
```bash
SDL_VIDEODRIVER=fbcon SDL_FBDEV=/dev/fb1 ./app
```

---

## Summary

**For testing Volumio Touch Display:**
1. Install fbturbo driver
2. Configure for /dev/fb1
3. Install Touch Display plugin
4. Set scaling to 50-55%
5. Understand this is primarily for testing/evaluation

**Production recommendations:**
- Use fbturbo for any X11 applications
- Consider framebuffer-only applications for better performance
- This display size works best for status/info displays, not full UIs
- For full-featured touch interface, use 4-7 inch display minimum

**Next steps:**
- Test with your specific applications
- Measure performance and usability
- Decide if X11 overhead is worth it for your use case
- Consider dedicated framebuffer applications for optimal performance
