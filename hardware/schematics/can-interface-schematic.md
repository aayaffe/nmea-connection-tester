# CAN Interface Schematic - NMEA 2000 Compatible

## Overview
This schematic describes the CAN bus transceiver circuit designed for NMEA 2000 compatibility. The design supports 250 kbps data rate, switchable 120Ω termination, and optional galvanic isolation.

## Block Diagram
```
┌─────────────────────────────────────────────────────────────────┐
│                    CAN Interface Circuit                         │
│                                                                   │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐  │
│  │   MCU    │───▶│   CAN    │───▶│   ISO    │───▶│ Micro-C  │  │
│  │  (ESP32) │    │Controller│    │(Optional)│    │Connector │  │
│  │          │◀───│(Built-in)│◀───│          │◀───│          │  │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘  │
│                         │                                         │
│                         ▼                                         │
│                  ┌──────────┐                                    │
│                  │Transceiver│                                   │
│                  │MCP2551/   │                                   │
│                  │TJA1050/   │                                   │
│                  │SN65HVD230 │                                   │
│                  └──────────┘                                    │
│                         │                                         │
│                         ▼                                         │
│                  ┌──────────┐                                    │
│                  │120Ω Term │                                    │
│                  │(Switchable)│                                  │
│                  └──────────┘                                    │
└─────────────────────────────────────────────────────────────────┘
```

## Component Selection

### CAN Transceiver Options

#### Option 1: MCP2551 (Recommended for general use)
- **Advantages**: Robust, well-documented, widely available
- **Supply Voltage**: 4.5V to 5.5V
- **Data Rate**: Up to 1 Mbps (250 kbps for NMEA 2000)
- **Package**: 8-pin DIP/SOIC
- **Cost**: ~$1-2 USD

#### Option 2: TJA1050
- **Advantages**: High EMC performance, automotive-grade
- **Supply Voltage**: 4.75V to 5.25V
- **Data Rate**: Up to 1 Mbps
- **Package**: 8-pin SOIC
- **Cost**: ~$1.50-2.50 USD

#### Option 3: SN65HVD230 (Recommended for 3.3V systems)
- **Advantages**: 3.3V compatible, low power
- **Supply Voltage**: 3.0V to 3.6V
- **Data Rate**: Up to 1 Mbps
- **Package**: 8-pin SOIC
- **Cost**: ~$1-2 USD

## Detailed Schematic

### MCP2551 Configuration
```
                    VDD (5V)
                      │
                      ├───────┐
                      │       │
                     ┌┴┐      │
                     │ │ 10kΩ │
                     │ │      │
                     └┬┘     ┌┴┐
                      │      │ │ 100nF
                      │      │ │
        ┌─────────────┴──────┴┬┘
        │                      │
        │    MCP2551           │
    ┌───┴───┐              ┌───┴───┐
    │  VDD  │              │  VSS  │
    │   3   │              │   2   │
    └───────┘              └───────┘
        │                      │
    ┌───┴───┐              ┌───┴───┐
    │  TXD  │◀────────────▶│  GPIO │ MCU TX
    │   1   │              │       │
    └───────┘              └───────┘
        │
    ┌───┴───┐              ┌───────┐
    │  RXD  │◀────────────▶│  GPIO │ MCU RX
    │   4   │              │       │
    └───────┘              └───────┘
        │
    ┌───┴───┐
    │  Rs   │ Mode Select
    │   8   │──────────┐
    └───────┘          │
                      GND
        │
    ┌───┴───┐
    │ CANH  │─────────┬─────────▶ NMEA 2000 Bus
    │   7   │         │
    └───────┘         │
        │            ┌┴┐
    ┌───┴───┐        │ │ 120Ω (switchable)
    │ CANL  │        │ │
    │   6   │        └┬┘
    └───────┘         │
        │             │
        └─────────────┴─────────▶ NMEA 2000 Bus
```

