# Power Extraction Circuit - NMEA 2000 Bus Powered

## Overview
This document describes the power extraction circuit that allows the device to be powered directly from the NMEA 2000 bus (9-16V DC). The circuit provides regulated 5V and 3.3V outputs for the microcontroller and CAN transceiver.

## Power Budget
NMEA 2000 specifies a maximum of 1 LEN (Load Equivalence Number) per device:
- **1 LEN = 50mA @ 12V = 0.6W**
- Typical device consumption should be < 40mA to leave margin

### Estimated Power Consumption
| Component | Current @ 5V | Current @ 3.3V | Notes |
|-----------|--------------|----------------|-------|
| ESP32 (active) | - | 80-160mA | WiFi/BT active |
| ESP32 (idle) | - | 20-40mA | Light sleep |
| CAN Transceiver | 10-15mA | - | MCP2551 |
| Display (OLED) | - | 20-30mA | 128x64 OLED |
| LEDs | 5-10mA | - | Status indicators |
| **Total Max** | **25mA** | **200mA** | Active operation |
| **Total Idle** | **15mA** | **60mA** | Idle operation |

## Block Diagram
```
NMEA 2000 Bus
(9-16V DC)
    │
    ├──[Fuse]──[TVS]──[Input Filter]
    │
    ▼
┌───────────────────────────────────────────┐
│     Buck Converter (9-16V → 5V)           │
│     LM2596 / MP1584 / TPS54331            │
└───────────────────────────────────────────┘
    │
    ├─── 5V @ 500mA ──▶ CAN Transceiver
    │                   (if using 5V type)
    │
    ▼
┌───────────────────────────────────────────┐
│     LDO Regulator (5V → 3.3V)             │
│     AMS1117-3.3 / LM1117-3.3              │
└───────────────────────────────────────────────┘
    │
    └─── 3.3V @ 800mA ──▶ ESP32, Display, Logic
```

## Circuit Design

### Option 1: LM2596 Buck Converter (Simple, Lower Efficiency)
```
VBUS (9-16V)
    │
    ├──[2A Fuse]
    │
   ┌┴┐
   │ │ TVS Diode (18V)
   │ │ SMBJ18CA
   └┬┘
    │
    ├──[100µH Inductor]───┐
    │                      │
   ┌┴─────────────────┐   │
   │    LM2596-5.0     │   │
   │                   │   │
   │  VIN    VOUT      │   │
   │   1       2       │───┼──┬──▶ 5V Out
   │                   │   │  │
   │  GND    FB        │   │ ┌┴┐
   │   3       4       │   │ │ │ 100µF
   │                   │   │ │ │ Low ESR
   │  ON/OFF           │   │ └┬┘
   │   5               │   │  │
   └───────────────────┘   │  │
    │                     ┌┴┐ │
   GND                    │ │ │ 100µF
                          │ │ │ Low ESR
                          └┬┘ │
                           │  │
                          GND GND

Components:
- L1: 100µH, 2A, Shielded inductor
- C1: 100µF, 25V, Electrolytic (input)
- C2: 100µF, 16V, Low ESR (output)
- D1: Schottky diode, 3A, 40V (internal in LM2596)
- F1: Resettable fuse, 2A (Polyfuse)
```

### Option 2: MP1584 Buck Converter Module (Recommended, Small Size)
```
VBUS (9-16V)
    │
    ├──[2A Fuse]
    │
   ┌┴┐
   │ │ TVS Diode
   │ │ SMBJ18CA
   └┬┘
    │
   ┌┴┐
   │ │ 100µF, 25V
   └┬┘
    │
    ├─────────────────────┐
    │  MP1584 Module       │
    │  (Pre-built)         │
    │                      │
    │  VIN+    VOUT+       │──┬──▶ 5V @ 3A
    │                      │  │
    │  VIN-    VOUT-       │  │
    │                      │ ┌┴┐
    └──────────────────────┘ │ │ 100µF
         │                   └┬┘
        GND                   │
                             GND

Advantages:
- Pre-assembled, tested
- Small footprint (22x17mm)
- High efficiency (>92%)
- Adjustable output (trim pot)
- Cost: ~$1-2 USD
```

### Option 3: TPS54331 Buck Converter (High Efficiency, SMD)
```
VBUS (9-16V)
    │
    ├──[2A Fuse]──[10µF]
    │              Ceramic
   ┌┴┐
   │ │ SMBJ18CA
   └┬┘
    │
   ┌┴─────────────────────┐
   │     TPS54331          │
   │                       │
   │  VIN  BOOT  PH        │────[47µH]───┬──▶ 5V @ 3A
   │   1    2     3        │             │
   │                       │            ┌┴┐
   │  GND  VSENSE  EN      │            │ │ 47µF
   │   4    5      6       │───┐        │ │ Ceramic
   │                       │   │        └┬┘
   │  SS   COMP   RT/CLK   │  ┌┴┐        │
   │   7    8      9       │  │ │ 10kΩ  GND
   └───────────────────────┘  │ │
    │    │                    └┬┘
   GND  GND                    │
                              GND

Features:
- Efficiency: up to 95%
- Small solution size
- Integrated FETs
- Wide input range: 3.5-28V
- Fixed frequency: 570kHz
```

