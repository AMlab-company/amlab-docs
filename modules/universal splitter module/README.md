# Universal RJ45 Signal Splitter Module

The **Universal RJ45 Signal Splitter Module** by **AMlab** is a passive signal distribution and routing PCB designed to maximize pin efficiency across escape rooms, modular props, and sensor networks.

When connecting simple single-signal modules (such as Hall effect sensors, reed switches, or buttons) directly to the Main Control Module via RJ45, three available signal lines on that port remain unused. The Universal Splitter solves this problem by allowing up to **4 single-signal modules** (or custom signal groupings) to share a **single RJ45 cable** back to the Main Control Module.

---

## Key Features

* **1-to-4 RJ45 Port Distribution:**
  * **1x Host Port ('Port'):** Connects directly to the Main Control Module.
  * **4x Ports 1–4 (Port 1 – Port 4):** Connects to up to 4 individual sensor or actuator modules.
* **0-Ohm Jumper Pad Matrix:** Built-in SMD `0000` / `0-Ohm` resistor pads allow completely customizable hardware signal routing between the 4 Ports 1–4 and the 4 signal lines of the Main I/O port.
* **Shared Power Rails:** Automatically passes standard power (`VCC` on Pin 1, `GND` on Pins 2, 7, and 8) to all 4 Ports 1–4, ensuring all connected sensors receive system power without extra wiring.
* **Zero Latency & Passive Design:** Requires no firmware or microcontroller—pure hardware signal routing.

---

## Technical Specifications

| Parameter | Specification |
| :--- | :--- |
| **Manufacturer** | AMlab |
| **Host Connection** | 1x Standard RJ45 Port (To Main Controller) |
| **Peripheral Connections** | 4x Standard RJ45 Ports (To Peripheral Modules) |
| **Max Voltage / Current** | **30V DC / 3A Max** (Passed through from Main Power Bus) |
| **Routing Mechanism** | Onboard SMD 0-Ohm Resistor Jumpers (0805 / 0603 footprint) |
| **Supported Signal Lines** | 4 Dedicated Signal Lines (Pins 3, 4, 5, 6) + VCC/GND |

---

## How It Works & Resistor Configuration Examples

By default or via soldering SMD 0-Ohm resistors (or bridge wires) onto designated solder pads, you can route specific signal lines from each of the 4 Ports 1–4 to dedicated signal lines on the Main I/O port.

### Example 1: 4x Single-Signal Sensor Aggregation (e.g., 4x Hall Sensors)

In a typical setup with 4 Hall effect sensors where each sensor uses **Signal 1 (RJ45 Pin 3)**:

* Solder **Resistor 1:** Connects **Port 1 (Signal 1)** $
ightarrow$ **Port (Signal 1 / Pin 3)**
* Solder **Resistor 2:** Connects **Port 2 (Signal 1)** $
ightarrow$ **Port (Signal 2 / Pin 4)**
* Solder **Resistor 3:** Connects **Port 3 (Signal 1)** $
ightarrow$ **Port (Signal 3 / Pin 5)**
* Solder **Resistor 4:** Connects **Port 4 (Signal 1)** $
ightarrow$ **Port (Signal 4 / Pin 6)**

> **Result:** All 4 Hall sensors receive power from the splitter, and the Main Control Module reads all 4 sensor states over a single CAT5/CAT6 cable!

---

## RJ45 Pinout & Routing Architecture

Standardized pinout across the host and Ports 1–4:

```
+-------------------------------------------------------------------------------+
|                        Universal Splitter Routing                             |
+---------+--------------------+------------------------------------------------+
| Pin     | Port      | Ports 1–4 (Port 1 - Port 4 Default)      |
+---------+--------------------+------------------------------------------------+
| Pin 1   | VCC (System Power) | Shared VCC across all 4 Ports                  |
| Pin 2   | GND (Ground)       | Shared GND across all 4 Ports                  |
| Pin 3   | Signal 1           | Configurable via 0-Ohm Resistor Pad Matrix     |
| Pin 4   | Signal 2           | Configurable via 0-Ohm Resistor Pad Matrix     |
| Pin 5   | Signal 3           | Configurable via 0-Ohm Resistor Pad Matrix     |
| Pin 6   | Signal 4           | Configurable via 0-Ohm Resistor Pad Matrix     |
| Pin 7   | GND                | Shared GND across all 4 Ports                  |
| Pin 8   | GND                | Shared GND across all 4 Ports                  |
+---------+--------------------+------------------------------------------------+
```

---

## Setup & Soldering Guide

1. **Identify Your Modules:** Determine which signal line each module on Ports 1–4 uses (e.g., Pin 3 / Signal 1).
2. **Select Routing Scheme:** Choose which signal on the Main I/O port (Signals 1–4) should correspond to each satellite port.
3. **Solder 0-Ohm Resistors:**
   * Solder 0-Ohm SMD resistors (or bridge with a small solder blob/wire) across the appropriate pad pairs on the board.
4. **Connect Cables:**
   * Plug the **Port** into the Main Control Module.
   * Plug **Ports 1–4 1–4** into your sensors or actuators.

---

## Programming Example (Main Control Module)

Reading 4 Hall sensors connected through the Universal Splitter Module:

```cpp
// Signal lines from the Splitter Module on the Main Control Port
const int HALL_SENSOR_1_PIN = 3; // Signal 1 (Port 1)
const int HALL_SENSOR_2_PIN = 4; // Signal 2 (Port 2)
const int HALL_SENSOR_3_PIN = 5; // Signal 3 (Port 3)
const int HALL_SENSOR_4_PIN = 4; // Signal 4 (Port 4)

void setup() {
  pinMode(HALL_SENSOR_1_PIN, INPUT_PULLUP);
  pinMode(HALL_SENSOR_2_PIN, INPUT_PULLUP);
  pinMode(HALL_SENSOR_3_PIN, INPUT_PULLUP);
  pinMode(HALL_SENSOR_4_PIN, INPUT_PULLUP);
  
  Serial.begin(115200);
}

void loop() {
  bool hall1 = digitalRead(HALL_SENSOR_1_PIN) == LOW;
  bool hall2 = digitalRead(HALL_SENSOR_2_PIN) == LOW;
  bool hall3 = digitalRead(HALL_SENSOR_3_PIN) == LOW;
  bool hall4 = digitalRead(HALL_SENSOR_4_PIN) == LOW;

  if (hall1 && hall2 && hall3 && hall4) {
    Serial.println("PUZZLE SOLVED: All 4 Hall sensors triggered!");
  }
  
  delay(100);
}
```

---

## Troubleshooting

| Symptom | Probable Cause | Recommended Action |
| :--- | :--- | :--- |
| **Connected sensor receives no power** | Main I/O port cable disconnected or power rail issue. | Verify CAT5/6 cable between Main Controller and Main I/O port. |
| **Sensor triggers wrong signal on Main Controller** | 0-Ohm resistor soldered to incorrect pad. | Inspect PCB resistor matrix and verify routing configuration. |
| **Signal crosstalk or floating reads** | Unsoldered/floating resistor pads or missing pull-up/pull-down. | Ensure 0-Ohm resistors make solid contact and enable internal pin pull-ups in microcontroller code. |
