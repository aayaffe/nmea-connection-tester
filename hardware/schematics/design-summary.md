# NMEA 2000 CAN Interface - Design Summary

## Quick Overview

This is a one-page visual summary of the complete CAN interface design for NMEA 2000 compatibility.

## System Block Diagram

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                  NMEA 2000 CAN Bus Interface System                        ║
╚═══════════════════════════════════════════════════════════════════════════╝

        ┌─────────────────────────────────────────────────────────┐
        │  NMEA 2000 Bus (9-16V DC, 250 kbps CAN)                 │
        └────────────┬────────────────────────────────────────────┘
                     │
                     │ Micro-C Connector (M12, 5-pin)
                     │ or Screw Terminal (5.08mm, 5-pos)
                     │
                     ▼
        ┌────────────────────────────────────────────────────────┐
        │                Input Protection                         │
        │  [TVS Diode] [Polyfuse] [Reverse Polarity Protection]  │
        └───┬─────────────────────┬──────────────────────────────┘
            │                     │
            │ Power               │ CAN Signals
            ▼                     ▼
   ┌────────────────┐   ┌───────────────────────┐
   │  Buck Converter│   │   CAN Transceiver     │
   │   9-16V → 5V   │   │   MCP2551 / SN65HVD230│
   │   (MP1584)     │   │   TJA1050             │
   │   >90% Eff.    │   └───────┬───────────────┘
   └────┬───────────┘           │
        │                       │ TX/RX (Logic Level)
        │ 5V @ 500mA            │
        │                       │
        ▼                       │
   ┌────────────────┐           │
   │  LDO Regulator │           │
   │   5V → 3.3V    │           │
   │  (AMS1117-3.3) │           │
   └────┬───────────┘           │
        │                       │
        │ 3.3V @ 800mA          │
        │                       │
        └───────────────────┬───┘
                            │
                            ▼
                  ┌──────────────────┐
                  │   Microcontroller │
                  │     (ESP32)       │
                  │  - CAN Controller │
                  │  - Processing     │
                  │  - Display/UI     │
                  └──────────────────┘
```

## Key Components at a Glance

### Power Supply Chain
```
NMEA Bus     Buck Converter      LDO           Outputs
(9-16V)   →   LM2596/MP1584   →  AMS1117   →  3.3V @ 800mA
  │            TPS54331                         5V @ 500mA
  │              ↓
  │           5V @ 3A
  │
  └──────→ Protected by:
           - SMBJ18CA TVS (18V)
           - 2A Polyfuse
           - Reverse polarity protection
```

### CAN Interface Chain
```
NMEA Bus    Connector    Transceiver      MCU
CAN_H/L  →  Micro-C   →  MCP2551      →  GPIO 4 (TX)
  │          or           SN65HVD230       GPIO 5 (RX)
  │          Terminal     TJA1050
  │
  └──────→ [120Ω Termination]
           (Switchable via jumper)
```

## Component Options Summary

### Option A: Budget Build (~$18)
- **CAN Transceiver**: MCP2551 (5V, $1.50)
- **Buck Converter**: LM2596 Module ($1.50)
- **LDO**: AMS1117-3.3 ($0.20)
- **Connector**: Screw Terminal ($0.80)
- **Total**: ~$18 for complete circuit

### Option B: Professional Build (~$28)
- **CAN Transceiver**: SN65HVD230 (3.3V, $1.80)
- **Buck Converter**: TPS54331 ($2.00)
- **LDO**: AMS1117-3.3 ($0.20)
- **Connector**: Phoenix Micro-C ($10.00)
- **Total**: ~$28 for complete circuit

### Option C: Isolated Build (~$21)
- **CAN Transceiver**: SN65HVD230 ($1.80)
- **Isolation**: ADUM1201 ($3.50)
- **Buck Converter**: MP1584 Module ($1.20)
- **LDO**: AMS1117-3.3 ($0.20)
- **Connector**: Screw Terminal ($0.80)
- **Total**: ~$21 for isolated circuit

## Pin Connections (ESP32 Example)

```
┌──────────────────────────────────────────────────┐
│              ESP32 GPIO Connections               │
├──────────────┬───────────────────────────────────┤
│ ESP32 Pin    │ Connection                        │
├──────────────┼───────────────────────────────────┤
│ GPIO 4       │ → CAN TX (to transceiver TXD)    │
│ GPIO 5       │ ← CAN RX (from transceiver RXD)  │
│ 3.3V         │ → Power (3.3V transceiver)       │
│ 5V           │ → Power (5V transceiver)         │
│ GND          │ → Common Ground                   │
└──────────────┴───────────────────────────────────┘
```

## NMEA 2000 Connector Pinout

### Micro-C (M12) 5-Pin Connector
```
        Front View
         ╔═══╗
       ╔╝ 1 ╚╗  ← Pin 1: Shield (Bare)
      ╔╝     ╚╗
     ║ 4     5 ║ ← Pin 4: CAN_H (White)
      ╚╗  3  ╔╝   Pin 5: CAN_L (Blue)
       ╚╗ 2 ╔╝  ← Pin 2: V+ (Red, 9-16V)
         ╚═╝      Pin 3: GND (Black)
