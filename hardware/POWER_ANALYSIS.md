# Display Module Power Consumption Analysis

## Overview

This document provides detailed power consumption analysis for the selected ILI9488 3.5" TFT LCD display module, including theoretical calculations, measurement methodology, and optimization strategies for battery-operated NMEA Connection Tester device.

## Power Supply Requirements

### Display Module Specifications

| Component | Voltage | Current (Typical) | Current (Max) | Power (Typical) |
|-----------|---------|-------------------|---------------|-----------------|
| Display Logic | 3.3V | 20mA | 30mA | 66mW |
| Backlight (100%) | 3.3V | 100mA | 120mA | 330mW |
| Touch Controller | 3.3V | 5mA | 10mA | 16.5mW |
| **Total (100% BL)** | 3.3V | **125mA** | **160mA** | **412.5mW** |

### Backlight Power Consumption by Brightness Level

| Brightness Level | Duty Cycle | Current | Power | Use Case |
|------------------|------------|---------|-------|----------|
| 0% (Off) | 0% | 20mA | 66mW | Display off, logic only |
| 10% | 10% | 30mA | 99mW | Night mode |
| 20% | 20% | 40mA | 132mW | Indoor low light |
| 50% | 50% | 70mA | 231mW | Indoor normal |
| 75% | 75% | 95mA | 313.5mW | Indoor bright |
| 100% | 100% | 120mA | 396mW | Outdoor/sunlight |

## Theoretical Power Analysis

### Calculation Method

```
Display Power = Logic Power + Backlight Power + Touch Power

Where:
- Logic Power (constant) = 3.3V × 20mA = 66mW
- Backlight Power = 3.3V × (10mA + brightness% × 90mA)
- Touch Power (when active) = 3.3V × 5mA = 16.5mW

Total = 66mW + (3.3V × (10 + 0.9 × brightness_percent)) + 16.5mW
```

### Power at Different Brightness Levels

#### 100% Brightness (Outdoor)
```
Power = 66mW + (3.3V × 100mA) + 16.5mW
Power = 66mW + 330mW + 16.5mW = 412.5mW
Current = 125mA @ 3.3V
```

#### 50% Brightness (Typical Indoor)
```
Power = 66mW + (3.3V × 55mA) + 16.5mW
Power = 66mW + 181.5mW + 16.5mW = 264mW
Current = 80mA @ 3.3V
```

#### 20% Brightness (Low Light)
```
Power = 66mW + (3.3V × 28mA) + 16.5mW
Power = 66mW + 92.4mW + 16.5mW = 174.9mW
Current = 53mA @ 3.3V
```

## Battery Life Estimation

### Battery Configuration

**Assumed Battery**: Rechargeable Li-ion 18650
- **Capacity**: 2500mAh @ 3.7V nominal
- **Energy**: 9.25Wh (2500mAh × 3.7V)
- **Voltage Range**: 4.2V (full) to 3.0V (cutoff)
- **With 3.3V Regulator**: ~85% efficiency

**Effective Energy Available**: 9.25Wh × 0.85 = 7.86Wh

### Battery Life by Operating Mode

| Operating Mode | Display Power | ESP32 Power* | Total Power | Runtime (2500mAh) |
|----------------|---------------|--------------|-------------|-------------------|
| **Full Operation (100% BL)** | 412.5mW | 240mW | 652.5mW | **12.0 hours** |
| **Typical Use (50% BL)** | 264mW | 240mW | 504mW | **15.6 hours** |
| **Power Save (20% BL)** | 174.9mW | 240mW | 414.9mW | **18.9 hours** |
| **Display Off** | 66mW | 160mW | 226mW | **34.8 hours** |
| **Deep Sleep** | 3mW | 10mW | 13mW | **604 hours (25 days)** |

\* ESP32 power estimates:
- Active with WiFi/BLE: 240mW (typical)
- Active without WiFi/BLE: 160mW
- Light sleep: 50mW
- Deep sleep: 10mW

### Usage Pattern Analysis

**Typical NMEA Testing Session**:
- Active testing: 2 hours @ 50% brightness → 1008mWh
- Standby/idle: 6 hours @ 20% brightness → 2489mWh
- Deep sleep: 16 hours → 208mWh
- **Daily Total**: 3705mWh (47% of battery)
- **Battery Life**: ~2 days per charge

**Intensive Field Use**:
- Active testing: 8 hours @ 75% brightness → 4740mWh
- Standby/idle: 2 hours @ 50% brightness → 1008mWh
- Deep sleep: 14 hours → 182mWh
- **Daily Total**: 5930mWh (75% of battery)
- **Battery Life**: ~1.3 days per charge

