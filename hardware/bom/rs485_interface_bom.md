# Bill of Materials (BOM) - RS-422/RS-485 Interface

## Overview
Complete bill of materials for the NMEA 0183 RS-422/RS-485 interface circuit with supplier information, part numbers, and pricing.

**Document Version:** 1.0  
**Date:** 2025-10-21  
**Currency:** USD  
**Pricing Date:** October 2025  

## BOM Summary

| Category | Quantity | Total Cost (1 unit) | Total Cost (100 units) |
|----------|----------|---------------------|------------------------|
| Active Components | 3 | $2.85 | $127.00 |
| Passive Components | 8 | $0.96 | $42.00 |
| Connectors | 1 | $1.25 | $75.00 |
| LEDs | 2 | $0.30 | $15.00 |
| Optional Components | 1 | $0.85 | $35.00 |
| **TOTAL** | **15** | **$6.21** | **$294.00** |

*Note: Prices are approximate and subject to change. Bulk pricing shown for 100 units.*

---

## Detailed BOM

### Active Components

#### U1 - RS-485/RS-422 Transceiver

**Primary Option: MAX3485ESA+**

| Parameter | Specification |
|-----------|---------------|
| **Manufacturer** | Analog Devices (Maxim Integrated) |
| **Part Number** | MAX3485ESA+ |
| **Package** | SOIC-8 |
| **Description** | 3.3V RS-485/RS-422 Transceiver, Half-Duplex, Slew-Rate Limited |
| **Key Features** | - Low power (375µA operating)<br>- Extended common-mode range (-7V to +12V)<br>- ±15kV ESD protection<br>- Fail-safe receiver<br>- 10 Mbps max data rate |
| **Operating Voltage** | 3.0V to 3.6V |
| **Temperature Range** | -40°C to +85°C (Industrial) |
| **Quantity per Board** | 1 |

**Supplier Information:**
| Supplier | Part Number | Stock | Unit Price (1) | Unit Price (100+) | Link |
|----------|-------------|-------|----------------|-------------------|------|
| **Digi-Key** | MAX3485ESA+-ND | In Stock | $2.45 | $1.15 | https://www.digikey.com/en/products/detail/MAX3485ESA/MAX3485ESA-ND |
| **Mouser** | 700-MAX3485ESA | In Stock | $2.50 | $1.20 | https://www.mouser.com/ProductDetail/700-MAX3485ESA |
| **Newark** | 58K3356 | In Stock | $2.48 | $1.17 | https://www.newark.com/maxim-integrated/MAX3485ESA |
| **LCSC** | C116220 | In Stock | $1.85 | $0.89 | https://www.lcsc.com/product-detail/MAX3485ESA |

**Alternative Components:**

| Part Number | Manufacturer | Description | Price (1/100+) | Notes |
|-------------|--------------|-------------|----------------|-------|
| **MAX485ESA+** | Analog Devices | 5V RS-485 Transceiver | $1.85 / $0.95 | Lower cost, requires 5V supply |
| **SN65HVD78DR** | Texas Instruments | 3.3V RS-485 Transceiver | $2.10 / $1.05 | Automotive grade, better ESD |
| **LTC2850IS8** | Analog Devices | 3.3V RS-485 Transceiver | $3.20 / $1.65 | Best EMI performance |
| **MAX13487EESA+** | Analog Devices | 3.3V RS-485 Transceiver | $2.95 / $1.45 | Enhanced ESD protection |

**Recommended:** MAX3485ESA+ (best cost/performance balance)

---

#### D1 - ESD Protection Array

**Primary Option: PRTR5V0U2X**

| Parameter | Specification |
|-----------|---------------|
| **Manufacturer** | NXP Semiconductors |
| **Part Number** | PRTR5V0U2X,215 |
| **Package** | SOT-143 (4-pin) |
| **Description** | Ultra-low capacitance TVS diode array for ESD protection |
| **Key Features** | - 2 bidirectional channels<br>- 0.8pF capacitance (typical)<br>- ±12kV ESD protection (contact)<br>- 9.7V clamping voltage |
| **Working Voltage** | 5.0V |
| **Temperature Range** | -55°C to +125°C |
| **Quantity per Board** | 1 (protects both A and B lines) |

