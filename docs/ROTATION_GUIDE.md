# Rotation Guide

## Physical Orientations

### pins-bottom (Portrait)
- Resolution: 240x320
- MADCTL: 0x88
- Overlay: `ili9341-zjy240-pins-bottom.dtbo`
- Usage: `dtoverlay=ili9341-zjy240-pins-bottom`

### pins-right (Landscape)
- Resolution: 320x240  
- MADCTL: 0xe8
- Overlay: `ili9341-zjy240-pins-right.dtbo`
- Usage: `dtoverlay=ili9341-zjy240-pins-right`
- **RASPDAC MINI default orientation**

### pins-top (Portrait)
- Resolution: 240x320
- MADCTL: 0x48
- Overlay: `ili9341-zjy240-pins-top.dtbo`
- Usage: `dtoverlay=ili9341-zjy240-pins-top`

### pins-left (Landscape)
- Resolution: 320x240
- MADCTL: 0x28
- Overlay: `ili9341-zjy240-pins-left.dtbo`
- Usage: `dtoverlay=ili9341-zjy240-pins-left`

## MADCTL Bit Meanings

| MADCTL | MY | MX | MV | BGR | Pins Position |
|--------|----|----|----|----|---------------|
| 0x88   | 1  | 0  | 0  | 1  | BOTTOM        |
| 0xe8   | 1  | 1  | 1  | 1  | RIGHT         |
| 0x48   | 0  | 1  | 0  | 1  | TOP           |
| 0x28   | 0  | 0  | 1  | 1  | LEFT          |

BGR bit must always be 1 for this display.

## Choosing Orientation

Choose based on physical connector position:
- Connector BOTTOM: `ili9341-zjy240-pins-bottom`
- Connector RIGHT: `ili9341-zjy240-pins-right` (RASPDAC MINI)
- Connector TOP: `ili9341-zjy240-pins-top`
- Connector LEFT: `ili9341-zjy240-pins-left`
