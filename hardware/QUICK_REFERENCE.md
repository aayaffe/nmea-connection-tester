# RS-422/RS-485 Interface Quick Reference

**One-page reference for the NMEA 0183 RS-422/RS-485 interface design**

## Circuit at a Glance

```
         3.3V
          │
    ┌─────┴─────┐
    │  MAX3485  │  RS-485 Transceiver
    │  (SOIC-8) │
    └─────┬─────┘
          │
     ┌────┴────┐
     │ PRTR5V0U2X │  ESD Protection
     └────┬────┘
          │
       ┌──┴──┐
     A │     │ B   Differential Lines (120Ω termination)
       └──┬──┘
          │
      [RJ45/Terminal]  External Connector
```

## Pin Connections

### MAX3485ESA+ (U1)
| Pin | Name | Connect To | Description |
|-----|------|------------|-------------|
| 1 | RO | MCU RX | Receiver Output |
| 2 | RE | MCU GPIO | Receiver Enable (active LOW) |
| 3 | DE | MCU GPIO | Driver Enable (active HIGH) |
| 4 | DI | MCU TX | Driver Input |
| 5 | GND | Ground | Ground |
| 6 | A | ESD → Connector | Non-inverting I/O |
| 7 | B | ESD → Connector | Inverting I/O |
| 8 | VCC | 3.3V + C1/C2 | Power Supply |

**Note**: Tie RE and DE together, control with single GPIO

### Connector Pinout (RJ45 Option)
| Pin | Signal | Description |
|-----|--------|-------------|
| 3 | A | Data+ (non-inverting) |
| 6 | B | Data- (inverting) |
| 4,5 | GND | Ground/Shield (optional) |

### Connector Pinout (Screw Terminal Option)
| Terminal | Signal | Description |
|----------|--------|-------------|
| 1 | A | Data+ (non-inverting) |
| 2 | B | Data- (inverting) |
| 3 | GND | Signal Ground |

## Component Values

| Ref | Value | Package | Description |
|-----|-------|---------|-------------|
| U1 | MAX3485ESA+ | SOIC-8 | RS-485 Transceiver |
| D1 | PRTR5V0U2X | SOT-143 | ESD Protection (2-channel) |
| RT1 | 120Ω 1% | 0805 | Termination Resistor |
| R1, R2 | 150Ω 5% | 0805 | LED Current Limiting |
| R3, R4 | 560Ω 5% | 0805 | Bias/Failsafe Network |
| C1 | 100nF X7R | 0805 | HF Decoupling |
| C2 | 10µF X7R | 0805 | Bulk Decoupling |
| LED1 | Green 0805 | 0805 | TX Activity Indicator |
| LED2 | Yellow 0805 | 0805 | RX Activity Indicator |
| SW1 | SPST or Jumper | - | Termination Enable (optional) |

## Key Specifications

| Parameter | Value | Notes |
|-----------|-------|-------|
| **Supply Voltage** | 3.3V ±5% | 3.0V to 3.6V range |
| **Supply Current** | <10 mA | Typical operation |
| **Baud Rates** | 4800, 38400 | Standard & high-speed |
| **Data Format** | 8N1 | 8 data, no parity, 1 stop |
| **Differential Output** | ±2V typ | @ 120Ω load |
| **Common-Mode Range** | -7V to +12V | Input range |
| **Receiver Threshold** | ±200mV | Sensitivity |
| **ESD Protection** | ±12kV | Contact discharge |
| **Max Cable (4800)** | 1000m | With termination |
| **Max Cable (38400)** | 300m | With termination |
| **Temperature** | -40°C to +85°C | Operating range |

## UART Configuration

```
Baud Rate: 4800 or 38400 bps
Data Bits: 8
Parity: None
Stop Bits: 1
Flow Control: None
```

## Firmware Control

### Direction Control
```c
// Transmit Mode
GPIO_DE_RE = HIGH;  // Enable driver, disable receiver
UART_Transmit(data);
GPIO_DE_RE = LOW;   // Back to receive mode

// Receive Mode (default)
GPIO_DE_RE = LOW;   // Enable receiver, disable driver
data = UART_Receive();
```

### LED Control
```c
// TX Activity
GPIO_TX_LED = HIGH;  // Turn on during transmission
// ... transmit ...
GPIO_TX_LED = LOW;

// RX Activity  
GPIO_RX_LED = HIGH;  // Turn on during reception
// ... receive ...
GPIO_RX_LED = LOW;
```

## PCB Layout Checklist

- [ ] Differential traces (A/B): 20 mil width, 10 mil spacing, 100Ω impedance
- [ ] Traces matched to within 5mm length
- [ ] ESD diodes within 5mm of connector
- [ ] Decoupling caps within 3mm of U1 VCC pin
- [ ] Solid ground plane under entire circuit
- [ ] No splits in ground under differential traces
- [ ] Via stitching every 10mm around perimeter
- [ ] Power traces minimum 40 mil width
- [ ] Test points for A, B, GND signals