### Switchable 120Ω Termination
```
CANH ───────┬─────────────────
            │
           ┌┴┐
           │ │ 60Ω
           │ │
           └┬┘
            │
            ├─── Switch/Jumper
            │
           ┌┴┐
           │ │ 60Ω
           │ │
           └┬┘
            │
CANL ───────┴─────────────────

Alternative: Single 120Ω resistor with switch
CANH ────┬───[Switch]───[120Ω]───┬──── CANL
         │                        │
```

### Optional Galvanic Isolation (ADUM1201 or ISO1050)
```
                MCU Side                 │                CAN Side
                                         │
    MCU TX ────▶ TXI  TXO ───────────────┼────────────▶ CAN_TX
                                         │
    MCU RX ◀──── RXO  RXI ◀──────────────┼───────────── CAN_RX
                                         │
         VDD1    ISO    VDD2             │
           │    ADUM    │                │
          GND1   1201  GND2              │
                                         │
                      Isolation Barrier

Components:
- ADUM1201: Dual-channel digital isolator
- Isolated DC-DC converter (5V to 5V)
- Additional decoupling capacitors
```

## Pin Connections

### MCU to CAN Transceiver (ESP32 Example)
| ESP32 Pin | Function | CAN Transceiver Pin |
|-----------|----------|---------------------|
| GPIO 4    | CAN TX   | TXD (Pin 1)        |
| GPIO 5    | CAN RX   | RXD (Pin 4)        |
| 5V        | Power    | VDD (Pin 3)        |
| GND       | Ground   | VSS (Pin 2)        |

### CAN Bus Pinout (DeviceNet Micro-C Connector)
| Pin | Signal | Color (Standard) | Description |
|-----|--------|------------------|-------------|
| 1   | Drain  | Shield/Bare      | Shield Ground |
| 2   | V+     | Red              | +12V (9-16V) |
| 3   | GND    | Black            | Ground |
| 4   | CAN_H  | White            | CAN High |
| 5   | CAN_L  | Blue             | CAN Low |

## Component Values

### Essential Components
- **Bypass Capacitor**: 100nF ceramic (X7R), 0805 package
- **Termination Resistor**: 120Ω ±1%, 1/4W
  - Alternative: 2x 60Ω in series
- **Mode Select Resistor**: 10kΩ to GND (high-speed mode for MCP2551)
- **Pull-up Resistor (optional)**: 10kΩ on RXD line

### Protection Components (Recommended)
- **TVS Diodes**: SMAJ24CA or similar for CAN lines
- **Common Mode Choke**: For EMI suppression
- **Series Resistors**: 22Ω-47Ω on CAN_H and CAN_L for EMC

## PCB Layout Considerations
1. **Ground Plane**: Use solid ground plane under CAN transceiver
2. **Trace Width**: Minimum 10 mil (0.25mm) for CAN signals
3. **Differential Pair**: Keep CAN_H and CAN_L traces matched length
4. **Spacing**: Maintain consistent spacing between CAN_H and CAN_L
5. **Via Placement**: Minimize vias in CAN signal paths
6. **Termination Placement**: Place termination resistor close to connector

## Design Notes
1. **NMEA 2000 Data Rate**: Fixed at 250 kbps
2. **Bus Voltage**: 9-16V DC (nominal 12V)
3. **Termination**: Only enable at both ends of the bus
4. **Cable**: Use twisted pair, 120Ω characteristic impedance
5. **Stub Length**: Keep drop cables (stubs) < 6 meters
6. **Max Bus Length**: Up to 200 meters depending on drop cables

## Testing Points
- TP1: VDD (3.3V or 5V)
- TP2: CAN_H (Dominant: ~3.5V, Recessive: 2.5V)
- TP3: CAN_L (Dominant: ~1.5V, Recessive: 2.5V)
- TP4: TXD (Logic level from MCU)
- TP5: RXD (Logic level to MCU)

## References
- NMEA 2000 Standard (IEC 61162-3)
- ISO 11898 (CAN Specification)
- DeviceNet Micro-C Connector Standard
- Component datasheets (see hardware/datasheets/)