**Optimal Power-Saving Mode**:
- Active testing: 4 hours @ 50% brightness → 2016mWh
- Standby/idle: 4 hours @ 20% brightness → 1660mWh
- Deep sleep: 16 hours → 208mWh
- **Daily Total**: 3884mWh (49% of battery)
- **Battery Life**: ~2 days per charge

## Measurement Methodology

### Required Equipment

1. **Multimeter** (DMM): ±0.1mA accuracy or better
2. **USB Power Monitor**: Ruideng UM25C or equivalent
3. **Oscilloscope** (optional): For PWM waveform analysis
4. **Lab Power Supply**: Adjustable 3.3V with current monitoring
5. **Load Simulator**: For battery drain testing

### Measurement Setup

```
┌─────────────┐      ┌──────────────┐      ┌─────────────┐
│   Battery   │──────│ Current Meter│──────│   Display   │
│  or Power   │      │   (in series)│      │   Module    │
│   Supply    │      └──────────────┘      └─────────────┘
└─────────────┘              │
     3.3V                    │
                        Measure Here
```

### Test Procedure

#### Test 1: Static Power Consumption

```cpp
// Measure power at different brightness levels
void testStaticPower() {
    for (int brightness = 0; brightness <= 255; brightness += 25) {
        setBacklight(brightness);
        delay(5000);  // Allow stabilization
        
        // Record current reading from multimeter
        Serial.print("Brightness: ");
        Serial.print(brightness);
        Serial.println(" (Record current now)");
        
        delay(10000);  // 10s measurement window
    }
}
```

**Expected Results** (to be verified):
- 0% brightness: ~25mA
- 25% brightness: ~45mA
- 50% brightness: ~70mA
- 75% brightness: ~95mA
- 100% brightness: ~125mA

#### Test 2: Dynamic Content Power Consumption

```cpp
// Measure power during typical NMEA display operations
void testDynamicPower() {
    setBacklight(128);  // 50% brightness
    
    // Test 1: Static display (no updates)
    tft.fillScreen(TFT_BLACK);
    tft.setCursor(0, 0);
    tft.println("Static Text - Measure Now");
    delay(30000);  // 30s measurement
    
    // Test 2: Scrolling text (1 line/second)
    for (int i = 0; i < 60; i++) {
        tft.fillScreen(TFT_BLACK);
        tft.setCursor(0, 0);
        tft.println("Line " + String(i));
        delay(1000);
    }
    
    // Test 3: Full screen color changes (worst case)
    for (int i = 0; i < 30; i++) {
        tft.fillScreen(TFT_RED);
        delay(500);
        tft.fillScreen(TFT_GREEN);
        delay(500);
        tft.fillScreen(TFT_BLUE);
        delay(500);
    }
}
```

#### Test 3: Battery Life Test

```cpp
// Run until battery depleted
void batteryLifeTest() {
    setBacklight(128);  // 50% brightness
    unsigned long startTime = millis();
    unsigned long lastUpdate = millis();
    
    tft.fillScreen(TFT_BLACK);
    
    while (true) {
        if (millis() - lastUpdate > 1000) {
            lastUpdate = millis();
            unsigned long elapsedSeconds = (millis() - startTime) / 1000;
            unsigned long hours = elapsedSeconds / 3600;
            unsigned long minutes = (elapsedSeconds % 3600) / 60;
            unsigned long seconds = elapsedSeconds % 60;
            
            // Read battery voltage (ADC)
            float batteryVoltage = readBatteryVoltage();
            
            tft.setCursor(10, 10);
            tft.setTextColor(TFT_GREEN, TFT_BLACK);
            tft.printf("Runtime: %02lu:%02lu:%02lu\n", hours, minutes, seconds);
            tft.printf("Battery: %.2fV\n", batteryVoltage);
            
            // Log to serial
            Serial.printf("%lu,%f\n", elapsedSeconds, batteryVoltage);
            
            // Check for battery cutoff
            if (batteryVoltage < 3.0) {
                tft.fillScreen(TFT_RED);
                tft.setCursor(10, 10);
                tft.println("BATTERY DEPLETED");
                Serial.println("Test complete");
                while(1);  // Stop
            }
        }
    }
}
```

#### Test 4: Touch Power Consumption

