# PCB Layout Guidelines - NMEA 2000 CAN Interface

## Overview
This document provides comprehensive PCB layout guidelines for the NMEA 2000 CAN interface circuit. Proper layout is critical for EMC compliance and reliable operation.

## PCB Stack-up

### 2-Layer Board (Recommended for prototype)
```
┌─────────────────────────────────┐
│  TOP Layer (Signal + Power)     │  1 oz copper
├─────────────────────────────────┤
│  Core (FR4)                      │  1.6mm
├─────────────────────────────────┤
│  BOTTOM Layer (Ground)           │  1 oz copper
└─────────────────────────────────┘

Specifications:
- Material: FR4, Tg 130-140°C
- Thickness: 1.6mm standard
- Copper weight: 1 oz (35µm)
- Minimum trace width: 6 mil (0.15mm)
- Minimum spacing: 6 mil (0.15mm)
```

### 4-Layer Board (Recommended for production)
```
┌─────────────────────────────────┐
│  TOP Layer (Signal)              │  1 oz copper
├─────────────────────────────────┤
│  Inner Layer 1 (Ground)          │  0.5 oz copper
├─────────────────────────────────┤
│  Inner Layer 2 (Power)           │  0.5 oz copper
├─────────────────────────────────┤
│  BOTTOM Layer (Signal)           │  1 oz copper
└─────────────────────────────────┘

Advantages:
- Better EMC performance
- Improved power distribution
- Better impedance control
- Reduced crosstalk
```

## Component Placement

### Overall Layout Strategy
```
┌────────────────────────────────────────────────────┐
│                                                     │
│  ┌──────────┐         ┌──────────┐                │
│  │  Power   │         │   CAN    │                │
│  │  Supply  │────────▶│Transceiver│───┐           │
│  │(Buck+LDO)│         │          │    │           │
│  └──────────┘         └──────────┘    │           │
│                                        │           │
│                                        │           │
│  ┌──────────┐                          │           │
│  │   MCU    │                          │           │
│  │ (ESP32)  │◀─────────────────────────┘           │
│  │          │                                      │
│  └──────────┘                         ┌──────────┐ │
│                                       │ Connector│ │
│  ┌──────────┐                         │ Micro-C  │◀┤
│  │ Display  │                         │  or      │ │
│  │  OLED    │                         │ Terminal │ │
│  └──────────┘                         └──────────┘ │
│                                                     │
└────────────────────────────────────────────────────┘

Key Points:
1. Power supply in one corner (minimize noise coupling)
2. CAN transceiver close to connector
3. Keep CAN signals short
4. MCU in center, equal distance to peripherals
5. Display/UI on opposite side from CAN
```

### Critical Component Placement Rules

#### 1. CAN Transceiver to Connector
- **Maximum trace length**: < 50mm (2 inches)
- **Placement**: Transceiver within 25mm of connector
- **Routing**: Direct, minimal vias
- **Ground**: Solid ground pour underneath

#### 2. MCU to CAN Transceiver
- **Maximum trace length**: < 150mm (6 inches)
- **Placement**: Keep TX/RX traces parallel
- **Routing**: Avoid crossing power traces
- **Clearance**: 20 mil from high-speed signals

#### 3. Power Components
- **Buck converter**: Keep switching node small
- **Input caps**: Within 5mm of VIN pin
- **Output caps**: Within 5mm of VOUT pin
- **Inductor**: Away from sensitive circuits (>10mm)

## Trace Width and Spacing

### Power Traces
| Net | Current | Trace Width (1oz) | Trace Width (2oz) |
|-----|---------|-------------------|-------------------|
| VBUS (9-16V) | 1A | 30 mil (0.76mm) | 20 mil (0.5mm) |
| 5V | 500mA | 20 mil (0.5mm) | 15 mil (0.38mm) |
| 3.3V | 300mA | 15 mil (0.38mm) | 10 mil (0.25mm) |
| GND | Return | Pour | Pour |

### Signal Traces
| Signal Type | Trace Width | Spacing | Notes |
|-------------|-------------|---------|-------|
| CAN_H / CAN_L | 10 mil | 10 mil | Differential pair |
| CAN TX/RX | 8 mil | 8 mil | Standard digital |
| I2C (SCL/SDA) | 8 mil | 8 mil | Keep short |
| SPI | 8 mil | 8 mil | Keep parallel |

