# PIR Motion Sensor Module

The **PIR Motion Sensor Module** by **AMlab** is a high-sensitivity infrared motion detection module engineered for escape rooms, interactive props, and automated prop triggering.

Built around the premium industrial-grade **Panasonic EKMC1601113** Passive Infrared (PIR) sensor, this module delivers exceptional motion detection precision, low noise immunity, and reliable performance without false triggers.

It connects to an **AMlab Main Control Module MIDI** or **Main Control Module MEGA** using a standard RJ45 Ethernet patch cable. An onboard status LED gives immediate visual diagnostic feedback when motion is detected.

---

## Key Features

* **Panasonic EKMC1601113 Sensor Core:** Premium, highly reliable PIR sensor with integrated digital driver circuit and superior digital noise rejection.
* **Onboard Pulldown Resistor:** Includes a dedicated **51 kΩ pulldown resistor** on the output signal line (`Pin 2`) to guarantee clean, stable `LOW` logic levels during idle states and prevent floating signal states.
* **Plug-and-Play RJ45 Connection:** Connects directly to AMlab Main Controller extension ports via standard CAT5/CAT6 patch cables.
* **Status LED Indicator:** Provides immediate visual diagnostic feedback on motion detection without requiring software monitoring.

---

## Technical Specifications

| Parameter | Specification |
| :--- | :--- |
| **Manufacturer** | AMlab |
| **PIR Sensor Element** | Panasonic EKMC1601113 |
| **Detection Type** | Passive Infrared (PIR) Motion Detection |
| **Output Signal Pin** | `Pin 2` |
| **Pull-down Resistor** | 51 kΩ onboard pulldown resistor on `Pin 2` |
| **Output Logic** | Active HIGH (3.3V DC digital signal output) |
| **Supply Voltage** | 12V / 24V DC (Supplied via RJ45 cable from Main Controller) |
| **Connection to Main Controller** | Standard RJ45 Ethernet Port (CAT5 / CAT6 cable) |
| **Extra Power Required?** | No (Fully powered via RJ45) |

---

## Wiring Instructions

### 1. Module Alignment & Placement

Mount the PIR sensor facing the area where player movement should be detected (e.g., entryway, secret passage, or in front of an interactive puzzle panel).

> **Note:** Keep the sensor away from direct thermal sources (such as heating vents, strong incandescent bulbs, or direct sunlight) to maintain optimal detection accuracy.

### 2. Module to Main Controller Connection (RJ45)

Connect a standard RJ45 Ethernet patch cable from the module's RJ45 port directly into one of the extension ports on your **Main Control Module MIDI** or **Main Control Module MEGA**.

#### RJ45 Pinout Allocation

```
+-------------------------------------------------------------+
|                     RJ45 Cable Pinout                       |
+---------+--------------------+------------------------------+
| Pin     | Signal             | Description                  |
+---------+--------------------+------------------------------+
| Pin 1   | VCC                | Power (+12V / +24V DC)       |
| Pin 2   | VCC                | Power (+12V / +24V DC)       |
| Pin 3   | Pin 2 (SIGNAL)     | PIR Motion Signal Output     |
| Pin 4   | N/C                | Not Connected                |
| Pin 5   | N/C                | Not Connected                |
| Pin 6   | N/C                | Not Connected                |
| Pin 7   | GND                | Ground                       |
| Pin 8   | GND                | Ground                       |
+---------+--------------------+------------------------------+
```

---

## Operating Logic & Programming

The Panasonic EKMC1601113 outputs an **Active HIGH** signal on `Pin 2`:

* **Idle (No Motion):** Signal = `LOW` (Pulled down to 0V via onboard **51 kΩ resistor**)
* **Motion Detected:** Signal = `HIGH` (3.3V DC signal pulse)

### Option A: Arduino / C++ Example

```cpp
// Define signal pin connected to PIR Motion Module
const int PIR_SIGNAL_PIN = 2; // Pin 2 on RJ45 port layout

void setup() {
  Serial.begin(115200);
  pinMode(PIR_SIGNAL_PIN, INPUT); // Onboard 51k pulldown keeps pin LOW when idle
  
  Serial.println("AMlab PIR Motion Sensor Initialized.");
}

void loop() {
  // Read PIR signal state (HIGH = Motion Detected)
  bool motion_detected = (digitalRead(PIR_SIGNAL_PIN) == HIGH);

  if (motion_detected) {
    Serial.println("[ALERT] Motion Detected!");
  } else {
    Serial.println("Area Idle - No Motion.");
  }

  delay(200); // Poll state 5 times per second
}
```

### Option B: MicroPython Example

```python
from machine import Pin
import time

# Define signal pin connected to PIR Motion Module
pir_sensor = Pin(2, Pin.IN) # Pin 2 on Main Controller port

print("AMlab PIR Motion Sensor Initialized.")

while True:
    # Read PIR signal state (True / 1 = Motion Detected)
    if pir_sensor.value() == 1:
        print("[ALERT] Motion Detected!")
    else:
        print("Area Idle - No Motion.")

    time.sleep(0.2)
```

---

## Troubleshooting

| Symptom | Probable Cause | Recommended Action |
| :--- | :--- | :--- |
| **No motion triggers detected** | RJ45 cable disconnected or incorrect port used. | Check RJ45 connection and verify controller port power supply. |
| **False positive / Continuous triggers** | Thermal interference near sensor (heaters, sunlight, hot electronics). | Reposition sensor away from direct heat sources and drafty vents. |
| **Floating signal state in code** | Damaged 51 kΩ pulldown resistor or missing ground. | Ensure ground line on RJ45 cable is secure. The onboard 51 kΩ resistor holds logic `LOW` when idle. |