**Supplier Information:**
| Supplier | Part Number | Stock | Unit Price (1) | Unit Price (100+) | Link |
|----------|-------------|-------|----------------|-------------------|------|
| **Digi-Key** | 1727-PRTR5V0U2X215CT-ND | In Stock | $0.29 | $0.11 | https://www.digikey.com/en/products/detail/PRTR5V0U2X-215 |
| **Mouser** | 771-PRTR5V0U2X215 | In Stock | $0.31 | $0.12 | https://www.mouser.com/ProductDetail/771-PRTR5V0U2X215 |
| **Newark** | 76M9796 | In Stock | $0.30 | $0.11 | https://www.newark.com/nxp/PRTR5V0U2X-215 |
| **LCSC** | C12333 | In Stock | $0.19 | $0.08 | https://www.lcsc.com/product-detail/PRTR5V0U2X-215 |

**Alternative Components:**

| Part Number | Manufacturer | Description | Price (1/100+) | Notes |
|-------------|--------------|-------------|----------------|-------|
| **PESD3V3L2BT** | Nexperia | 3.3V ESD Protection | $0.22 / $0.09 | Lower cost option |
| **TPD2E001DRLR** | Texas Instruments | 2-Channel ESD Protection | $0.35 / $0.15 | Better clamping performance |

**Recommended:** PRTR5V0U2X (best signal integrity due to ultra-low capacitance)

---

### Passive Components

#### RT1 - Termination Resistor

| Parameter | Specification |
|-----------|---------------|
| **Value** | 120Ω ±1% |
| **Package** | 0805 (2012 metric) |
| **Power Rating** | 1/4W (0.25W) |
| **Description** | Thin film resistor for differential line termination |
| **Quantity per Board** | 1 |

**Supplier Information:**
| Supplier | Example Part Number | Unit Price (1) | Unit Price (100+) | Link |
|----------|---------------------|----------------|-------------------|------|
| **Digi-Key** | RMCF0805FT120RCT-ND | $0.10 | $0.01 | https://www.digikey.com/en/products/detail/RMCF0805FT120R |
| **Mouser** | 603-RC0805FR-07120RL | $0.10 | $0.01 | https://www.mouser.com/ProductDetail/603-RC0805FR-07120RL |
| **LCSC** | C149504 | $0.01 | $0.001 | https://www.lcsc.com/product-detail/C149504 |

**Recommended:** Any quality 1% 120Ω 0805 resistor (Yageo, Vishay, Panasonic)

---

#### R1, R2 - LED Current Limiting Resistors

| Parameter | Specification |
|-----------|---------------|
| **Value** | 150Ω ±5% |
| **Package** | 0805 (2012 metric) |
| **Power Rating** | 1/8W (0.125W) |
| **Description** | Thick film resistor for LED current limiting (20mA @ 3.3V) |
| **Quantity per Board** | 2 |

**Supplier Information:**
| Supplier | Example Part Number | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|----------------|-------------------|
| **Digi-Key** | RMCF0805JT150RCT-ND | $0.10 | $0.01 |
| **Mouser** | 603-RC0805JR-07150RL | $0.10 | $0.01 |
| **LCSC** | C269901 | $0.01 | $0.001 |

---

#### R3, R4 - Bias/Failsafe Resistors

| Parameter | Specification |
|-----------|---------------|
| **Value** | 560Ω ±5% |
| **Package** | 0805 (2012 metric) |
| **Power Rating** | 1/8W (0.125W) |
| **Description** | Bias resistors for receiver failsafe operation |
| **Quantity per Board** | 2 |

**Supplier Information:**
| Supplier | Example Part Number | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|----------------|-------------------|
| **Digi-Key** | RMCF0805JT560RCT-ND | $0.10 | $0.01 |
| **Mouser** | 603-RC0805JR-07560RL | $0.10 | $0.01 |
| **LCSC** | C28636 | $0.01 | $0.001 |

---

#### C1 - High-Frequency Decoupling Capacitor