### High-Speed Considerations
For CAN differential pair:
```
Impedance Target: 120Ω differential

Trace parameters (FR4, 1.6mm):
- Trace width: 10 mil (0.25mm)
- Trace spacing: 10 mil (0.25mm)
- Trace-to-ground: 20 mil (0.5mm)
- Length matching: ±5 mil (±0.13mm)

Calculated impedance:
Zo_diff ≈ 120Ω ± 10%
```

## Ground Plane Strategy

### 2-Layer Board
```
TOP Layer:
┌─────────────────────────────────────┐
│ ███ Ground Pour ██████████████████  │
│ ██                          ███████ │
│ ██  Keep-out for           ████████ │
│ ██  signal routing         ████████ │
│ ██                          ███████ │
│ ███████████████████████████████████ │
└─────────────────────────────────────┘

BOTTOM Layer:
┌─────────────────────────────────────┐
│ ███████████████████████████████████ │
│ ███████████████████████████████████ │
│ ███████  Solid Ground  ████████████ │
│ ███████████████████████████████████ │
│ ███████████████████████████████████ │
└─────────────────────────────────────┘
```

### Ground Plane Rules
1. **Solid bottom plane**: No breaks except necessary vias
2. **Top layer pour**: Fill unused areas with ground
3. **Via stitching**: Every 10-15mm around perimeter
4. **Thermal reliefs**: Use for hand soldering pads
5. **Ground loops**: Avoid by using star ground topology

### Ground Zones
```
┌────────────────────────────────────────┐
│                                         │
│  ┌─────────────┐    ┌──────────────┐  │
│  │   POWER     │    │     CAN      │  │
│  │   GROUND    │    │   GROUND     │  │
│  │   (Noisy)   │    │   (Clean)    │  │
│  └──────┬──────┘    └──────┬───────┘  │
│         │                   │          │
│         └─────────┬─────────┘          │
│                   │                    │
│              Single Point              │
│              Connection                │
│                   │                    │
│  ┌───────────────┴────────────────┐   │
│  │      MAIN GROUND PLANE          │   │
│  └─────────────────────────────────┘   │
└────────────────────────────────────────┘

Strategy:
- Separate analog and digital grounds
- Connect at single point (star topology)
- Use ferrite bead if needed
```

## CAN Bus Differential Pair Routing

### Routing Rules
```
CANH ═══════════════════════════════════▶
        ▲                                 
        │ S = 10 mil (0.25mm)
        ▼
CANL ═══════════════════════════════════▶

Rules:
1. Keep traces parallel
2. Match lengths within ±5 mil
3. Avoid vias if possible
4. Keep away from power traces
5. Route over ground plane
6. No 90° angles (use 45° or arcs)
```

### Bad vs Good Routing
```
BAD:
CANH ─────┐     ┌──────▶
          └─VIA─┘
CANL ─────┐     ┌──────▶
          └─VIA─┘
(Different via lengths)

GOOD:
CANH ══════════════════▶
CANL ══════════════════▶
(No vias, matched length)

ACCEPTABLE:
CANH ─────┐VIA ┌──────▶
CANL ─────┘VIA └──────▶
(Same via structure)
```

## Power Distribution Network (PDN)

### Decoupling Capacitor Placement
```
        VDD Pin
           │
          ┌┴┐
          │ │ 100nF (closest, <5mm)
          │ │ 0402/0603
          └┬┘
           │
          ┌┴┐
          │ │ 10µF (close, <10mm)
          │ │ 0805
          └┬┘
           │
          ┌┴┐
          │ │ 100µF (nearby, <20mm)
          │ │ Electrolytic
          └┬┘
           │
          GND

Rules:
1. Smallest capacitor closest to IC
2. Short, wide traces to pads
3. Via to ground plane immediately
4. Multiple parallel vias for low impedance
```

### Power Routing
```
Power IN ──[Large traces]──┬──▶ Buck Converter
                           │
                          ┌┴┐
                          │ │ Bulk Cap
                          └┬┘
                           │
5V OUT ────[Medium traces]─┬──▶ Devices
                           │
                          ┌┴┐
                          │ │ Filter Cap
                          └┬┘
                           │
3.3V OUT ──[Fine traces]───┬──▶ Logic
                           │
                          ┌┴┐
                          │ │ Bypass Cap
                          └┬┘
                          GND
```

## EMC Design Features

### Input Protection
```
NMEA Bus ───[Common Mode Choke]───[TVS]───▶ Circuit
                                   │
                                  GND

Components:
- Common mode choke: 10µH, 2A
- TVS diode: SMBJ18CA
- Series resistors: 22Ω on CANH/CANL
```

