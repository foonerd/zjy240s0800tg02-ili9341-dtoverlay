# Hardware Reference

## Display Specifications

### ZJY240S0800TG02

| Parameter | Value |
|-----------|-------|
| Model Number | ZJY240S0800TG02 |
| Controller IC | ILI9341 |
| Display Type | TFT LCD |
| Diagonal Size | 2.4 inches |
| Resolution | 320x240 pixels (QVGA) |
| Color Depth | 16-bit (65,536 colors) |
| Color Format | RGB565 (BGR order) |
| Viewing Angle | Full viewing angle |
| Interface | 4-wire SPI |
| Operating Voltage | 3.3V |
| Backlight | White LED |

## ILI9341 Controller

The ILI9341 is a single-chip TFT LCD controller-driver with 240 RGB x 320 dot matrix. Key features:

- 262K color display support
- Internal display RAM (172,800 bytes)
- Direct display RAM access
- SPI interface support
- Hardware rotation support via MADCTL register
- RGB and BGR order support

## GPIO Pin Assignments

### Audiophonics RASPDAC MINI LCD Pinout

| GPIO Pin | Function | Direction | Description |
|----------|----------|-----------|-------------|
| GPIO 27  | DC       | Output    | Data/Command select (0=Command, 1=Data) |
| GPIO 24  | RESET    | Output    | Hardware reset (Active LOW) |
| GPIO 18  | LED      | Output    | Backlight control (PWM capable) |
| GPIO 10  | MOSI     | Output    | SPI Master Out Slave In |
| GPIO 11  | SCLK     | Output    | SPI Clock |
| GPIO 8   | CE0      | Output    | Chip Enable 0 (Active LOW) |
| 3.3V     | VCC      | Power     | Display power supply |
| GND      | GND      | Ground    | Ground connection |

### Pin Functions Explained

**DC (Data/Command):**
- Controls whether data sent is a command or pixel data
- LOW (0) = Command byte
- HIGH (1) = Data byte
- Critical for proper display operation

**RESET:**
- Hardware reset signal
- Active LOW (pull LOW to reset)
- Must be held LOW for minimum 10ms to ensure proper reset
- Controlled by device tree overlay

**LED (Backlight):**
- Controls display backlight
- Can use PWM for brightness control
- GPIO 18 supports hardware PWM
- Current draw depends on backlight design

**MOSI (Master Out Slave In):**
- SPI data line from Pi to display
- Transmits both commands and pixel data
- Unidirectional (Pi → Display only)

**SCLK (SPI Clock):**
- Clock signal for SPI communication
- Maximum 64 MHz supported
- Data sampled on clock edges

**CE0 (Chip Enable):**
- Selects the display for SPI communication
- Active LOW
- Multiple SPI devices can share bus using different CE pins

## SPI Configuration

### SPI Bus Parameters

| Parameter | Value | Notes |
|-----------|-------|-------|
| Bus | SPI0 | Primary SPI bus |
| Device | spi0.0 | First device on SPI0 |
| Mode | 0 | CPOL=0, CPHA=0 |
| Bits per word | 8 | Standard byte transfer |
| Max Speed | 64 MHz | Tested stable |
| Min Speed | 16 MHz | For troubleshooting |

### SPI Mode Explanation

**Mode 0 (CPOL=0, CPHA=0):**
- Clock idle state: LOW
- Data sampled on: Rising edge
- Data shifted on: Falling edge
- Most common SPI mode

## Display Memory Organization

### Framebuffer

**Landscape (320x240):**
- Width: 320 pixels
- Height: 240 pixels
- Total pixels: 76,800
- Bytes per pixel: 2 (RGB565)
- Total size: 153,600 bytes
- Line length: 640 bytes (320 pixels × 2 bytes)

**Portrait (240x320):**
- Width: 240 pixels
- Height: 320 pixels  
- Total pixels: 76,800
- Bytes per pixel: 2 (RGB565)
- Total size: 153,600 bytes
- Line length: 480 bytes (240 pixels × 2 bytes)

### RGB565 Color Format

16-bit color encoding:
```
Bit:  15 14 13 12 11 | 10 9 8 7 6 5 | 4 3 2 1 0
      R  R  R  R  R  | G  G G G G G | B B B B B
      Red (5 bits)   | Green (6 bits) | Blue (5 bits)
```

