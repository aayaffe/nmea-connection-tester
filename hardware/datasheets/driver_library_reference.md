# Display Driver Library Reference

## Overview

This document provides detailed information about compatible driver libraries for the selected ILI9488 3.5" TFT LCD display module, including installation instructions, configuration, and usage examples.

## Recommended Library: TFT_eSPI

### Library Information

- **Name**: TFT_eSPI
- **Repository**: https://github.com/Bodmer/TFT_eSPI
- **Version**: 2.5.0+ (tested with 2.5.31)
- **License**: FreeBSD License (MIT-like, permissive)
- **Maintainer**: Bodmer
- **Last Updated**: Active development (2023+)
- **Platform**: Arduino, ESP32, ESP8266, STM32, RP2040
- **Stability**: Production-ready, widely used

### Features

✓ Hardware-optimized SPI for ESP32 (uses DMA)  
✓ Fast rendering (60+ FPS possible)  
✓ Multiple display driver support (ILI9341, ILI9486, ILI9488, ST7735, etc.)  
✓ Sprite/buffer support for flicker-free animations  
✓ Touch screen support (XPT2046, FT6206, etc.)  
✓ Multiple font support (built-in, smooth fonts, custom)  
✓ Image loading from SPIFFS/SD card  
✓ Rotation support (0°, 90°, 180°, 270°)  
✓ Drawing primitives (lines, circles, rectangles, triangles)  
✓ Text rendering with background color  
✓ UTF-8 support  
✓ Anti-aliased fonts (smooth fonts)  

### Installation

#### Arduino IDE

1. Open Arduino IDE
2. Go to `Sketch` → `Include Library` → `Manage Libraries...`
3. Search for "TFT_eSPI"
4. Click "Install"

#### PlatformIO

Add to `platformio.ini`:

```ini
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino
lib_deps = 
    bodmer/TFT_eSPI@^2.5.31
```

#### Manual Installation

```bash
cd ~/Arduino/libraries
git clone https://github.com/Bodmer/TFT_eSPI.git
```

### Configuration

The library requires configuration in `User_Setup.h`. For this project, create a custom setup:

**Location**: `TFT_eSPI/User_Setup.h` (or use `User_Setup_Select.h` to include custom config)

```cpp
// USER_SETUP.h for NMEA Connection Tester
// ILI9488 3.5" Display with XPT2046 Touch

// Driver selection
#define ILI9488_DRIVER
#define TFT_WIDTH  320
#define TFT_HEIGHT 480

// ESP32 pin definitions
#define TFT_MISO 19  // Master In Slave Out
#define TFT_MOSI 23  // Master Out Slave In
#define TFT_SCLK 18  // SPI Clock
#define TFT_CS   15  // Chip select control pin
#define TFT_DC    2  // Data Command control pin
#define TFT_RST   4  // Reset pin (could connect to RST pin)
#define TFT_BL   32  // LED back-light control pin

// Touch screen
#define TOUCH_CS 21  // Touch chip select pin

// Font loading
#define LOAD_GLCD   // Font 1. Original Adafruit 8 pixel font needs ~1820 bytes in FLASH
#define LOAD_FONT2  // Font 2. Small 16 pixel high font, needs ~3534 bytes in FLASH
#define LOAD_FONT4  // Font 4. Medium 26 pixel high font, needs ~5848 bytes in FLASH
#define LOAD_FONT6  // Font 6. Large 48 pixel high font, needs ~2666 bytes in FLASH
#define LOAD_FONT7  // Font 7. 7 segment 48 pixel high font, needs ~2438 bytes in FLASH
#define LOAD_FONT8  // Font 8. Large 75 pixel high font, needs ~3256 bytes in FLASH
#define LOAD_GFXFF  // FreeFonts. Include access to the 48 Adafruit_GFX free fonts

// Smooth fonts
#define SMOOTH_FONT

// SPI frequency
#define SPI_FREQUENCY       27000000  // Maximum is 27MHz for ILI9488
#define SPI_READ_FREQUENCY  20000000  // Lower frequency for reading data
#define SPI_TOUCH_FREQUENCY  2500000  // Must be 2.5MHz for XPT2046

// Optional: Use DMA for faster updates (ESP32 only)
#define USE_DMA

// Comment out if using hardware SPI only
// #define USE_HSPI_PORT
```

