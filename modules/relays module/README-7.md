# 4-Channel Relay Module

The **4-Channel Relay Module** by **AMlab** is a heavy-duty power switching expansion board designed to drive high-current loads—such as electromagnetic locks, solenoids, lights, sirens, motors, and prop actuators—in escape room environments.

Controlled via an **AMlab Main Control Module MIDI** or **Main Control Module MEGA** over a standard RJ45 Ethernet patch cable, the module provides **4 independent relays** with status LEDs and customizable power routing options.

---

## Key Features

* **4 Independent Relay Channels:** Switch high-voltage / high-current external circuits safely using digital signals (`Pin 1` through `Pin 4`).
* **5-Pin Terminal Block Per Relay:** Each channel features a dedicated screw terminal exposing:
  * `VIN` (Power distribution line for external loads)
  * `NO` (Normally Open contact)
  * `COM` (Common terminal)
  * `NC` (Normally Closed contact)
  * `GND` (Common Ground return)
* **Flexible Power Selection (`VIN` Jumper):**
  * **Ethernet Power Mode:** Route system power directly from the RJ45 Ethernet connection (6V–30V DC) to the `VIN` terminals.
  * **External Power Mode:** Isolate relay load power by feeding an external power supply into the dedicated external terminal.
* **Onboard Diagnostic LEDs:** Each relay channel includes a dedicated status LED that illuminates when the corresponding relay coil is energized / triggered.
* **MOSFET Driver Circuits:** Each relay coil is driven by an N-Channel MOSFET transistor directly triggered by 3.3V logic from the Raspberry Pi Pico.

---

## Technical Specifications

| Parameter | Specification |
| :--- | :--- |
| **Manufacturer** | AMlab |
| **Relay Count** | 4 Independent Relay Channels (`Relay 1` – `Relay 4`) |
| **Terminal Connections** | 5-Pin Screw Terminals per channel (`VIN`, `NO`, `COM`, `NC`, `GND`) |
| **Power Selection Jumper** | Selects `VIN` source: **RJ45 Ethernet VCC** vs. **External Supply** |
| **Control Signal Pins** | `Pin 1` (Relay 1), `Pin 2` (Relay 2), `Pin 3` (Relay 3), `Pin 4` (Relay 4) |
| **Status Indicators** | 4x Onboard Channel LEDs (ON when active / coil energized) |
| **Operating / Load Voltage** | 6V – 30V DC (3A Max) via `VIN` Power Terminal / RJ45 Jumper |
| **Relay Contact Rating** | Up to **10A @ 230V AC** (5A/10A DC depending on load) |
| **Connection to Main Controller** | Standard RJ45 Ethernet Port (CAT5 / CAT6 cable) |

---

## Terminal Block Pinout (Per Relay Channel)

Each of the 4 relay channels includes a 5-pin screw terminal block for versatile load wiring:

```
+-------------------------------------------------------------+
|               5-Pin Relay Terminal Block Pinout             |
+---------+--------------------+------------------------------+
| Terminal| Function           | Description                  |
+---------+--------------------+------------------------------+
| VIN     | Power Supply       | Selected power source (+V DC)|
| NO      | Normally Open      | Connected to COM when ON     |
| COM     | Common             | Load switching input/output  |
| NC      | Normally Closed    | Connected to COM when OFF    |
| GND     | Ground             | Power Ground return (0V)     |
+---------+--------------------+------------------------------+
```

---

## Power Configuration (VIN Jumper Settings)

The module includes a **Power Source Jumper** to select how power is delivered to the `VIN` terminals across all 4 relay output channels:

1. **Jumper Set to Ethernet (RJ45 VCC):**
   * Uses system power (6V–30V DC, up to 3A) supplied over the RJ45 cable.
   * Ideal for low-to-medium power loads (e.g., small 12V Maglocks, LEDs, low-current solenoids).
2. **Jumper Set to External Port:**
   * Disconnects `VIN` from the RJ45 power line and connects it to an external power supply terminal on the board.
   * Ideal for heavy-duty loads, high-current electromagnets, or when powering loads requiring a different operating voltage from the main controller.

---

## Control Logic & Pinout Allocation

The 4 relays are activated by driving digital signal lines `Pin 1` through `Pin 4`:

* **Logic LOW (0V):** Relay Coil DE-ENERGIZED (Status LED **OFF**, `COM` connected to `NC`)
* **Logic HIGH (3.3V DC - Driven by Raspberry Pi Pico):** Relay Coil ENERGIZED (Status LED **ON**, `COM` connected to `NO`)

### RJ45 Signal Mapping

```
+-------------------------------------------------------------+
|                     RJ45 Cable Pinout                       |
+---------+--------------------+------------------------------+
| Pin     | Signal             | Target Channel               |
+---------+--------------------+------------------------------+
| Pin 1   | VCC                | System Power (6V–30V DC (3A Max))|
| Pin 2   | Pin 1              | Controls Relay 1             |
| Pin 3   | Pin 2              | Controls Relay 2             |
| Pin 4   | Pin 3              | Controls Relay 3             |
| Pin 5   | Pin 4              | Controls Relay 4             |
| Pin 6   | N/C                | Not Connected                |
| Pin 7   | GND                | Power Ground                 |
| Pin 8   | GND                | Power Ground                 |
+---------+--------------------+------------------------------+
```

---

## Programming & Software Control

### Arduino / C++ Example

```cpp
// Define pins connected to Relay Channels via Main Controller Port
const int RELAY_1 = 2; // Pin 1
const int RELAY_2 = 3; // Pin 2
const int RELAY_3 = 4; // Pin 3
const int RELAY_4 = 5; // Pin 4

void setup() {
  Serial.begin(115200);

  // Initialize all relay pins as outputs
  pinMode(RELAY_1, OUTPUT);
  pinMode(RELAY_2, OUTPUT);
  pinMode(RELAY_3, OUTPUT);
  pinMode(RELAY_4, OUTPUT);

  // Ensure all relays start DE-ENERGIZED
  digitalWrite(RELAY_1, LOW);
  digitalWrite(RELAY_2, LOW);
  digitalWrite(RELAY_3, LOW);
  digitalWrite(RELAY_4, LOW);

  Serial.println("4-Channel Relay Module Initialized.");
}

void loop() {
  // Turn ON Relay 1 (e.g., Unlock Maglock)
  Serial.println("Energizing Relay 1...");
  digitalWrite(RELAY_1, HIGH); // LED ON, COM connects to NO
  delay(3000);

  // Turn OFF Relay 1
  Serial.println("De-energizing Relay 1...");
  digitalWrite(RELAY_1, LOW);  // LED OFF, COM connects to NC
  delay(3000);
}
```

---

## Troubleshooting

| Symptom | Probable Cause | Recommended Action |
| :--- | :--- | :--- |
| **Status LED lights up, but connected load doesn't turn on** | Incorrect terminal wiring or missing power on `VIN`. | Check if load is connected between `COM` and `NO`. Verify power jumper setting and check `VIN` voltage with a multimeter. |
| **Relays reset or brownout main controller when triggered** | High current draw from connected loads exceeding RJ45 power limits. | Move `VIN` jumper to **External Port** mode and power loads using a dedicated external power supply. |
| **Relay status LED doesn't illuminate when signal set HIGH** | Loose RJ45 cable or improper signal pin assignment in code. | Inspect RJ45 Ethernet patch cable and confirm control pin assignments in code. |