### 3.3V LDO Regulator
```
5V ───┬───▶ To CAN Transceiver (if 5V type)
      │
     ┌┴┐
     │ │ 10µF
     └┬┘
      │
  ┌───┴───────────┐
  │  AMS1117-3.3  │
  │               │
  │  VIN   VOUT   │───┬──▶ 3.3V @ 800mA
  │   1     3     │   │
  │               │  ┌┴┐
  │  GND   ADJ    │  │ │ 22µF
  │   2     4     │  │ │ Ceramic
  └───────────────┘  └┬┘
       │              │
      GND            GND

Alternative: XC6206P332MR (for lower current, <300mA)
- Ultra-low dropout: 0.25V @ 100mA
- Small SOT-23 package
- Low quiescent current: 1µA
```

## Input Protection

### TVS Diode and Fuse
```
NMEA 2000 V+ ───┬──[Fuse]──┬──▶ To Buck Converter
                │          │
               ┌┴┐        ┌┴┐
               │ │ TVS    │ │ 10µF, 25V
               │ │ 18V    │ │ Ceramic
               └┬┘        └┬┘
                │          │
NMEA 2000 GND ──┴──────────┴──▶ GND

Components:
- F1: Polyfuse, 2A hold, 4A trip (e.g., MF-R200)
- D1: TVS Diode, 18V breakdown (e.g., SMBJ18CA)
- C1: Ceramic capacitor, 10µF, 25V, X7R
```

### Reverse Polarity Protection
```
NMEA 2000 V+ ───┬──▶ [P-Channel MOSFET] ──▶ To Buck
                │         │
               ┌┴┐        │ Gate
               │ │ 100kΩ  │
               │ │        │
               └┬┘       ─┴─ Schottky
                │        GND

Alternative: Use diode bridge or series Schottky diode
- Simple: 1N5822 Schottky in series (0.5V drop)
- Better: FDN340P P-MOSFET (low Rds, minimal drop)
```

## Power Sequencing
For proper startup, ensure this sequence:
1. Bus voltage applied → Buck converter starts
2. 5V stabilizes → LDO starts
3. 3.3V stabilizes → MCU starts
4. MCU initializes → CAN transceiver enabled

### Power-Good Signal (Optional)
```
5V ───┬─────────────────────────▶ VDD
      │
     ┌┴┐ R1
     │ │ 100kΩ
     │ │
     └┬┘
      ├────────▶ PWR_GOOD to MCU
      │
     ┌┴┐ R2
     │ │ 47kΩ
     │ │
     └┬┘
      │
     ─┴─ Zener 3.3V
     GND

Logic:
- PWR_GOOD high when 5V > 4.5V
- MCU can monitor this pin
```

## Component Selection

### Buck Converter ICs
| Part Number | Vin Range | Iout Max | Efficiency | Package | Cost |
|-------------|-----------|----------|------------|---------|------|
| LM2596-5.0 | 4.5-40V | 3A | 75-85% | TO-263 | $1.50 |
| MP1584 | 4.5-28V | 3A | 92% | Module | $1.00 |
| TPS54331 | 3.5-28V | 3A | 95% | SOIC-8 | $2.00 |
| LM2575-5.0 | 4.5-40V | 1A | 75% | TO-220 | $1.00 |

### LDO Regulators
| Part Number | Vin Max | Iout Max | Dropout | Package | Cost |
|-------------|---------|----------|---------|---------|------|
| AMS1117-3.3 | 15V | 1A | 1.3V | SOT-223 | $0.20 |
| LM1117-3.3 | 15V | 800mA | 1.2V | SOT-223 | $0.30 |
| XC6206P332 | 6V | 250mA | 0.25V | SOT-23 | $0.15 |
| MCP1700-3302E | 6V | 250mA | 0.18V | SOT-23 | $0.40 |

## PCB Layout Guidelines
1. **Input capacitor**: Place close to buck converter VIN pin
2. **Output capacitor**: Place close to buck converter VOUT pin
3. **Inductor placement**: Keep away from sensitive circuits
4. **Ground plane**: Use solid ground plane, separate analog/digital
5. **Thermal relief**: Ensure adequate copper pour for heat dissipation
6. **Via stitching**: Use multiple vias for high-current paths

## Thermal Management
### Heat Dissipation Calculation
```
Power Loss = (Vin - Vout) × Iout × (1 - Efficiency)

Example (worst case):
Vin = 16V, Vout = 5V, Iout = 300mA, Eff = 85%
Ploss = (16 - 5) × 0.3 × 0.15 = 0.495W

For LDO (3.3V from 5V):
Ploss = (5 - 3.3) × 0.2 = 0.34W
```

### Cooling Solutions
- **Copper pour**: 2oz copper, minimum 500mm² area
- **Heat sink**: For LM2596, optional small heat sink
- **Air flow**: Natural convection usually sufficient
- **Thermal vias**: Use thermal vias under power components

## Testing and Validation
### Test Points
- TP_VIN: Input voltage from NMEA 2000 bus
- TP_5V: 5V output
- TP_3V3: 3.3V output
- TP_GND: Common ground

### Acceptance Criteria
1. ✓ Input voltage range: 9-16V DC
2. ✓ Output voltage: 5V ±5% under all load conditions
3. ✓ Output voltage: 3.3V ±5% under all load conditions
4. ✓ Ripple voltage: < 50mV p-p
5. ✓ Efficiency: > 80% at nominal load
6. ✓ Total current draw: < 50mA @ 12V (1 LEN)
7. ✓ Reverse polarity protection functional
8. ✓ Over-current protection functional

## Design Files
- Eagle/KiCAD schematic: `hardware/schematics/power-supply.sch`
- PCB layout: `hardware/pcb/power-supply.brd`
- BOM: `hardware/bom/power-supply-bom.csv`

## References
- NMEA 2000 Power Budget Specification
- TI Power Supply Design Seminar (SLUP261)
- Buck Converter Design Guidelines (AN-1197)