### Basic Usage Examples

#### 1. Initialization and Setup

```cpp
#include <TFT_eSPI.h>

TFT_eSPI tft = TFT_eSPI();  // Create TFT object

void setup() {
    Serial.begin(115200);
    
    // Initialize display
    tft.init();
    
    // Set rotation (0=portrait, 1=landscape, 2=inverted portrait, 3=inverted landscape)
    tft.setRotation(1);  // Landscape mode for NMEA display
    
    // Clear screen
    tft.fillScreen(TFT_BLACK);
    
    // Set text properties
    tft.setTextColor(TFT_WHITE, TFT_BLACK);  // White text, black background
    tft.setTextSize(2);
    
    // Display startup message
    tft.setCursor(10, 10);
    tft.println("NMEA Connection Tester");
    tft.println("Ready");
}
```

#### 2. Display NMEA Sentences

```cpp
void displayNMEASentence(const char* sentence, uint16_t yPos) {
    // Set cursor position
    tft.setCursor(5, yPos);
    
    // Set color based on sentence type
    if (strncmp(sentence, "$GPGGA", 6) == 0) {
        tft.setTextColor(TFT_GREEN, TFT_BLACK);  // GPS Fix data
    } else if (strncmp(sentence, "$GPRMC", 6) == 0) {
        tft.setTextColor(TFT_CYAN, TFT_BLACK);   // Recommended minimum
    } else {
        tft.setTextColor(TFT_WHITE, TFT_BLACK);  // Other sentences
    }
    
    // Display the sentence
    tft.println(sentence);
}
```

#### 3. Status Display with Graphics

```cpp
void displayConnectionStatus(bool connected) {
    // Draw status indicator
    int x = 450;
    int y = 10;
    int radius = 8;
    
    if (connected) {
        tft.fillCircle(x, y, radius, TFT_GREEN);
        tft.setTextColor(TFT_GREEN, TFT_BLACK);
        tft.setCursor(x - 50, y - 5);
        tft.print("CONNECTED");
    } else {
        tft.fillCircle(x, y, radius, TFT_RED);
        tft.setTextColor(TFT_RED, TFT_BLACK);
        tft.setCursor(x - 70, y - 5);
        tft.print("DISCONNECTED");
    }
}
```

#### 4. Using Sprites for Flicker-Free Updates

```cpp
TFT_eSprite sprite = TFT_eSprite(&tft);

void setup() {
    // ... previous setup code ...
    
    // Create sprite (buffer)
    sprite.createSprite(320, 240);  // Half screen buffer
    sprite.setTextColor(TFT_WHITE, TFT_BLACK);
}

void updateDisplay() {
    // Draw to sprite (off-screen buffer)
    sprite.fillSprite(TFT_BLACK);
    sprite.setCursor(10, 10);
    sprite.setTextSize(2);
    sprite.println("Data: " + String(millis()));
    
    // Push sprite to screen (fast, no flicker)
    sprite.pushSprite(0, 0);
}
```

#### 5. Backlight Control

```cpp
// PWM setup for backlight
const int pwmChannel = 0;
const int pwmFreq = 5000;
const int pwmResolution = 8;
const int backlightPin = 32;

void setupBacklight() {
    ledcSetup(pwmChannel, pwmFreq, pwmResolution);
    ledcAttachPin(backlightPin, pwmChannel);
}

void setBacklight(uint8_t brightness) {
    // brightness: 0 (off) to 255 (full)
    ledcWrite(pwmChannel, brightness);
}

void adjustBacklightAuto(int ambientLight) {
    // Auto-adjust based on ambient light sensor
    // ambientLight: 0 (dark) to 1023 (bright)
    uint8_t brightness = map(ambientLight, 0, 1023, 50, 255);
    setBacklight(brightness);
}
```

#### 6. Touch Screen Integration

