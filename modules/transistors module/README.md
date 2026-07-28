# 4-Channel Transistor / MOSFET Module

The **4-Channel Transistor / MOSFET Module** by **AMlab** provides 4 high-speed, low-side switching outputs, designed to drive inductive and resistive loads in escape rooms such as electromagnetic locks (maglocks), 12V/24V solenoids, high-brightness LED strips, motors, or prop triggers. 

It interfaces seamlessly with the **Main Control Module MIDI** or **Main Control Module MEGA** via a standard RJ45 Ethernet patch cable. Onboard status LEDs provide real-time visual feedback for each channel, while an onboard jumper selector allows you to power loads using power from the Ethernet cable or an external power supply (5V–30V DC) via a standard 5.5/2.1mm DC barrel jack.

---

## Technical Specifications

| Parameter | Specification |
| :--- | :--- |
| **Manufacturer** | AMlab |
| **Output Channels** | 4 low-side N-Channel MOSFET channels |
| **MOSFET Type** | 30N06 (N-Channel Power MOSFET, 30V / 30A rated chip) |
| **Current Limits** | Max **3A continuous total current** (limited by connector rating); transistor rated up to 20A pulsed |
| **External Voltage Range** | 5V to 30V DC (via external DC Barrel Jack) |
| **Series Load Resistor** | 1206 footprint SMD resistor on output line (Default: 0 Ω, user-replaceable for current limiting) |
| **Power Inputs** | Power via RJ45 cable **OR** 5.5mm / 2.1mm DC Barrel Jack |
| **Power Selection** | Onboard 3-pin Jumper (Selects RJ45 VCC vs. DC Jack VIN) |
| **Status Indicators** | 4x Onboard LEDs (illuminates when corresponding MOSFET is active) |
| **Connection to Controller** | Standard RJ45 Ethernet Port (CAT5 / CAT6 cable) |
| **Output Terminal Type** | 4x 2-Pin Screw Terminal Blocks (`VIN` and `DRAIN`) |

---

## Technical Architecture & Power Modes

Since this module uses N-Channel MOSFETs (30N06), it operates as a **low-side switch**:
* **Pin 1 (`VIN`):** Connected directly to the positive power rail (`+5V` to `+30V`), supplied either via the RJ45 cable or external DC Jack.
* **Pin 2 (`DRAIN`):** Switched ground return path. When the MOSFET is turned ON, this pin connects to `GND`, completing the circuit for your connected load.
* **Inline Load Resistor:** The load path on each channel passes through a **1206-package 0 Ω SMD resistor**. Customers can swap this 1206 resistor for a specific resistance value to add current limiting or drop voltage for sensitive loads (e.g., low-power LEDs or custom sensors).

### Power Source Selection (Jumper Settings)

The module includes an onboard 3-pin power selection header:
* **RJ45 Position:** Powers connected loads directly from the **Main Control Module MIDI / MEGA** power rail over the Ethernet cable (typically 12V or 24V DC).
* **DC Jack Position:** Isolates load power from the RJ45 cable and draws power from an external supply (5V–30V DC) plugged into the **5.5/2.1mm DC barrel jack**. Ideal for loads requiring voltages other than the system rail (e.g., 5V LED strips or 24V solenoids).

---

## Wiring Instructions

### 1. Connecting Devices (Maglocks, LED Strips, Solenoids)

Connect your load's positive wire to `VIN` and negative wire to the `DRAIN` terminal.

```
       [ + Load Wire ]  [ - Load Wire ]
       (e.g., 12V Maglock / LED Strip)
              |              |
      +-----+  +-----+
      |              |
           VIN DRAIN (-)
            |  |
       +----|--|-------------------------------------+
       |   [CH1]     [CH2]     [CH3]     [CH4]       |
       |   (o)(o)    (o)(o)    (o)(o)    (o)(o)      |
       |                                             |
       |     4-Channel Transistor Module             |
       |                AMlab                        |
       +---------------------------------------------+
```

### 2. Module to Main Controller Connection (RJ45)

Connect a standard RJ45 Ethernet patch cable from the module's RJ45 port directly into one of the designated extension ports on your **Main Control Module MIDI** or **Main Control Module MEGA**.

#### RJ45 Pinout Allocation

