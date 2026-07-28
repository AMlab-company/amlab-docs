# Servo & Addressable LED Module

The **Servo & Addressable LED Module** by **AMlab** is a high-performance expansion board designed to drive precision **servo motors** (SG90, MG996R, etc.) and digital **addressable LED strips** (WS2812B, SK6812, Neopixels) in escape room props and automated mechanical puzzles.

Controlled via an **AMlab Main Control Module MIDI** or **Main Control Module MEGA** over a standard RJ45 Ethernet patch cable, this module features high-speed **3.3V to 5V signal buffering** and a flexible 3-way power management architecture.

---

## Key Features

* **High-Quality Signal Buffering:** Onboard active logic buffer converts 3.3V GPIO control signals from the Raspberry Pi Pico into clean, high-speed **5V digital logic signals**—ensuring glitch-free addressable LED communication and reliable servo PWM control.
* **Dual Output Modes:**
  * **Addressable LED Controller:** Native support for WS2812B, WS2811, SK6812, APA102, and standard Neopixel LED strips/rings.
  * **Servo Motor Driver:** Standard 3-pin output configuration for RC servo control (`Pin 1` through `Pin 4`).
* **3-Pin Terminal Blocks (Per Channel):** Each output channel includes a dedicated 3-pin connector exposing:
  * `PWR` (Selected Power Line: Ethernet VCC / Regulated 5V / External VCC)
  * `SIG` (Buffered 5V Control Signal)
  * `GND` (Power Ground return)
* **3-Way Power Source Selection Jumper (`PWR`):**
  * **ETHERNET:** Power derived directly from RJ45 system power.
  * **5V:** Onboard regulated **5V DC supply (2A Max)**.
  * **VCC_EXT:** High-current **External Power Connector (3A Max)** for demanding servo or high-density LED loads.

---

## Technical Specifications

| Parameter | Specification |
| :--- | :--- |
| **Manufacturer** | AMlab |
| **Compatible Peripherals** | Addressable LEDs (WS2812B, SK6812), RC Servos (SG90, MG90S, MG996R) |
| **Signal Translation** | Onboard active buffer (**3.3V Logic Input -> 5V Logic Output**) |
| **Terminal Connections** | 3-Pin Terminals per channel (`PWR`, `SIG`, `GND`) |
| **Power Selection Options** | 3-Position Jumper: **ETHERNET** / **5V (2A Max)** / **VCC_EXT (3A Max)** |
| **Max Regulated 5V Current** | 2A |
| **Max External Terminal Current** | 3A |
| **Control Signal Pins** | `Pin 1` to `Pin 4` (from Raspberry Pi Pico via RJ45) |
| **Connection to Main Controller** | Standard RJ45 Ethernet Port (CAT5 / CAT6 cable) |

---

## Power Source Configuration Jumper

The onboard power selector jumper allows you to optimize power delivery depending on connected loads:

| Jumper Setting | Power Source | Max Current | Recommended Applications |
| :--- | :--- | :--- | :--- |
| **ETHERNET** | RJ45 Cable Supply | Varies | Small servos, basic diagnostic indicator LEDs |
| **5V** | Regulated Onboard 5V | **2A Max** | Standard WS2812B LED rings, 1–2 micro servos (SG90) |
| **VCC_EXT** | External `VCC_EXT` Terminal | **3A Max** | High-density LED strips, multiple heavy-duty servos (MG996R) |

---

## 3-Pin Terminal Block Pinout

```
+-------------------------------------------------------------+
|                3-Pin Output Terminal Pinout                 |
+---------+--------------------+------------------------------+
| Terminal| Function           | Description                  |
+---------+--------------------+------------------------------+
| PWR     | Power Line         | Selected power (Ethernet/5V/EXT)|
| SIG     | Signal             | Buffered 5V Logic Output     |
| GND     | Ground             | Power & Logic Ground Return  |
+---------+--------------------+------------------------------+
```

---

## Control Logic & RJ45 Signal Mapping

The Raspberry Pi Pico sends 3.3V logic signals across the RJ45 cable, which are immediately buffered to **5V logic** on the module for optimal signal integrity over longer distances.

```
+-------------------------------------------------------------+
|                     RJ45 Cable Pinout                       |
+---------+--------------------+------------------------------+
| Pin     | Signal             | Target Output                |
+---------+--------------------+------------------------------+
| Pin 1   | VCC                | System Power                 |
| Pin 2   | Pin 1              | Output Channel 1 (Buffered)  |
| Pin 3   | Pin 2              | Output Channel 2 (Buffered)  |
| Pin 4   | Pin 3              | Output Channel 3 (Buffered)  |
| Pin 5   | Pin 4              | Output Channel 4 (Buffered)  |
| Pin 6   | N/C                | Not Connected                |
| Pin 7   | GND                | Ground                       |
| Pin 8   | GND                | Ground                       |
+---------+--------------------+------------------------------+
```

---

## Programming & Software Control Examples

### Example 1: Driving WS2812B Addressable LEDs (FastLED / Adafruit NeoPixel)

```cpp
#include <Adafruit_NeoPixel.h>

#define LED_PIN    2    // Pin 1 on RJ45 port layout
#define NUM_LEDS   16

Adafruit_NeoPixel strip(NUM_LEDS, LED_PIN, NEO_GRB + NEO_KHZ800);

void setup() {
  strip.begin();
  strip.show(); // Initialize all pixels to 'off'
  strip.setBrightness(50);
}

void loop() {
  // Rainbow cycle or solid color effect
  for(int i=0; i<NUM_LEDS; i++) {
    strip.setPixelColor(i, strip.Color(255, 0, 0)); // Green
  }
  strip.show();
  delay(1000);

  for(int i=0; i<NUM_LEDS; i++) {
    strip.setPixelColor(i, strip.Color(0, 255, 0)); // Red
  }
  strip.show();
  delay(1000);
}
```

### Example 2: Driving RC Servo Motors

```cpp
#include <Servo.h>

Servo myServo;
const int SERVO_PIN = 2; // Pin 1 on RJ45 port layout

void setup() {
  myServo.attach(SERVO_PIN);
}

void loop() {
  // Move servo to 0 degrees
  myServo.write(0);
  delay(1000);

  // Move servo to 90 degrees
  myServo.write(90);
  delay(1000);

  // Move servo to 180 degrees
  myServo.write(180);
  delay(1000);
}
```

---

## Troubleshooting

| Symptom | Probable Cause | Recommended Action |
| :--- | :--- | :--- |
| **Addressable LEDs flicker or display wrong colors** | Missing 3.3V to 5V buffer logic or inadequate ground return. | Confirm signal goes through the onboard buffer stage. Verify common ground line. |
| **Servo twitches or resets controller when moving** | Power draw exceeds power supply limit (e.g. 5V regulator overload). | Move power selection jumper to **VCC_EXT** and attach an external power supply capable of 3A+. |
| **LEDs stay dark** | Incorrect jumper position or blown fuse. | Ensure the `PWR` jumper is properly seated on **ETHERNET**, **5V**, or **VCC_EXT**. |