## Common Troubleshooting

| Symptom | Likely Cause | Check |
|---------|--------------|-------|
| No communication | Power issue | VCC present? 3.3V? |
| No communication | Wrong DE/RE state | DE/RE toggling correctly? |
| Poor signal quality | No termination | RT1 installed? 120Ω? |
| Poor signal quality | Wrong impedance | Trace width/spacing correct? |
| Intermittent errors | ESD damage | D1 installed correctly? |
| High error rate | Baud rate mismatch | MCU crystal accurate? |
| Bus contention | Timing issue | Only one transmitter at a time? |

## NMEA Sentence Format

```
$GPGGA,123519,4807.038,N,01131.000,E,1,08,0.9,545.4,M,46.9,M,,*47<CR><LF>

$ - Start character
GPGGA - Sentence ID (GPS Fix Data)
[...data fields separated by commas...]
*47 - Checksum (XOR of all chars between $ and *)
<CR><LF> - End of sentence (0x0D 0x0A)

Maximum length: 82 characters including $ and <CR><LF>
```

## Testing Checklist

### Basic Bring-Up
- [ ] Visual inspection (no shorts, correct orientation)
- [ ] Power-on test (measure 3.3V at U1 pin 8)
- [ ] Current draw check (<10 mA typical)
- [ ] DE/RE control test (toggles 0V to 3.3V)
- [ ] LED test (turn on/off manually)

### Electrical Tests
- [ ] Differential output voltage (≥±2V @ 120Ω)
- [ ] Receiver sensitivity (±200mV threshold)
- [ ] Common-mode range (-7V to +12V)
- [ ] Signal quality with oscilloscope (clean square wave)

### Functional Tests
- [ ] Transmit NMEA sentence at 4800 baud
- [ ] Transmit NMEA sentence at 38400 baud
- [ ] Receive from commercial NMEA device
- [ ] Verify checksums calculate correctly
- [ ] Test with 1m, 10m, 50m cables

## Supplier Quick Links

### Components
- **Digi-Key**: https://www.digikey.com/
- **Mouser**: https://www.mouser.com/
- **LCSC**: https://www.lcsc.com/

### PCB Fabrication
- **JLCPCB**: https://jlcpcb.com/ (budget, fast)
- **PCBWay**: https://www.pcbway.com/ (mid-range)
- **OSH Park**: https://oshpark.com/ (premium, small runs)

## Design Files Location

```
hardware/
├── README.md                          # Overview and getting started
├── QUICK_REFERENCE.md                 # This file
├── schematics/
│   ├── rs422_rs485_interface.md       # Complete schematic
│   └── DESIGN_SUMMARY.md              # Design summary
├── pcb/
│   └── layout_guidelines.md           # PCB layout rules
├── bom/
│   └── rs485_interface_bom.md         # Bill of materials
└── datasheets/
    └── compliance_verification_plan.md # Testing procedures
```

## Standards Reference

- **NMEA 0183 v4.11**: NMEA sentence format and electrical specs
- **RS-422 (TIA/EIA-422-B)**: Differential signaling standard
- **RS-485 (TIA/EIA-485-A)**: Multi-drop bus standard
- **IEC 61162-1**: Maritime equipment digital interface

## Bill of Materials Summary

| Category | Qty | Total Cost (1x) | Total Cost (100x) |
|----------|-----|-----------------|-------------------|
| Active (U1, D1) | 2 | $2.74 | $1.20 |
| Passive (R, C) | 8 | $0.96 | $0.42 |
| Connector | 1 | $1.25 | $0.75 |
| LEDs | 2 | $0.30 | $0.15 |
| Optional (SW1) | 1 | $0.85 | $0.35 |
| **Component Total** | 14-15 | **$6.21** | **$2.94** |
| PCB | - | $2.00 | $2.00 |
| Assembly | - | $3.00 | $1.50 |
| **Grand Total** | - | **$11.21** | **$6.44** |

## Key Design Features

✓ Low power: 375µA typical, <10mA operating  
✓ Wide common-mode range: -7V to +12V  
✓ Extended cable support: 1000m @ 4800 baud  
✓ Robust ESD protection: ±12kV contact discharge  
✓ Failsafe operation: Bias resistors ensure defined idle state  
✓ Visual feedback: TX/RX activity LEDs  
✓ Flexible termination: Optional 120Ω with jumper/switch  
✓ Standards compliant: NMEA 0183, RS-422, RS-485  
✓ Industrial temperature: -40°C to +85°C  
✓ Cost effective: $6.44/unit @ 100 quantity  

---

**For detailed information, refer to the complete documentation in the hardware/ directory.**

**Design Status**: ✅ COMPLETE - Ready for Prototype Implementation

**Version**: 1.0 | **Date**: 2025-10-21 | **License**: See repository LICENSE file