### Shielding
```
┌─────────────────────────────────────┐
│  ┌──────────────────────────────┐   │
│  │   Ground pour with vias      │   │
│  │   stitched to bottom plane   │   │
│  │   (EMI shielding)             │   │
│  └──────────────────────────────┘   │
└─────────────────────────────────────┘

Via stitching pattern:
● ● ● ● ● ● ● ●
● ┌─────────┐ ●
● │ CAN IC  │ ●
● └─────────┘ ●
● ● ● ● ● ● ● ●

Spacing: 10-15mm (< λ/20 at highest freq)
```

## Connector Interface

### Micro-C Connector Footprint
```
        Pin 1 (Shield)
          │
    ┌─────┴─────┐
    │ 1   2   3 │
    │ 4       5 │
    └───────────┘
     │       │
   Pin 4   Pin 5
  (CANH)   (CANL)

Footprint requirements:
- Use official connector library
- Add mounting holes (M2.5 or M3)
- Ground shield to PCB immediately
- Keep out zone: 5mm around connector
```

### Screw Terminal Alternative
```
    ┌───┬───┬───┬───┬───┐
    │ 1 │ 2 │ 3 │ 4 │ 5 │
    └───┴───┴───┴───┴───┘
      │   │   │   │   │
    Shield V+ GND  H   L

Terminal block:
- Pitch: 5.08mm (0.2")
- Wire size: 18-24 AWG
- Screw: M3 or #4-40
- Current rating: 10A minimum
```

## Test Points and Debug Features

### Test Point Placement
```
         TOP VIEW
┌─────────────────────────┐
│TP1  TP2         TP3 TP4 │
│ VIN  5V         3V3 GND │
│                         │
│  ┌──────┐    ┌───────┐ │
│  │ Buck │    │  CAN  │ │
│  └──────┘    └───────┘ │
│                         │
│ TP5      TP6       TP7  │
│CANH     CANL      TXD   │
└─────────────────────────┘

Test points:
- Use 1mm diameter pads
- Label clearly on silkscreen
- Accessible with probe clips
- Standard 100 mil (2.54mm) spacing
```

## Manufacturing Specifications

### PCB Fabrication Notes
```
Layer Count: 2 or 4
Material: FR4, Tg 130°C min
Thickness: 1.6mm ±10%
Copper: 1 oz (35µm) outer, 0.5 oz inner
Surface Finish: ENIG (gold) or HASL
Solder Mask: Green (or Blue/Black)
Silkscreen: White, both sides
Min Trace: 6 mil (0.15mm)
Min Space: 6 mil (0.15mm)
Min Drill: 10 mil (0.25mm)
```

### Assembly Notes
```
Reflow Profile: SAC305 (lead-free)
Peak Temperature: 245°C
Time above 217°C: 60-90 seconds
Solder Paste: Type 3 or Type 4
Stencil Thickness: 4 mil (0.1mm)
```

## Design Rule Check (DRC) Settings

### Critical Rules
- Minimum trace width: 6 mil
- Minimum trace spacing: 6 mil
- Minimum via diameter: 20 mil
- Minimum via drill: 10 mil
- Minimum annular ring: 5 mil
- Copper to board edge: 20 mil

### Clearance Rules
- High voltage (12V) to low voltage: 20 mil
- Power to signal: 10 mil
- Signal to signal: 8 mil
- Ground pour to trace: 8 mil

## Design Verification Checklist

### Pre-fabrication Checks
- [ ] DRC passed with no errors
- [ ] All component footprints verified
- [ ] All net names assigned correctly
- [ ] All decoupling caps placed correctly
- [ ] Ground plane continuous (no gaps)
- [ ] CAN differential pair matched
- [ ] Power trace widths adequate
- [ ] Test points accessible
- [ ] Silkscreen readable (>40 mil height)
- [ ] Mounting holes present
- [ ] Version number on PCB
- [ ] Polarity marks on connectors
- [ ] Pin 1 indicators present

### Post-fabrication Checks
- [ ] Visual inspection (no shorts/opens)
- [ ] Continuity test (all nets)
- [ ] Resistance test (power rails)
- [ ] Capacitance test (ground to power)
- [ ] High-pot test (isolation)

## Revision History
- v1.0: Initial layout guidelines
- v1.1: Added 4-layer stack-up
- v1.2: Updated EMC requirements

## References
- IPC-2221: Generic PCB Design Standard
- IPC-2222: Rigid Organic PCB Design Standard
- IPC-7351: Generic Component Footprints
- NMEA 2000 Installation Guidelines
- ISO 11898: CAN Physical Layer
