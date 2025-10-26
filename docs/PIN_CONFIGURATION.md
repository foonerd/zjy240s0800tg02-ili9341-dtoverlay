# Pin Configuration

## GPIO Assignments

| GPIO | Function | Description |
|------|----------|-------------|
| 27   | DC       | Data/Command select |
| 24   | RESET    | Hardware reset |
| 18   | LED      | Backlight |
| 10   | MOSI     | SPI data |
| 11   | SCLK     | SPI clock |
| 8    | CE0      | Chip select |

## Physical Connections

All pins use Audiophonics RASPDAC MINI LCD pinout.

Connect display ribbon cable to board header following manufacturer pin mapping.

## Wiring for Custom Installations

If creating custom wiring:
1. Connect all GPIO pins as listed
2. Connect 3.3V power
3. Connect GND
4. Ensure solid connections for SPI signals
5. Keep wires short for high-speed SPI
