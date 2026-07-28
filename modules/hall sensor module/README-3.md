# Hall Effect Sensor Module

The **Hall Effect Sensor Module** by **AMlab** detects magnetic fields (unipolar/single-pole detection) for escape room props and puzzles. It is commonly used for detecting hidden magnets, key placement, drawer/door positioning, or magnet-based physical puzzles.

It connects to an **AMlab Main Control Module MIDI** or **Main Control Module MEGA** using a standard RJ45 Ethernet patch cable. The module features an onboard status LED that provides visual diagnostic feedback: the LED is **illuminated when idle** and **goes dark when a magnetic field is detected**.

---

## Technical Specifications

| Parameter | Specification |
| :--- | :--- |
| **Manufacturer** | AMlab |
| **Sensor Type** | Hall Effect Sensor (Unipolar - detects single magnetic pole) |
| **Recommended Magnet** | **Neodymium (N35 or higher)** magnet recommended |
| **Sensing Range** | Dependent on magnet size and strength (larger/stronger magnets increase range) |
| **Output Channels** | 1 Digital Signal Line (carried over RJ45 Ethernet cable) |
| **Output Logic** | Active LOW (HIGH when idle, LOW when magnet detected) |
| **Status Indicator** | Onboard LED (ON = Idle / No magnet, OFF = Magnet detected) |
| **Supply Voltage** | 12V / 24V DC (Supplied via RJ45 cable from Main Controller) |
| **Connection to Main Controller** | Standard RJ45 Ethernet Port (CAT5 / CAT6 cable) |
| **Extra Power Required?** | No (Fully powered via RJ45) |

---

## Magnet Selection & Sensing Distance

For optimal performance in prop design, please consider the following magnetic property guidelines:

* **Recommended Magnet Type:** **Neodymium (Rare Earth) magnets** are strongly recommended over standard ferrite/ceramic magnets due to their significantly stronger magnetic flux density.
* **Sensing Distance vs. Magnet Size:** The detection distance directly depends on the **size, thickness, and strength grade** of the magnet:
  * **Small Neodymium Magnets (e.g., 5mm x 2mm):** Trigger range ~3mm to 8mm.
  * **Medium Neodymium Magnets (e.g., 10mm x 3mm):** Trigger range ~10mm to 20mm.
  * **Large Neodymium Magnets (e.g., 20mm x 5mm+):** Trigger range up to 30mm+ (ideal for sensing through wood panels or acrylic sheets).
* **Pole Orientation:** The module features a **unipolar** Hall effect sensor. It responds to only **one specific magnetic pole** (North or South). If the sensor does not trigger when bringing the magnet close, simply flip the magnet 180 degrees.

---

## Wiring Instructions

### 1. Sensor Placement

Position the module where players will place or move a magnet (e.g., behind a wall panel, inside a lockbox, or under a tabletop). 

> **Note:** Ensure the non-conductive barrier (wood, plastic, acrylic) between the module and the player prop matches the detection range provided by your chosen magnet size.

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
| Pin 3   | GPIO 1 (SIGNAL)    | Digital Input Signal Line    |
| Pin 4   | N/C                | Not Connected                |
| Pin 5   | N/C                | Not Connected                |
| Pin 6   | N/C                | Not Connected                |
| Pin 7   | GND                | Ground                       |
| Pin 8   | GND                | Ground                       |
+---------+--------------------+------------------------------+
```

---

## Programming & Logic

The signal output is **Active LOW**:

* **Idle (No Magnet):** Output = `HIGH` (Status LED is **ON**)
* **Magnet Detected:** Output = `LOW` (Status LED is **OFF**)

### Option A: Arduino / C++ Example

```cpp
// Define the GPIO pin connected to the Hall Sensor signal line
const int HALL_SENSOR_PIN = 2; // Adjust pin number according to your Main Control Module port layout

void setup() {
  Serial.begin(115200);
  pinMode(HALL_SENSOR_PIN, INPUT);
  
  Serial.println("Hall Effect Sensor Module Initialized.");
}

void loop() {
  // Read sensor state (LOW = Magnet Detected)
  bool magnet_detected = (digitalRead(HALL_SENSOR_PIN) == LOW);

  if (magnet_detected) {
    Serial.println("Magnetic Field Detected! (LED OFF)");
  } else {
    Serial.println("Idle - No Magnet Detected. (LED ON)");
  }

  delay(200); // Check state 5 times per second
}
```

### Option B: MicroPython Example

```python
from machine import Pin
import time

# Define the GPIO pin connected to the Hall Sensor signal line
hall_sensor = Pin(2, Pin.IN) # Adjust pin number for your Main Control Module

print("Hall Effect Sensor Module Initialized.")

while True:
    # Read sensor (0 / False = Magnet Detected)
    magnet_detected = (hall_sensor.value() == 0)

    if magnet_detected:
        print("Status: MAGNET DETECTED (LED OFF)")
    else:
        print("Status: IDLE (LED ON)")

    time.sleep(0.2)
```

---

## Troubleshooting

| Symptom | Probable Cause | Recommended Action |
| :--- | :--- | :--- |
| **LED stays ON when magnet is placed nearby** | Wrong magnetic pole facing sensor, or magnet too small/weak. | Flip the magnet 180 degrees (unipolar sensors respond to only one pole). Use a larger Neodymium magnet to increase detection range. |
| **LED remains dark continuously without magnet** | RJ45 cable disconnected or power issue. | Check Ethernet cable connection and verify that the Main Controller port is supplying power. |
| **Main controller reads incorrect state** | Software pin mismatch or unhandled logic. | Verify logic in code: sensor outputs **LOW** when triggered, **HIGH** when idle. Check controller GPIO pin mapping. |