**Color Examples:**
- Black: 0x0000
- White: 0xFFFF
- Red: 0xF800
- Green: 0x07E0
- Blue: 0x001F

**BGR Order:**
This display uses BGR order instead of RGB:
```
Bit:  15 14 13 12 11 | 10 9 8 7 6 5 | 4 3 2 1 0
      B  B  B  B  B  | G  G G G G G | R R R R R
      Blue (5 bits)  | Green (6 bits) | Red (5 bits)
```

## ILI9341 Registers

### MADCTL Register (0x36)

Memory Access Control register controls display orientation:

| Bit | Name | Function |
|-----|------|----------|
| 7   | MY   | Row Address Order (0=Top-to-Bottom, 1=Bottom-to-Top) |
| 6   | MX   | Column Address Order (0=Left-to-Right, 1=Right-to-Left) |
| 5   | MV   | Row/Column Exchange (0=Normal, 1=Swapped) |
| 4   | ML   | Vertical Refresh Order |
| 3   | BGR  | RGB-BGR Order (0=RGB, 1=BGR) |
| 2   | MH   | Horizontal Refresh Order |
| 1-0 | -    | Unused |

**Validated MADCTL Values:**

| Value | MY | MX | MV | BGR | Result | Pins Position |
|-------|----|----|----|----|--------|---------------|
| 0xe8  | 1  | 1  | 1  | 1  | Landscape | RIGHT |
| 0x88  | 1  | 0  | 0  | 1  | Portrait | BOTTOM |
| 0x48  | 0  | 1  | 0  | 1  | Portrait | TOP |
| 0x28  | 0  | 0  | 1  | 1  | Landscape | LEFT |

## Electrical Characteristics

### Power Requirements

| Parameter | Min | Typ | Max | Unit |
|-----------|-----|-----|-----|------|
| Supply Voltage (VCC) | 3.0 | 3.3 | 3.6 | V |
| Logic High (VIH) | 2.0 | - | - | V |
| Logic Low (VIL) | - | - | 0.8 | V |
| Display Current (typ) | - | 50 | - | mA |
| Backlight Current | - | 20 | 100 | mA |

**Note:** Actual current draw depends on:
- Display content (white draws more than black)
- Backlight brightness setting
- Refresh rate

### Signal Timing

| Parameter | Min | Typ | Max | Unit | Notes |
|-----------|-----|-----|-----|------|-------|
| SPI Clock Frequency | 0 | 64 | 64 | MHz | Tested stable |
| Reset Pulse Width | 10 | - | - | ms | Minimum LOW time |
| Command/Data Setup | 10 | - | - | ns | DC setup before CLK |

## Physical Dimensions

| Parameter | Value | Unit |
|-----------|-------|------|
| Display Diagonal | 2.4 | inches |
| Active Area Width | 48.96 | mm |
| Active Area Height | 36.72 | mm |
| Pixel Pitch | 0.153 | mm |

## Temperature Range

| Parameter | Min | Max | Unit |
|-----------|-----|-----|------|
| Operating Temperature | -20 | 70 | °C |
| Storage Temperature | -30 | 80 | °C |

## Connector Type

The ZJY240S0800TG02 typically uses a flexible flat cable (FFC) or ribbon cable connector. Pin arrangement varies by manufacturer integration. The Audiophonics RASPDAC MINI LCD uses a specific pinout detailed in the GPIO assignments section.

## Compatibility

### Tested Platforms

- Raspberry Pi 4 Model B
- Raspberry Pi 3 B/B+
- Raspberry Pi Zero/Zero W (requires pin mapping adjustment)

### Operating Systems

- Raspberry Pi OS (Debian-based)
- Volumio
- Ubuntu for Raspberry Pi
- Any Linux distribution with fbtft support

### Kernel Requirements

- Linux kernel 4.4 or later
- fbtft (framebuffer TFT) driver support
- SPI subsystem enabled
- Device tree overlay support

## Related Documentation

- ILI9341 Datasheet (available from display manufacturers)
- Raspberry Pi GPIO Documentation
- Linux fbtft driver documentation
- Device Tree Overlay documentation
