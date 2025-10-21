# Connector Specification - NMEA 2000 Interface

## Overview
This document specifies the connector options for NMEA 2000 bus connection. Both DeviceNet Micro-C (M12) and screw terminal alternatives are provided for different installation scenarios.

## Connector Options

### Option 1: DeviceNet Micro-C (M12) Connector (Recommended)

#### Connector Type
**DeviceNet Micro-C 5-pin Male Connector**
- Standard: IEC 61076-2-101
- Also known as: M12 A-coded connector
- NMEA 2000 certified connector type

#### Pinout
```
    Front View (Looking at receptacle)
    
         ╔═══╗
       ╔╝ 1 ╚╗
      ╔╝     ╚╗
     ║ 4     5 ║
      ╚╗  3  ╔╝
       ╚╗ 2 ╔╝
         ╚═╝

Pin Assignment:
```

| Pin | Signal | Wire Color | Function | Voltage/Current |
|-----|--------|------------|----------|-----------------|
| 1   | Shield | Bare/Drain | Cable Shield | Ground |
| 2   | NET-S  | Red | Power (+) | 9-16V DC / 1A |
| 3   | NET-C  | Black | Ground (-) | Return |
| 4   | NET-H  | White | CAN High | 2.5-3.5V |
| 5   | NET-L  | Blue | CAN Low | 1.5-2.5V |

**Note**: NMEA 2000 uses same pinout as DeviceNet

#### Physical Specifications
- **Connector Style**: M12 circular, 5-position
- **Coding**: A-coded (prevents wrong mating)
- **Gender**: Male (device side)
- **Mounting**: Panel mount or PCB mount
- **Cable Entry**: Straight or right-angle
- **IP Rating**: IP67 when mated
- **Operating Temperature**: -40°C to +85°C
- **Rated Voltage**: 60V DC
- **Rated Current**: 4A per pin

#### Recommended Part Numbers

##### PCB Mount (Vertical)
- **Phoenix Contact**: SACC-M12MS-5CON-PG 9
  - P/N: 1513367
  - Price: ~$8-12 USD
  - Availability: Excellent

- **TE Connectivity**: T4043312051-000
  - P/N: T4043312051-000  
  - Price: ~$6-10 USD
  - Availability: Good

- **Molex**: 1300070051
  - P/N: 1300070051
  - Price: ~$7-11 USD
  - Availability: Good

##### PCB Mount (Right-angle)
- **Phoenix Contact**: SACC-M12MS-5CON-PG 9-M
  - P/N: 1513354
  - Price: ~$9-13 USD
  - Angle: 90°

- **Amphenol**: T4143312051-000
  - P/N: T4143312051-000
  - Price: ~$7-11 USD
  - Angle: 90°

##### Panel Mount (with cable)
- **Ancor Marine**: NMEA 2000 Tee Connector
  - Includes: Female connector with cable
  - Price: ~$15-25 USD
  - Common in marine applications

#### Mating Cable Assembly
- **Pre-made cables**: Available from multiple vendors
- **Standard lengths**: 1m, 2m, 5m, 10m, 20m
- **Cable type**: Twisted pair, shielded
- **Wire gauge**: 22 AWG minimum for power
- **Characteristic impedance**: 120Ω
- **Capacitance**: <100pF/m

#### Mechanical Drawing
```
    Side View (PCB Mount - Vertical)
    
    ┌─────────┐
    │ ●     ● │ ← Mounting screws
    │         │
    │  ╔═══╗  │ ← Body
    │  ║ ● ║  │
    │  ║●●●║  │ ← Pins
    │  ╚═╩═╝  │
    └────┼────┘
         │
         ▼
    ─────────── ← PCB surface
         │
    ─────┼───── ← Solder pads
         ●

Dimensions:
- Body diameter: 16mm
- Thread: M12 x 1
- PCB hole diameter: 1.2mm
- Pin spacing: 2.5mm
- Panel cutout: 12.5mm
```

### Option 2: Screw Terminal Block (Alternative)