```
+-------------------------------------------------------------+
|                     RJ45 Cable Pinout                       |
+---------+--------------------+------------------------------+
| Pin     | Signal             | Description                  |
+---------+--------------------+------------------------------+
| Pin 1   | VCC                | Power (+12V / +24V DC)       |
| Pin 2   | VCC                | Power (+12V / +24V DC)       |
| Pin 3   | GPIO 1             | Channel 1 Gate Control Signal|
| Pin 4   | GPIO 2             | Channel 2 Gate Control Signal|
| Pin 5   | GPIO 3             | Channel 3 Gate Control Signal|
| Pin 6   | GPIO 4             | Channel 4 Gate Control Signal|
| Pin 7   | GND                | Common Ground                |
| Pin 8   | GND                | Common Ground                |
+---------+--------------------+------------------------------+
```

---

## Programming & Logic

Setting a GPIO pin **HIGH** applies voltage to the MOSFET gate, turning it ON and pulling the `DRAIN` terminal to `GND` (energizing the connected load and lighting the channel LED). Setting it **LOW** turns the MOSFET OFF.

* **GPIO LOW (0):** MOSFET OFF / Load OFF (LED OFF)
* **GPIO HIGH (1):** MOSFET ON / Load ON (LED ON)

### Option A: Arduino / C++ Example

```cpp
// Define GPIO pins corresponding to the RJ45 port on Main Control Module MIDI / MEGA
const int MOS_CH1 = 2; // e.g., Maglock 1
const int MOS_CH2 = 3; // e.g., Maglock 2
const int MOS_CH3 = 4; // e.g., LED Strip
const int MOS_CH4 = 5; // e.g., Solenoid prop

void setup() {
  Serial.begin(115200);

  // Configure output pins
  pinMode(MOS_CH1, OUTPUT);
  pinMode(MOS_CH2, OUTPUT);
  pinMode(MOS_CH3, OUTPUT);
  pinMode(MOS_CH4, OUTPUT);

  // Default state: turn all channels OFF
  digitalWrite(MOS_CH1, LOW);
  digitalWrite(MOS_CH2, LOW);
  digitalWrite(MOS_CH3, LOW);
  digitalWrite(MOS_CH4, LOW);

  Serial.println("Transistor Module Initialized.");
}

void loop() {
  // Example: Pulse Channel 1 (Maglock) ON for 2 seconds, then OFF
  Serial.println("Energizing Channel 1...");
  digitalWrite(MOS_CH1, HIGH);
  delay(2000);

  Serial.println("De-energizing Channel 1...");
  digitalWrite(MOS_CH1, LOW);
  delay(2000);
}
```

### Option B: MicroPython Example

```python
from machine import Pin
import time

# Define GPIO pins corresponding to the RJ45 port on Main Control Module MIDI / MEGA
mos_ch1 = Pin(2, Pin.OUT)
mos_ch2 = Pin(3, Pin.OUT)
mos_ch3 = Pin(4, Pin.OUT)
mos_ch4 = Pin(5, Pin.OUT)

# Default: Turn OFF all channels
mos_ch1.value(0)
mos_ch2.value(0)
mos_ch3.value(0)
mos_ch4.value(0)

print("Transistor Module Initialized.")

while True:
    # Toggle Channel 1 every 2 seconds
    print("Channel 1 -> ON")
    mos_ch1.value(1)
    time.sleep(2)

    print("Channel 1 -> OFF")
    mos_ch1.value(0)
    time.sleep(2)
```

---

## Troubleshooting

| Symptom | Probable Cause | Recommended Action |
| :--- | :--- | :--- |
| **Connected load does not turn on and LED stays dark** | Wrong jumper position or missing power input. | Check power jumper position (RJ45 vs. Barrel Jack). If set to DC Jack, ensure external 5V–30V DC power is plugged into the 5.5/2.1 jack. |
| **Status LED turns on, but load stays powered continuously** | Load wired incorrectly (bypassing MOSFET). | Ensure load positive is on `VIN` and negative is on `DRAIN`. If connected directly to ground, the MOSFET cannot control it. |
| **Total load draws >3A and connector overheats** | Exceeding terminal/connector current rating. | Total continuous current must not exceed 3A across connectors. Reduce load or distribute heavy loads across multiple module ports. |
| **Load current limited or load won't power up** | Inline 1206 resistor replaced or damaged. | Inspect the 1206 SMD inline load resistor. Standard factory value is **0 Ω**; if modified to a non-zero value, check if it severely restricts load current. |