| Parameter | Specification |
|-----------|---------------|
| **Value** | 100nF (0.1µF) ±10% |
| **Voltage Rating** | 16V minimum (25V recommended) |
| **Package** | 0805 (2012 metric) |
| **Dielectric** | X7R or X5R ceramic |
| **Description** | High-frequency bypass capacitor for transceiver VCC pin |
| **Quantity per Board** | 1 |

**Supplier Information:**
| Supplier | Example Part Number | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|----------------|-------------------|
| **Digi-Key** | 1276-1003-1-ND | $0.10 | $0.02 |
| **Mouser** | 80-C0805C104K3R | $0.10 | $0.02 |
| **LCSC** | C49678 | $0.01 | $0.005 |

**Recommended:** Samsung CL21B104KBCNNNC or equivalent X7R 0805 capacitor

---

#### C2 - Bulk Decoupling Capacitor

| Parameter | Specification |
|-----------|---------------|
| **Value** | 10µF ±20% |
| **Voltage Rating** | 10V minimum (16V recommended) |
| **Package** | 0805 (2012 metric) |
| **Dielectric** | X7R or X5R ceramic |
| **Description** | Bulk capacitance for transient current support |
| **Quantity per Board** | 1 |

**Supplier Information:**
| Supplier | Example Part Number | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|----------------|-------------------|
| **Digi-Key** | 1276-6456-1-ND | $0.15 | $0.05 |
| **Mouser** | 81-GRM21BR71C106KE1L | $0.18 | $0.06 |
| **LCSC** | C15850 | $0.03 | $0.01 |

**Recommended:** Murata GRM21BR71C106KE15 or equivalent X7R 0805 10µF capacitor

---

### Connectors

#### J1 - External RS-485 Connector

**Option A: RJ45 Modular Jack (Recommended for field use)**

| Parameter | Specification |
|-----------|---------------|
| **Type** | RJ45 8P8C Modular Jack |
| **Mounting** | Through-hole, vertical or right-angle |
| **Shielding** | Shielded (metal cage) |
| **Description** | Standard Ethernet-style connector for RS-485 wiring |
| **Quantity per Board** | 1 |

**Supplier Information:**
| Supplier | Example Part Number | Manufacturer | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|--------------|----------------|-------------------|
| **Digi-Key** | 380-1046-ND | Amphenol | $1.25 | $0.75 |
| **Mouser** | 523-54602-908LF | Amphenol | $1.28 | $0.78 |
| **Newark** | 67AH9184 | TRP | $1.20 | $0.72 |

**Recommended:** Amphenol RJHSE-5381 or equivalent shielded RJ45 jack

**Pinout Assignment:**
- Pin 3: A (Data+)
- Pin 6: B (Data-)
- Pins 4,5: GND/Shield (optional)
- Other pins: Not connected

---

**Option B: Screw Terminal (Recommended for permanent installation)**