```

### Screw Terminal (5-Position)
```
┌─────┬─────┬─────┬─────┬─────┐
│  1  │  2  │  3  │  4  │  5  │
│Shield V+ │ GND │CANH │CANL │
└─────┴─────┴─────┴─────┴─────┘
```

## PCB Layout Key Points

### Layer Stack (2-Layer)
```
TOP:    Signal routing + Ground pour
BOTTOM: Solid ground plane
```

### Critical Measurements
- **CAN traces**: 10 mil width, 10 mil spacing
- **CAN impedance**: 120Ω differential
- **Power traces**: 20+ mil for 500mA
- **Via stitching**: Every 10-15mm

### Component Placement
```
┌───────────────────────────────┐
│ [Buck]    [CAN IC]  [Micro-C] │
│                               │
│ [LDO]     [ESP32]   [120Ω]   │
│                               │
│ [Caps]    [Display]  [LEDs]  │
└───────────────────────────────┘
```

## Test Points

```
TP1: VIN (9-16V)
TP2: 5V Output
TP3: 3.3V Output
TP4: CAN_H (Dominant: 3.5V, Recessive: 2.5V)
TP5: CAN_L (Dominant: 1.5V, Recessive: 2.5V)
TP6: GND
```

## Performance Specifications

| Parameter | Specification |
|-----------|--------------|
| Input Voltage | 9-16V DC |
| Input Current | < 50mA @ 12V (< 1 LEN) |
| 5V Output | 500mA max, ±5% |
| 3.3V Output | 800mA max, ±5% |
| CAN Data Rate | 250 kbps (NMEA 2000) |
| Termination | 120Ω ±1% (switchable) |
| Operating Temp | -40°C to +85°C |
| Efficiency | > 85% |

## Design Documents Reference

| Document | Location | Content |
|----------|----------|---------|
| Main Overview | `docs/hardware/can-interface.md` | Complete guide |
| CAN Schematic | `hardware/schematics/can-interface-schematic.md` | Transceiver circuits |
| Power Design | `hardware/schematics/power-extraction-circuit.md` | Buck + LDO circuits |
| PCB Layout | `hardware/pcb/layout-guidelines.md` | Layout rules |
| Connectors | `hardware/schematics/connector-specification.md` | Connector specs |
| BOM | `hardware/bom/can-interface-bom.csv` | Parts list |

## Acceptance Criteria Status

- ✅ CAN interface schematic
- ✅ Power extraction circuit (if bus-powered option)
- ✅ PCB layout guidelines
- ✅ Connector specification

## Next Development Steps

1. **PCB Layout**: Create in KiCAD/Eagle following guidelines
2. **Prototype**: Order 5-10 PCBs from JLCPCB/PCBWay
3. **Assembly**: Solder components per BOM
4. **Testing**: Power-up test → CAN communication → NMEA 2000 device test
5. **Integration**: Connect to ESP32 firmware

## Quick Start Checklist

- [ ] Review all design documents
- [ ] Order components from BOM
- [ ] Create PCB layout in CAD software
- [ ] Export Gerber files
- [ ] Order PCBs (2-week lead time)
- [ ] Assemble first prototype
- [ ] Perform power-up testing
- [ ] Test CAN communication at 250 kbps
- [ ] Connect to actual NMEA 2000 network
- [ ] Validate against NMEA 2000 devices

## Common Design Variants

### Variant 1: Minimal (Screw Terminal, No Isolation)
- Lowest cost: ~$18
- Easy assembly: Through-hole friendly
- DIY friendly: No special connectors
- Use case: Prototypes, lab testing

### Variant 2: Professional (Micro-C, High Efficiency)
- Best performance: >90% efficiency
- Industry standard: M12 connector
- Marine ready: IP67 when mated
- Use case: Production, field deployment

### Variant 3: Isolated (Galvanic Isolation)
- Safety: Protects MCU from bus faults
- Noise immunity: Better EMC
- Cost: +$3.50 for ADUM1201
- Use case: Critical applications

## Useful Formulas

### Power Budget
```
Power (W) = Voltage (V) × Current (A)
1 LEN = 50mA @ 12V = 0.6W
Our device: ~40mA @ 12V = 0.48W = 0.8 LEN ✓
```

### Buck Converter Efficiency
```
Efficiency = Pout / Pin × 100%
Pout = 5V × 0.3A = 1.5W
Pin = 12V × 0.15A = 1.8W
Efficiency = 1.5 / 1.8 = 83% ✓
```

### CAN Bus Termination
```
Total bus impedance = 120Ω
Termination resistors (2 ends): 120Ω each
Parallel combination: 120Ω ∥ 120Ω = 60Ω ✓
```

## Troubleshooting Quick Guide

| Problem | Check | Solution |
|---------|-------|----------|
| No power output | Input voltage | Verify 9-16V on NMEA bus |
| Low voltage | Load current | Reduce current draw |
| No CAN communication | Termination | Enable 120Ω termination |
| Intermittent CAN | Connections | Check cable continuity |
| Bus errors | Data rate | Verify 250 kbps setting |

## Resources

- **Standards**: NMEA 2000 (IEC 61162-3), ISO 11898
- **Libraries**: github.com/ttlappalainen/NMEA2000
- **Community**: cruisersforum.com (Marine Electronics)
- **Tools**: CAN bus analyzer, oscilloscope, multimeter

---

**Design Status**: Documentation Complete ✅  
**Hardware Status**: PCB Layout Pending ⏳  
**Cost**: $18-28 per unit  
**Complexity**: Moderate (intermediate soldering skills)  

For detailed information, see the main documentation at `docs/hardware/can-interface.md`