#### Terminal Block Type
**5-position screw terminal block**
- Pitch: 5.08mm (0.2") or 3.5mm
- Wire entry: Top or side
- Wire size: 18-24 AWG (0.25-1.5mm²)
- Screw type: Phillips or flat head

#### Pinout (Left to Right)
```
┌─────┬─────┬─────┬─────┬─────┐
│  1  │  2  │  3  │  4  │  5  │
│Shield V+  │ GND │CANH │CANL │
└─────┴─────┴─────┴─────┴─────┘

Pin Assignment:
```

| Position | Signal | Wire Color | Terminal Marking |
|----------|--------|------------|------------------|
| 1 | Shield | Bare/Drain | ⏚ or SHLD |
| 2 | V+ | Red | + or VIN |
| 3 | GND | Black | - or GND |
| 4 | CAN_H | White | H or CH |
| 5 | CAN_L | Blue | L or CL |

#### Recommended Part Numbers

##### 5.08mm Pitch
- **Phoenix Contact**: 1757019 (MKDS 1/5-5.08)
  - Price: ~$1.50 USD
  - Current: 10A
  - Voltage: 320V

- **Wurth Elektronik**: 691137710005
  - Price: ~$0.80 USD
  - Current: 10A
  - Voltage: 300V

- **TE Connectivity**: 282837-5
  - Price: ~$1.20 USD
  - Current: 10A
  - Voltage: 300V

##### 3.5mm Pitch (Compact)
- **Phoenix Contact**: 1935161 (PTSM 0.5/5-2.5-H)
  - Price: ~$0.80 USD
  - Current: 6A
  - Wire size: 24-16 AWG

- **Wurth Elektronik**: 691311500005
  - Price: ~$0.60 USD
  - Current: 8A

#### Mechanical Drawing
```
    Top View
    
    ┌─────────────────────────┐
    │ ⊗   ⊗   ⊗   ⊗   ⊗      │ ← Screw heads
    ├─────┬───┬───┬───┬───────┤
    │  1  │ 2 │ 3 │ 4 │  5    │ ← Terminal numbers
    │Shield V+ GND H  L        │ ← Labels
    └─────┴───┴───┴───┴───────┘
    
    Side View
         Wire entry
            ▼
    ┌─────────────┐
    │   ╱╲  ╱╲    │ ← Screw clamp
    │  │  ││  │   │
    ├──┴──┴┴──┴───┤
    │             │
    └──────┬──────┘
           │
    ───────┼─────── ← PCB
           ●

Dimensions (5.08mm pitch):
- Length: 25.4mm (5 positions)
- Height: 8.5mm above PCB
- Depth: 9mm
- Pin diameter: 1.0mm
```

### Option 3: IDC Header (Service/Debug)

#### Header Type
**2x5 pin 0.1" (2.54mm) shrouded header**
- For programming/service cable
- Not for permanent installation
- Protected environment only

#### Pinout
```
    ┌─────────────────┐
    │ ● 2  ● 4  ● 6  ●│8  ●10
    │                  │
    │ ● 1  ● 3  ● 5  ●│7  ● 9
    └─────────────────┘

Pin Assignment:
```

| Pin | Signal | Pin | Signal |
|-----|--------|-----|--------|
| 1 | GND | 2 | V+ (12V) |
| 3 | CAN_L | 4 | CAN_H |
| 5 | NC | 6 | NC |
| 7 | Shield | 8 | 5V out |
| 9 | GND | 10 | 3.3V out |

**Note**: For development use only, not NMEA 2000 compliant

## Cable Specifications

### NMEA 2000 Certified Cable
- **Type**: Twisted pair with shield
- **Impedance**: 120Ω ± 10Ω
- **Capacitance**: 60-100 pF/m
- **Wire gauge**: 
  - Power (V+, GND): 22 AWG minimum
  - Data (CAN_H, CAN_L): 24 AWG minimum
- **Jacket**: Marine-grade PVC or TPE
- **Temperature range**: -40°C to +85°C
- **Color coding**: Per NMEA 2000 standard

### Recommended Cable Types
- **Ancor Marine**: 2000 series NMEA 2000 cable
- **Maretron**: Mid/Mini cable assemblies
- **Garmin**: NMEA 2000 backbone cable
- **Generic**: DeviceNet cable (compatible)

### Cable Length Limits
| Segment Type | Max Length | Gauge |
|--------------|------------|-------|
| Backbone | 200m total | 20 AWG |
| Drop (Tee to device) | 6m | 22 AWG |
| Drop (short) | 1m | 24 AWG |

## Installation Guidelines

### Micro-C Connector Installation
```
1. Strip cable jacket 40mm
2. Strip shield drain wire 10mm
3. Strip individual wires 5mm
4. Crimp pins to wires (if field termination)
5. Insert pins into connector body
6. Verify correct pin assignment
7. Tighten cable gland
8. Apply heat shrink if needed

Tools Required:
- Crimping tool (Micro-C specific)
- Wire stripper (18-24 AWG)
- Multimeter for verification
```

### Screw Terminal Installation
```
1. Strip cable jacket 30mm
2. Strip individual wires 6mm
3. Pre-tin wire ends (optional)
4. Insert wires into terminals
5. Tighten screws (torque: 0.5 N⋅m)
6. Verify mechanical security
7. Check continuity

Tools Required:
- Screwdriver (Phillips or flat)
- Wire stripper
- Multimeter
```

## PCB Footprint

### Micro-C Connector Footprint (KiCAD/Eagle)
```
Pad Layout (mm):
- Pad 1 (Shield): 0, 0 - 1.2mm drill
- Pad 2 (V+): 0, 2.5 - 1.2mm drill
- Pad 3 (GND): 2.5, 0 - 1.2mm drill
- Pad 4 (CAN_H): 2.5, 2.5 - 1.2mm drill
- Pad 5 (CAN_L): 5.0, 1.25 - 1.2mm drill
- Mounting holes: M3, 12.5mm apart

Copper:
- Power pads: 2.0mm diameter
- Signal pads: 1.5mm diameter
- Mounting: 3.5mm diameter

Silkscreen:
- Outline: Connector body profile
- Pin 1 indicator: Triangle or dot
- Pin labels: 1, 2, 3, 4, 5
```

### Screw Terminal Footprint
```
Pad Layout (5.08mm pitch):
- Pad spacing: 5.08mm (0.2")
- Pad diameter: 1.8mm
- Drill diameter: 1.2mm
- Copper annular ring: 0.3mm minimum

5 pads in line:
Pad 1: X=0, Y=0
Pad 2: X=5.08, Y=0
Pad 3: X=10.16, Y=0
Pad 4: X=15.24, Y=0
Pad 5: X=20.32, Y=0

Keep-out zone:
- 2mm around all pads
- 5mm above PCB (component height)
```

## Testing and Verification

### Connector Quality Checks
1. **Mechanical**
   - Insertion/extraction force (10-50N typical)
   - Mating cycles (≥500 cycles)
   - Vibration resistance (per IEC 60512)
   
2. **Electrical**
   - Contact resistance (<10mΩ)
   - Insulation resistance (>100MΩ)
   - Withstand voltage (500V AC, 1 min)
   - Impedance (120Ω for CAN pair)

3. **Environmental**
   - Temperature cycling (-40 to +85°C)
   - Salt spray (if marine use)
   - UV resistance (if exposed)
   - IP rating verification

### Pin Assignment Verification
```
Test Setup:
1. Connect multimeter to device pins
2. Insert probe into connector
3. Verify continuity for each pin
4. Check for shorts between pins
5. Measure impedance on CAN pair

Expected Results:
- Pin 1 to Shield: <0.5Ω
- Pin 2 to V+: <0.5Ω
- Pin 3 to GND: <0.5Ω
- Pin 4 to Pin 5 (CAN): 120Ω differential
- Adjacent pins: >10MΩ (no shorts)
```

## Sourcing Information

### Connector Availability
- **Digi-Key**: Excellent stock, worldwide shipping
- **Mouser**: Good stock, fast delivery
- **RS Components**: European availability
- **Allied Electronics**: Industrial suppliers
- **Newark**: North American distribution

### Lead Times
- Standard parts: 1-2 weeks
- Custom cable assemblies: 2-4 weeks
- Bulk orders (>100): 4-8 weeks

### Minimum Order Quantities (MOQ)
- Off-the-shelf connectors: 1 piece
- Custom assemblies: 10-25 pieces
- Production runs: 100+ pieces

## Cost Comparison

| Connector Type | Unit Cost | Cable Cost | Total |
|---------------|-----------|------------|-------|
| Micro-C PCB mount | $8-12 | $5/m | $13-22 |
| Micro-C with cable | $15-25 | Included | $15-25 |
| Screw terminal | $0.80-1.50 | $2/m | $3-10 |
| IDC header | $0.50 | $5 ribbon | $5.50 |

## Recommendations

### Production Device (Commercial)
**Use: Micro-C Connector**
- Reason: Industry standard, professional appearance
- Best practice: Right-angle PCB mount
- Cost: Higher but worth it for reliability

### Prototype/DIY (Hobbyist)
**Use: Screw Terminal**
- Reason: Easy to wire, no special tools
- Best practice: Add strain relief
- Cost: Very economical

### Development (Lab)
**Use: IDC Header**
- Reason: Quick connect/disconnect
- Best practice: Add both Micro-C and IDC
- Cost: Minimal addition

## Design Files
- KiCAD footprints: `hardware/footprints/connectors.pretty/`
- 3D models: `hardware/3d-models/connectors/`
- Cable specifications: `hardware/datasheets/cables/`

## References
- IEC 61162-3: NMEA 2000 Standard
- IEC 61076-2-101: M12 Circular Connector
- NMEA 2000 Installation Guide
- DeviceNet Physical Layer Specification