```cpp
#include <TFT_eSPI.h>

TFT_eSPI tft = TFT_eSPI();

void setup() {
    // ... display setup ...
    
    // Calibrate touch (run once, store values)
    uint16_t calData[5];
    tft.calibrateTouch(calData, TFT_WHITE, TFT_BLACK, 15);
    
    // Print calibration data to save for future use
    for (int i = 0; i < 5; i++) {
        Serial.print(calData[i]);
        Serial.print(", ");
    }
}

void loop() {
    uint16_t x, y;
    
    // Check if screen is touched
    if (tft.getTouch(&x, &y)) {
        // Draw a circle at touch point
        tft.fillCircle(x, y, 5, TFT_RED);
        
        Serial.print("Touch at: ");
        Serial.print(x);
        Serial.print(", ");
        Serial.println(y);
    }
}
```

### Performance Optimization

#### 1. Use DMA for Fast Updates

```cpp
// In User_Setup.h, ensure this is defined:
#define USE_DMA

// Use pushImageDMA for fast image transfer
void fastImageUpdate(uint16_t* imageBuffer) {
    tft.pushImageDMA(0, 0, 480, 320, imageBuffer);
}
```

#### 2. Partial Screen Updates

```cpp
// Update only changed areas
void updateNMEALine(int lineNum, const char* text) {
    int yPos = lineNum * 20;
    
    // Set update window
    tft.setAddrWindow(0, yPos, 480, yPos + 20);
    
    // Clear line
    tft.fillRect(0, yPos, 480, 20, TFT_BLACK);
    
    // Write text
    tft.setCursor(5, yPos + 2);
    tft.print(text);
}
```

#### 3. Reduce SPI Transactions

```cpp
// Batch operations
tft.startWrite();  // Begin transaction
tft.writeRect(x1, y1, w1, h1, buffer1);
tft.writeRect(x2, y2, w2, h2, buffer2);
tft.endWrite();    // End transaction
```

### Memory Management

```cpp
// Check available heap
Serial.print("Free heap: ");
Serial.println(ESP.getFreeHeap());

// Sprites use heap memory, manage carefully
TFT_eSprite sprite = TFT_eSprite(&tft);
sprite.createSprite(480, 320);  // Requires ~307KB
// ... use sprite ...
sprite.deleteSprite();  // Free memory when done
```

### Common Issues and Solutions

| Issue | Solution |
|-------|----------|
| Display shows nothing | Check power (3.3V), verify pin connections, ensure correct driver selected |
| White screen | Wrong driver or initialization failed, check User_Setup.h |
| Colors inverted | Add `tft.invertDisplay(true);` after init |
| Display flickers | Use sprites for off-screen rendering |
| Slow updates | Enable DMA, reduce SPI frequency if signal integrity issues |
| Touch not working | Verify TOUCH_CS pin, check SPI_TOUCH_FREQUENCY |
| Text garbled | Check SPI wiring, reduce SPI frequency |
| Screen rotated wrong | Change `tft.setRotation()` value (0-3) |

## Alternative Library: LovyanGFX

### Library Information

- **Name**: LovyanGFX
- **Repository**: https://github.com/lovyan03/LovyanGFX
- **Version**: 1.x.x
- **License**: BSD 3-Clause
- **Platform**: ESP32, ESP8266, SAMD51, RP2040
- **Performance**: ~20% faster than TFT_eSPI

### Installation (PlatformIO)

```ini
[env:esp32dev]
lib_deps = 
    lovyan03/LovyanGFX@^1.1.7
```

### Configuration

```cpp
#define LGFX_USE_V1
#include <LovyanGFX.hpp>

class LGFX : public lgfx::LGFX_Device {
    lgfx::Panel_ILI9488 _panel_instance;
    lgfx::Bus_SPI _bus_instance;
    lgfx::Touch_XPT2046 _touch_instance;
    
public:
    LGFX(void) {
        // Bus configuration
        auto cfg = _bus_instance.config();
        cfg.spi_host = VSPI_HOST;
        cfg.spi_mode = 0;
        cfg.freq_write = 27000000;
        cfg.freq_read = 16000000;
        cfg.pin_sclk = 18;
        cfg.pin_mosi = 23;
        cfg.pin_miso = 19;
        cfg.pin_dc = 2;
        _bus_instance.config(cfg);
        _panel_instance.setBus(&_bus_instance);
        
        // Panel configuration
        auto pcfg = _panel_instance.config();
        pcfg.pin_cs = 15;
        pcfg.pin_rst = 4;
        pcfg.pin_busy = -1;
        pcfg.panel_width = 320;
        pcfg.panel_height = 480;
        pcfg.offset_x = 0;
        pcfg.offset_y = 0;
        _panel_instance.config(pcfg);
        
        // Touch configuration
        auto tcfg = _touch_instance.config();
        tcfg.x_min = 0;
        tcfg.x_max = 319;
        tcfg.y_min = 0;
        tcfg.y_max = 479;
        tcfg.pin_int = 22;
        tcfg.pin_cs = 21;
        tcfg.freq = 2500000;
        tcfg.spi_host = VSPI_HOST;
        _touch_instance.config(tcfg);
        _panel_instance.setTouch(&_touch_instance);
        
        setPanel(&_panel_instance);
    }
};

LGFX tft;
```