| Parameter | Specification |
|-----------|---------------|
| **Type** | 3-position screw terminal |
| **Pitch** | 5.08mm (0.2") |
| **Wire Gauge** | 26-12 AWG |
| **Current Rating** | 10A |
| **Description** | Phoenix Contact style terminal block |
| **Quantity per Board** | 1 |

**Supplier Information:**
| Supplier | Example Part Number | Manufacturer | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|--------------|----------------|-------------------|
| **Digi-Key** | 277-1667-ND | Phoenix Contact | $1.35 | $0.85 |
| **Mouser** | 651-1757019 | Phoenix Contact | $1.40 | $0.90 |
| **Newark** | 97K2183 | Phoenix Contact | $1.38 | $0.88 |

**Recommended:** Phoenix Contact 1757019 (3-pos, 5.08mm) or equivalent

**Pinout Assignment:**
- Terminal 1: A (Data+)
- Terminal 2: B (Data-)
- Terminal 3: GND (Signal ground/shield)

---

### LEDs

#### LED1 - Transmit Activity Indicator

| Parameter | Specification |
|-----------|---------------|
| **Color** | Green |
| **Package** | 0805 SMD |
| **Forward Voltage** | 2.0V typical |
| **Forward Current** | 20mA |
| **Luminous Intensity** | 40mcd minimum |
| **Viewing Angle** | 120° |
| **Quantity per Board** | 1 |

**Supplier Information:**
| Supplier | Example Part Number | Manufacturer | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|--------------|----------------|-------------------|
| **Digi-Key** | 160-1427-1-ND | Lite-On | $0.15 | $0.05 |
| **Mouser** | 859-LTST-C171GKT | Lite-On | $0.16 | $0.06 |
| **LCSC** | C2296 | Everlight | $0.02 | $0.01 |

**Recommended:** Lite-On LTST-C171GKT or Kingbright KP-2012CGCK

---

#### LED2 - Receive Activity Indicator

| Parameter | Specification |
|-----------|---------------|
| **Color** | Yellow/Amber |
| **Package** | 0805 SMD |
| **Forward Voltage** | 2.1V typical |
| **Forward Current** | 20mA |
| **Luminous Intensity** | 30mcd minimum |
| **Viewing Angle** | 120° |
| **Quantity per Board** | 1 |

**Supplier Information:**
| Supplier | Example Part Number | Manufacturer | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|--------------|----------------|-------------------|
| **Digi-Key** | 160-1130-1-ND | Lite-On | $0.15 | $0.05 |
| **Mouser** | 859-LTST-C171YKT | Lite-On | $0.16 | $0.06 |
| **LCSC** | C2297 | Everlight | $0.02 | $0.01 |

**Recommended:** Lite-On LTST-C171YKT or Kingbright KP-2012SYC

---

### Optional Components

#### SW1 - Termination Enable Switch (Optional)

**Option A: DIP Switch**

| Parameter | Specification |
|-----------|---------------|
| **Type** | SPST DIP switch, 1-position |
| **Mounting** | Through-hole |
| **Rating** | 50mA, 12V DC |
| **Description** | Allows enabling/disabling 120Ω termination resistor |
| **Quantity per Board** | 1 |

**Supplier Information:**
| Supplier | Example Part Number | Manufacturer | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|--------------|----------------|-------------------|
| **Digi-Key** | CTS206-2MST-ND | CTS | $0.85 | $0.35 |
| **Mouser** | 774-2062MST | CTS | $0.88 | $0.38 |

---

**Option B: Jumper Header (Lower Cost Alternative)**

| Parameter | Specification |
|-----------|---------------|
| **Type** | 2-pin header, 2.54mm pitch |
| **Mounting** | Through-hole, vertical |
| **Rating** | 3A, 250V AC |
| **Description** | Simple jumper for termination selection |
| **Quantity per Board** | 1 |

**Supplier Information:**
| Supplier | Example Part Number | Manufacturer | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|--------------|----------------|-------------------|
| **Digi-Key** | S1012E-02-ND | Sullins | $0.10 | $0.03 |
| **Mouser** | 649-68001-102HLF | Amphenol | $0.12 | $0.04 |
| **LCSC** | C124375 | BOOMELE | $0.02 | $0.01 |

**Plus Jumper Cap:**
| Supplier | Example Part Number | Unit Price (1) | Unit Price (100+) |
|----------|---------------------|----------------|-------------------|
| **Digi-Key** | S9001-ND | $0.03 | $0.01 |
| **Mouser** | 855-M7566-05 | $0.04 | $0.01 |

**Recommended:** 2-pin header with jumper cap (cost-effective, flexible)

---

## Additional Materials

### Test Points (Optional but Recommended)

| Item | Description | Quantity | Supplier Part# | Unit Price (1) |
|------|-------------|----------|----------------|----------------|
| **TP1-TP6** | SMD Test Point, Loop | 6 | Keystone 5016 | $0.15 |

---

## Assembly Materials

### PCB Specifications

| Parameter | Specification | Typical Cost |
|-----------|---------------|--------------|
| **Size** | 50mm x 30mm | - |
| **Layers** | 2-layer (4-layer optional) | $2-5 per board (100 qty) |
| **Material** | FR-4, 1.6mm thick | Standard |
| **Copper Weight** | 1 oz (35µm) | Standard |
| **Surface Finish** | ENIG or HASL Lead-Free | Add $0.50-1.00 per board |
| **Solder Mask** | Green (standard) | Standard |
| **Silkscreen** | White, both sides | Standard |

**PCB Suppliers:**
- **JLCPCB:** https://jlcpcb.com/ - Budget option, good for prototypes
- **PCBWay:** https://www.pcbway.com/ - Mid-range, good quality
- **OSH Park:** https://oshpark.com/ - Premium, excellent for small runs
- **Advanced Circuits:** https://www.4pcb.com/ - Professional, fast turnaround

---

### Solder Paste and Flux

| Item | Description | Quantity | Typical Cost |
|------|-------------|----------|--------------|
| **Solder Paste** | SAC305 Lead-Free, No-Clean | 10g syringe | $15-25 |
| **Flux** | No-Clean Liquid Flux | 10ml bottle | $8-15 |

---

## Cost Analysis

### Unit Cost Breakdown (Single Board)

| Category | Cost |
|----------|------|
| Active Components | $2.85 |
| Passive Components | $0.96 |
| Connector | $1.25 |
| LEDs | $0.30 |
| Optional (SW1) | $0.85 |
| **Subtotal Components** | **$6.21** |
| PCB (2-layer) | $2.00 |
| Assembly (labor) | $3.00 |
| **Total per Unit** | **$11.21** |

### Volume Pricing (100 Units)

| Category | Total Cost | Per Unit |
|----------|------------|----------|
| Active Components | $127.00 | $1.27 |
| Passive Components | $42.00 | $0.42 |
| Connectors | $75.00 | $0.75 |
| LEDs | $15.00 | $0.15 |
| Optional (SW1) | $35.00 | $0.35 |
| **Subtotal Components** | **$294.00** | **$2.94** |
| PCB (2-layer, 100 qty) | $200.00 | $2.00 |
| Assembly (automated) | $150.00 | $1.50 |
| **Total 100 Units** | **$644.00** | **$6.44** |

**Savings at 100 units:** 42.6% cost reduction per unit

---

## Procurement Notes

### Lead Times
- **Standard Components:** 1-2 weeks (Digi-Key, Mouser)
- **PCB Fabrication:** 1-2 weeks (prototype), 2-4 weeks (production)
- **Assembly:** 1 week (manual), 2-3 weeks (automated)

### Minimum Order Quantities (MOQ)
- **Digi-Key/Mouser:** No MOQ for most components
- **LCSC:** 5-10 pieces typical MOQ
- **Direct from Manufacturer:** 1000-3000 pieces typical

### Recommended Sourcing Strategy
1. **Prototype (1-10 boards):** Source from Digi-Key or Mouser
2. **Small Production (10-100 boards):** Mix of Digi-Key/Mouser and LCSC
3. **Volume Production (100+ boards):** LCSC or direct from manufacturer

### Component Availability
All components are:
- [x] Active and not NRND (Not Recommended for New Designs)
- [x] Available from multiple suppliers
- [x] Have suitable alternatives if needed
- [x] RoHS compliant
- [x] Automotive grade options available

---

## Quality and Compliance

### Component Standards
- **RoHS:** All components RoHS 2 compliant
- **REACH:** EU REACH compliant
- **Conflict Minerals:** Supplier declarations available
- **Operating Temperature:** -40°C to +85°C (Industrial grade)

### Testing and Validation
Recommended testing for each component:
- [ ] Visual inspection (all components)
- [ ] Continuity test (resistors, connections)
- [ ] Capacitance measurement (capacitors)
- [ ] ESD protection verification (TVS diodes)
- [ ] Transceiver functionality test

---

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-10-21 | Design Team | Initial BOM with supplier links |

---

## Notes

1. **Substitutions:** Always verify pin compatibility and electrical characteristics before substituting components.

2. **Supplier Selection:** Choose suppliers based on:
   - Stock availability
   - Lead time requirements
   - Shipping costs
   - Minimum order requirements

3. **Cost Optimization:** For volume production:
   - Consider LCSC or direct manufacturer sourcing
   - Negotiate pricing for quantities over 1000 units
   - Consolidate orders to reduce shipping costs

4. **Lifecycle Management:** Monitor component obsolescence using:
   - Silicon Expert
   - Part Finder tools
   - Manufacturer notifications

5. **Documentation:** Keep all component datasheets in `/hardware/datasheets/` directory for reference.
