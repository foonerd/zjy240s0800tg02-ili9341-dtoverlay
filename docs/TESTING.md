# Testing Documentation

## Test Equipment

- Raspberry Pi 4 Model B
- ZJY240S0800TG02 display (Audiophonics RASPDAC MINI LCD)
- Volumio OS (Debian Bookworm)
- Kernel 6.12.47-v7l+

## Test Images

Pre-generated test images in `assets/` folder:

**For Landscape (pins-right, pins-left):**
```bash
dd if=assets/landscape_test.raw of=/dev/fb1 bs=153600 count=1
```

**For Portrait (pins-bottom, pins-top):**
```bash
dd if=assets/portrait_test.raw of=/dev/fb1 bs=153600 count=1
```

## Expected Results

When correct:
- **TOP LEFT** label in red quadrant (top-left)
- **TOP RIGHT** label in green quadrant (top-right)
- **BOTTOM LEFT** label in blue quadrant (bottom-left)
- **BOTTOM RIGHT** label in yellow quadrant (bottom-right)
- Center text upright and readable

**Wrong position or upside down = incorrect overlay**

## Validation Matrix

### pins-bottom (Portrait, BOTTOM)
```bash
dtoverlay=ili9341-zjy240-pins-bottom
fbset -fb /dev/fb1 -i  # Should show: 240x320
dd if=assets/portrait_test.raw of=/dev/fb1 bs=153600 count=1
```
**Expected:** Labels in correct corners

### pins-right (Landscape, RIGHT)
```bash
dtoverlay=ili9341-zjy240-pins-right
fbset -fb /dev/fb1 -i  # Should show: 320x240
dd if=assets/landscape_test.raw of=/dev/fb1 bs=153600 count=1
```
**Expected:** Labels in correct corners
**Note:** RASPDAC MINI default

### pins-top (Portrait, TOP)
```bash
dtoverlay=ili9341-zjy240-pins-top
fbset -fb /dev/fb1 -i  # Should show: 240x320
dd if=assets/portrait_test.raw of=/dev/fb1 bs=153600 count=1
```
**Expected:** Labels in correct corners

### pins-left (Landscape, LEFT)
```bash
dtoverlay=ili9341-zjy240-pins-left
fbset -fb /dev/fb1 -i  # Should show: 320x240
dd if=assets/landscape_test.raw of=/dev/fb1 bs=153600 count=1
```
**Expected:** Labels in correct corners

## Test Results

All orientations: **PASS**
- Correct geometry
- No grey stripes
- Test images display correctly
- Colors correct (BGR mode)
- No SPI errors
- Stable under load

## Validation Checklist

- [ ] Framebuffer geometry matches orientation
- [ ] No grey stripes
- [ ] Test image labels in correct positions
- [ ] Colors display correctly
- [ ] No SPI errors in dmesg
- [ ] Display stable