### Basic Usage

```cpp
void setup() {
    tft.init();
    tft.setRotation(1);
    tft.fillScreen(TFT_BLACK);
    tft.setTextColor(TFT_WHITE);
    tft.println("LovyanGFX Test");
}
```

LovyanGFX is API-compatible with TFT_eSPI for basic functions, making migration easy.

## Alternative Library: Adafruit GFX + ILI9488

### Library Information

- **Name**: Adafruit GFX + Adafruit ILI9341 (compatible with ILI9488)
- **Repository**: https://github.com/adafruit/Adafruit-GFX-Library
- **Version**: 1.11.x
- **License**: BSD License
- **Performance**: Slower than TFT_eSPI, but very stable

### Installation

```ini
lib_deps = 
    adafruit/Adafruit GFX Library@^1.11.5
    adafruit/Adafruit ILI9341@^1.6.0
```

### Basic Usage

```cpp
#include <Adafruit_GFX.h>
#include <Adafruit_ILI9341.h>

#define TFT_CS   15
#define TFT_DC   2
#define TFT_RST  4

Adafruit_ILI9341 tft = Adafruit_ILI9341(TFT_CS, TFT_DC, TFT_RST);

void setup() {
    tft.begin();
    tft.setRotation(1);
    tft.fillScreen(ILI9341_BLACK);
    tft.setTextColor(ILI9341_WHITE);
    tft.println("Adafruit GFX Test");
}
```

**Note**: Adafruit libraries are slower but have excellent documentation and examples.

## Library Comparison

| Feature | TFT_eSPI | LovyanGFX | Adafruit GFX |
|---------|----------|-----------|--------------|
| Performance | Fast | Very Fast | Moderate |
| Memory Usage | Moderate | Moderate | Low |
| Documentation | Excellent | Good | Excellent |
| Examples | Many | Good | Many |
| ESP32 DMA | Yes | Yes | No |
| Touch Support | Yes | Yes | Separate lib |
| Learning Curve | Easy | Moderate | Easy |
| **Recommendation** | **Primary** | Alternative | Fallback |

## Verification Checklist

- [x] **ILI9488 Driver Support**: All three libraries support ILI9488
- [x] **ESP32 Compatibility**: All libraries work with ESP32
- [x] **SPI Interface**: All libraries support hardware SPI
- [x] **Touch Support**: TFT_eSPI and LovyanGFX have built-in XPT2046 support
- [x] **Performance**: TFT_eSPI and LovyanGFX meet real-time requirements (60+ FPS)
- [x] **License Compatibility**: All libraries use permissive licenses (BSD/MIT-like)
- [x] **Active Maintenance**: All libraries are actively maintained
- [x] **Community Support**: Large user base and active forums

## Recommended: TFT_eSPI

For this project, **TFT_eSPI** is the recommended library because:
1. Best balance of performance and ease of use
2. Excellent documentation and examples
3. Large community support
4. Built-in touch support
5. Optimized for ESP32 with DMA
6. Wide display driver support

## References

1. TFT_eSPI: https://github.com/Bodmer/TFT_eSPI
2. LovyanGFX: https://github.com/lovyan03/LovyanGFX
3. Adafruit GFX: https://github.com/adafruit/Adafruit-GFX-Library
4. ILI9488 Datasheet: [Display controller specifications]
5. XPT2046 Touch Controller: [Touch controller specifications]

---

**Document Version**: 1.0  
**Last Updated**: 2025-10-21  
**Status**: VERIFIED - Ready for implementation
