# 4-Channel Digital Input / Buttons Module

The **4-Channel Digital Input / Buttons Module** by **AMlab** allows escape room builders to connect up to 4 dry-contact inputs—such as push buttons, reed switches, limit switches, or microswitches—to an AMlab **Main Control Module MIDI** or **Main Control Module MEGA** using a single standard RJ45 Ethernet cable.

It features hardware debouncing and onboard ESD protection to eliminate ghost triggers and protect sensitive electronics from static discharges caused by players.

---

## Technical Specifications

| Parameter | Specification |
| :--- | :--- |
| **Manufacturer** | AMlab |
| **Input Channels** | 4 digital inputs (dry-contact switches / sensors) |
| **Input Type** | Active LOW (Internal pull-up on GPIO lines) |
| **Status Indicators** | 4x Onboard LEDs (illuminates when input closed to GND) |
| **Hardware Features** | Hardware RC debouncing, ESD protection |
| **Connection to Main Controller** | Standard RJ45 Ethernet Port (CAT5 / CAT6 cable) |
| **Sensor Connector Type** | 4x 2-Pin Screw Terminal Blocks |
| **Supply Voltage** | 12V / 24V DC (Supplied via RJ45 cable from Main Controller) |
| **Extra DC Jack Required?** | No (Fully powered via RJ45) |

---

## Wiring Instructions

### 1. Connecting Sensors / Buttons

Each input channel has a **2-pin screw terminal block**:
* **Pin 1 (GPIO):** Signal line connected to an internal pull-up resistor, debouncing RC circuit, and ESD diode.
* **Pin 2 (GND):** Ground return line for the switch.

> **Note:** Because the module uses internal pull-up resistors, you only need to connect passive, "dry-contact" switches across each terminal pair. **Do not apply external voltage to these terminals.**

```
       +---------------------------------+
       |   4-Channel Buttons Module      |
       |             AMlab               |
       |                                 |
       |  [CH1]  [CH2]  [CH3]  [CH4]     |
       |  (o)(o) (o)(o) (o)(o) (o)(o)    |
       +---|--|--------------------------+
           |  |
      +----+  +----+
      |            |
  [ Switch / Reed Sensor ]
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
| Pin 3   | GPIO 1             | Channel 1 Input Signal       |
| Pin 4   | GPIO 2             | Channel 2 Input Signal       |
| Pin 5   | GPIO 3             | Channel 3 Input Signal       |
| Pin 6   | GPIO 4             | Channel 4 Input Signal       |
| Pin 7   | GND                | Ground                       |
| Pin 8   | GND                | Ground                       |
+---------+--------------------+------------------------------+
```

---

## Programming & Logic

Since the inputs are pulled HIGH internally, pressing a button or closing a switch pulls the corresponding GPIO line to **LOW**. 

* **Open / Idle State:** `HIGH` (LED OFF)
* **Closed / Triggered State:** `LOW` (LED ON)

Because hardware debouncing is built onto the board, software debouncing delays are unnecessary in your code.

### Option A: Arduino / C++ Example

```cpp
// Define the GPIO pins corresponding to the RJ45 port on Main Control Module MIDI / MEGA
const int BTN_CH1 = 2; // Adjust pin numbers according to your Main Control Module port layout
const int BTN_CH2 = 3;
const int BTN_CH3 = 4;
const int BTN_CH4 = 5;

void setup() {
  Serial.begin(115200);

  pinMode(BTN_CH1, INPUT);
  pinMode(BTN_CH2, INPUT);
  pinMode(BTN_CH3, INPUT);
  pinMode(BTN_CH4, INPUT);
  
  Serial.println("Buttons Module Initialized.");
}

void loop() {
  // Read inputs (LOW = Pressed, HIGH = Released)
  bool ch1 = (digitalRead(BTN_CH1) == LOW);
  bool ch2 = (digitalRead(BTN_CH2) == LOW);
  bool ch3 = (digitalRead(BTN_CH3) == LOW);
  bool ch4 = (digitalRead(BTN_CH4) == LOW);

  // Print current status of each button
  Serial.print("Status -> CH1: ");
  Serial.print(ch1 ? "PRESSED " : "RELEASED");
  Serial.print(" | CH2: ");
  Serial.print(ch2 ? "PRESSED " : "RELEASED");
  Serial.print(" | CH3: ");
  Serial.print(ch3 ? "PRESSED " : "RELEASED");
  Serial.print(" | CH4: ");
  Serial.println(ch4 ? "PRESSED " : "RELEASED");

  delay(500); // Update status twice per second
}
```

### Option B: MicroPython Example

```python
from machine import Pin
import time

# Define GPIO pins corresponding to the RJ45 port on Main Control Module MIDI / MEGA
# (Replace pin numbers with your controller's pin assignments)
btn_ch1 = Pin(2, Pin.IN)
btn_ch2 = Pin(3, Pin.IN)
btn_ch3 = Pin(4, Pin.IN)
btn_ch4 = Pin(5, Pin.IN)

print("Buttons Module Initialized.")

while True:
    # Read inputs (0 / False = Pressed, 1 / True = Released)
    ch1_pressed = (btn_ch1.value() == 0)
    ch2_pressed = (btn_ch2.value() == 0)
    ch3_pressed = (btn_ch3.value() == 0)
    ch4_pressed = (btn_ch4.value() == 0)

    # Print current status of each button
    print(f"Status -> CH1: {'PRESSED' if ch1_pressed else 'RELEASED'} | "
          f"CH2: {'PRESSED' if ch2_pressed else 'RELEASED'} | "
          f"CH3: {'PRESSED' if ch3_pressed else 'RELEASED'} | "
          f"CH4: {'PRESSED' if ch4_pressed else 'RELEASED'}")

    time.sleep(0.5)
```

---

## Troubleshooting

| Symptom | Probable Cause | Recommended Action |
| :--- | :--- | :--- |
| **Status LED doesn't light up when switch is closed** | Broken wire or faulty switch. | Use a jumper wire directly across the 2-pin screw terminal. If the LED turns on, the module is working; check your external wiring or switch. |
| **Channel stays triggered constantly (LED always ON)** | Short circuit on switch wires or wrong RJ45 pinout. | Disconnect the external switch wires from the terminal block. If the LED turns off, check your wiring for short circuits. |
| **Inputs flicker or trigger randomly** | Ethernet cable loose or corrupted ground line. | Inspect RJ45 cable connectors. Ensure cable is firmly seated into both the module and the Main Control Module MIDI / MEGA. |
| **Main controller fails to read state changes** | Incorrect pin assignment in code. | Verify that the physical RJ45 port on the Main Control Module MIDI / MEGA maps to the correct software pin numbers defined in your code. |