```cpp
// Measure touch controller power impact
void testTouchPower() {
    setBacklight(128);  // 50% brightness
    
    // Baseline (no touch)
    delay(30000);  // 30s baseline measurement
    
    // Continuous touch polling
    Serial.println("Start continuous touch polling");
    for (int i = 0; i < 300; i++) {  // 5 minutes
        uint16_t x, y;
        bool touched = tft.getTouch(&x, &y);
        if (touched) {
            Serial.printf("Touch: %d, %d\n", x, y);
        }
        delay(100);  // 10Hz polling
    }
    Serial.println("End touch polling");
}
```

### Measurement Results Template

**Test Date**: _____________
**Hardware Version**: _____________
**Temperature**: _____________°C
**Battery**: _____________ (model/capacity)

| Test | Brightness | Display State | Current (mA) | Power (mW) | Notes |
|------|------------|---------------|--------------|------------|-------|
| 1.1 | 0% | Black screen | | | Display off |
| 1.2 | 25% | Black screen | | | Low brightness |
| 1.3 | 50% | Black screen | | | Medium brightness |
| 1.4 | 75% | Black screen | | | High brightness |
| 1.5 | 100% | Black screen | | | Max brightness |
| 2.1 | 50% | Static text | | | Typical NMEA display |
| 2.2 | 50% | Scrolling text (1Hz) | | | Active updates |
| 2.3 | 50% | Full screen updates | | | Worst case |
| 3.1 | 50% | Battery life test | | | Time to cutoff: ___h |
| 4.1 | 50% | Touch active | | | With touch polling |

## Power Optimization Strategies

### 1. Intelligent Backlight Management

```cpp
class BacklightManager {
private:
    const int PIN_BACKLIGHT = 32;
    const int PIN_AMBIENT_LIGHT = 34;  // ADC pin
    const int PWM_CHANNEL = 0;
    const int PWM_FREQ = 5000;
    const int PWM_RESOLUTION = 8;
    
    uint8_t currentBrightness = 128;
    uint8_t targetBrightness = 128;
    unsigned long lastActivity = 0;
    
public:
    void begin() {
        ledcSetup(PWM_CHANNEL, PWM_FREQ, PWM_RESOLUTION);
        ledcAttachPin(PIN_BACKLIGHT, PWM_CHANNEL);
        setTargetBrightness(128);
    }
    
    void setTargetBrightness(uint8_t brightness) {
        targetBrightness = constrain(brightness, 10, 255);  // Min 10 for visibility
        lastActivity = millis();
    }
    
    void update() {
        // Smooth transition
        if (currentBrightness < targetBrightness) {
            currentBrightness += 2;
        } else if (currentBrightness > targetBrightness) {
            currentBrightness -= 2;
        }
        
        // Auto-dim after 30 seconds of inactivity
        if (millis() - lastActivity > 30000) {
            targetBrightness = 50;  // Dim to 20%
        }
        
        // Turn off after 60 seconds
        if (millis() - lastActivity > 60000) {
            targetBrightness = 0;
        }
        
        ledcWrite(PWM_CHANNEL, currentBrightness);
    }
    
    void autoAdjust() {
        // Read ambient light sensor
        int ambientLight = analogRead(PIN_AMBIENT_LIGHT);
        
        // Map to brightness (0-1023 ADC to 10-255 brightness)
        targetBrightness = map(ambientLight, 0, 1023, 10, 255);
        targetBrightness = constrain(targetBrightness, 10, 255);
        
        lastActivity = millis();
    }
    
    void onUserActivity() {
        // Reset timeout
        lastActivity = millis();
        
        // Restore brightness if dimmed
        if (targetBrightness < 100) {
            autoAdjust();  // Re-read ambient light
        }
    }
};
```

### 2. Display Update Optimization

```cpp
// Use partial updates instead of full screen refresh
void updateNMEALine(int line, const char* text) {
    const int lineHeight = 20;
    int y = line * lineHeight;
    
    // Only redraw the changed line
    tft.fillRect(0, y, 480, lineHeight, TFT_BLACK);
    tft.setCursor(5, y + 2);
    tft.print(text);
}

// Use sprites for complex updates
TFT_eSprite sprite = TFT_eSprite(&tft);

void efficientUpdate() {
    // Create sprite once
    static bool spriteCreated = false;
    if (!spriteCreated) {
        sprite.createSprite(480, 100);
        spriteCreated = true;
    }
    
    // Draw to sprite (fast)
    sprite.fillSprite(TFT_BLACK);
    sprite.setCursor(10, 10);
    sprite.println("Efficient update");
    
    // Push to display (single SPI transaction)
    sprite.pushSprite(0, 0);
}
```

### 3. Power State Management

```cpp
enum PowerState {
    FULL_POWER,      // 100% brightness, all features
    NORMAL,          // Auto brightness, normal operation
    POWER_SAVE,      // Reduced brightness, reduced updates
    DISPLAY_OFF,     // Display off, logic active
    DEEP_SLEEP       // Everything off except wake sources
};

class PowerManager {
private:
    PowerState currentState = NORMAL;
    BacklightManager backlight;
    
public:
    void setState(PowerState newState) {
        currentState = newState;
        
        switch (newState) {
            case FULL_POWER:
                backlight.setTargetBrightness(255);
                setCpuFrequency(240);  // Full speed
                break;
                
            case NORMAL:
                backlight.autoAdjust();
                setCpuFrequency(160);  // Reduced speed
                break;
                
            case POWER_SAVE:
                backlight.setTargetBrightness(50);  // 20% brightness
                setCpuFrequency(80);   // Minimum speed
                break;
                
            case DISPLAY_OFF:
                backlight.setTargetBrightness(0);
                break;
                
            case DEEP_SLEEP:
                backlight.setTargetBrightness(0);
                // Configure wake sources
                esp_sleep_enable_ext0_wakeup(GPIO_NUM_0, 0);  // Button wake
                esp_deep_sleep_start();
                break;
        }
    }
    
    void update() {
        backlight.update();
    }
};
```

### 4. Scheduled Sleep Modes

```cpp
void setupPowerManagement() {
    // Auto power-save after 5 minutes idle
    esp_sleep_enable_timer_wakeup(5 * 60 * 1000000ULL);  // 5 min in microseconds
    
    // Wake on button press
    esp_sleep_enable_ext0_wakeup(GPIO_NUM_0, 0);
    
    // Wake on touch
    esp_sleep_enable_ext1_wakeup(BIT(GPIO_NUM_22), ESP_EXT1_WAKEUP_ANY_HIGH);
}

void checkIdleTimeout() {
    static unsigned long lastActivityTime = millis();
    
    if (millis() - lastActivityTime > 300000) {  // 5 minutes
        Serial.println("Entering power save mode");
        powerManager.setState(POWER_SAVE);
    }
    
    if (millis() - lastActivityTime > 600000) {  // 10 minutes
        Serial.println("Entering deep sleep");
        powerManager.setState(DEEP_SLEEP);
    }
}
```

## Expected Power Savings

| Strategy | Power Reduction | Runtime Improvement |
|----------|-----------------|---------------------|
| Auto-brightness (50% avg) | ~50mW | +15% |
| Display timeout (30s) | ~100mW avg | +25% |
| Partial updates | ~10mW | +3% |
| Deep sleep (idle 50% of day) | ~200mW avg | +40% |
| **Combined** | **~360mW avg** | **~60% improvement** |

## Comparison with Alternative Displays

| Display Type | Typical Power | Advantage | Disadvantage |
|--------------|---------------|-----------|--------------|
| ILI9488 TFT (selected) | 264mW @ 50% | Good balance, fast refresh | Moderate power |
| OLED (128x64) | 30-50mW | Low power for static | Poor sunlight readability |
| E-Paper (3.7") | <1mW standby | Ultra-low power | Very slow refresh (unusable for real-time) |

**Conclusion**: The selected ILI9488 TFT provides the best balance of:
- Acceptable power consumption (264mW typical)
- Excellent sunlight readability (critical for marine use)
- Fast refresh rate (required for real-time NMEA data)
- Good battery life (15+ hours typical use)

## Acceptance Criteria Status

- [x] **Power consumption analysis** - Completed with theoretical calculations
- [x] **Measurement methodology** - Documented procedures and test code
- [x] **Battery life estimation** - Calculated for various usage patterns
- [x] **Optimization strategies** - Documented with code examples
- [ ] **Physical measurements** - Pending hardware availability

## Next Steps

1. Order display module and measurement equipment
2. Build prototype hardware
3. Execute measurement tests as documented
4. Validate theoretical calculations against measured data
5. Fine-tune optimization strategies based on results
6. Update this document with actual measurement data

## References

1. ILI9488 Datasheet - Power consumption specifications
2. ESP32 Datasheet - Power modes and consumption
3. Li-ion Battery Handbook - Battery characteristics
4. Application Note AN-1110 - TFT LCD Power Management

---

**Document Version**: 1.0  
**Date**: 2025-10-21  
**Status**: THEORETICAL ANALYSIS COMPLETE - Awaiting hardware validation
**Next Review**: After prototype measurements
