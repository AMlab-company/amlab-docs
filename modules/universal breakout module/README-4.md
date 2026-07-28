# Ethernet Breakout Module

The **Ethernet Breakout Module** by **AMlab** is a universal expansion board designed to seamlessly adapt microcontrollers, third-party digital Arduino modules, buttons, and custom escape room props to **AMlab Main Control Modules (MIDI / MEGA)** via a standard RJ45 Ethernet patch cable.

By soldering designated jumper pads on the board, users can select signal routing options, enabling direct connection or bi-directional voltage level shifting across **4 digital signal channels**.

---

## Key Features

* **Universal Digital Module Compatibility:** Interface digital joysticks, PS2 controllers, trackballs, illuminated buttons, and Arduino digital sensors / modules (I2C, SPI, UART, or standard GPIO).
* **4-Channel Configurable Digital Signal Matrix:**
  * Carries 4 digital signals (`Pin 1–4`) over the RJ45 cable.
  * Each signal line can be individually routed **directly to GPIO** or **through an onboard bi-directional voltage level shifter (3.3V <-> 5V)** via solder pads.
* **Onboard Power Management:**
  * Dedicated footprint to solder an optional **Buck Converter** or **LDO** to derive 3.3V or 5V DC from the 12V/24V RJ45 system power.
  * Onboard **Voltage Selector Jumpers/Pads** to set output power levels.
* **Dedicated JST-XH Connectors:**
  * **2x JST-XH Connectors for Buttons:** Wired to 2 of the digital signal lines for direct button/microswitch inputs.
  * **2x JST-XH Connectors for MOSFET Low-Side Outputs:** Driven by 2 low-side N-Channel MOSFETs (ideal for powering button LEDs or small loads). Pin 1 supplies **12V/24V via a 120 Ω current-limiting resistor**; Pin 2 is the NMOS switched drain return.
  * **1x 6-Pin JST Connector (Direct GPIO):** Pinout: `VCC`, `Pin 1`, `Pin 2`, `Pin 3`, `Pin 4`, `GND` (Direct connection).
  * **1x 6-Pin JST Connector (Level-Shifted):** Pinout: `VCC`, `Pin 1`, `Pin 2`, `Pin 3`, `Pin 4`, `GND` (Routed through 3.3V <-> 5V level shifter).

---

## Technical Specifications

| Parameter | Specification |
| :--- | :--- |
| **Manufacturer** | AMlab |
| **Input Interface** | Standard RJ45 Ethernet Port (CAT5 / CAT6 cable) |
| **Signal Type** | **Digital Only** (GPIO, I2C, SPI, UART) |
| **Signal Channels** | 4 Digital Channels (`Pin 1` to `Pin 4`) |
| **Level Shifter** | Onboard 4-channel bi-directional 3.3V <-> 5V Logic Level Converter |
| **Power Options** | 12V/24V System VCC, or 3.3V / 5V DC via optional Buck Converter / LDO |
| **Button Inputs** | 2x JST-XH Connectors (wired to 2 signal channels) |
| **MOSFET Outputs** | 2x JST-XH Connectors (Low-side NMOS, 12V/24V via 120 Ω inline resistor) |
| **Expansion Header** | 2x 6-Pin JST Connectors (`VCC`, `Pin 1–4`, `GND`) - 1 Direct, 1 Level-Shifted |

---

## Technical Architecture & Connectors

### 1. MOSFET Output Connectors (2x JST-XH)
Designed to power button LEDs or small 12V/24V loads directly:
* **Pin 1:** `VCC` (+12V / +24V DC supplied through a **120 Ω resistor** for LED current limiting).
* **Pin 2:** Low-Side N-MOSFET (`DRAIN`) switched ground connection.

### 2. 6-Pin Digital Expansion Connectors (2x JST)
* **Direct JST Socket:** Bypasses logic conversion; connects `Pin 1–4` directly to the Main Controller lines.
* **Level-Shifted JST Socket:** Routes `Pin 1–4` through the onboard level shifter (converting 3.3V logic to 5V or vice versa).

```
+-------------------------------------------------------------+
|                      6-Pin JST Pinout                       |
+---------+--------------------+------------------------------+
| Pin     | Signal             | Description                  |
+---------+--------------------+------------------------------+
| Pin 1   | VCC                | Selected VCC (3.3V / 5V)     |
| Pin 2   | GPIO 1             | Channel 1 Digital Signal     |
| Pin 3   | GPIO 2             | Channel 2 Digital Signal     |
| Pin 4   | GPIO 3             | Channel 3 Digital Signal     |
| Pin 5   | GPIO 4             | Channel 4 Digital Signal     |
| Pin 6   | GND                | Ground                       |
+---------+--------------------+------------------------------+
```

---

## Solder Pad Configuration

By bridging specific solder pads on the board, you can customize signal routing and power:

1. **Voltage Selection:** Select output `VCC` source (Direct VCC vs Regulated 3.3V / 5V via Buck/LDO).
2. **Channel Signal Routing:** For each of the 4 digital signals, choose between:
   * **Direct Routing:** Bypasses level shifter for straight digital I/O.
   * **Level-Shifted Routing:** Routes the signal through the onboard level converter for 3.3V <-> 5V translation.

---

## Supported Digital Peripherals

* **Buttons with Integrated LEDs:** Plug button switches into Button JST-XH sockets and LED supply lines into MOSFET JST-XH sockets.
* **Trackballs & Digital Joysticks**
* **PS2 Gamepads / Controllers**
* **Digital Sensor / Communication Modules:** Modules using digital I/O, **I2C**, **SPI**, or **UART** protocols.

---

## Wiring Instructions

### Module to Main Controller Connection (RJ45)

Connect a standard RJ45 Ethernet patch cable from the breakout module to an available expansion port on your **Main Control Module MIDI** or **Main Control Module MEGA**.

---

## Programming Example

Below is an example showing how to read a button input connected to `Pin 1` and trigger the onboard MOSFET output on `Pin 2` to illuminate a button LED.

### Arduino / C++ Example

```cpp
// Define pins connected via Ethernet Breakout Module
const int BUTTON_PIN = 2; // GPIO 1 on Breakout
const int LED_MOSFET = 3; // GPIO 2 driving NMOS transistor

void setup() {
  Serial.begin(115200);
  
  // Configure Button input (Active LOW with internal pull-up)
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  
  // Configure MOSFET output for Button LED
  pinMode(LED_MOSFET, OUTPUT);
  digitalWrite(LED_MOSFET, LOW);
  
  Serial.println("Ethernet Breakout Module Initialized.");
}

void loop() {
  // Read button state
  if (digitalRead(BUTTON_PIN) == LOW) {
    // Button pressed -> turn ON button LED via MOSFET output
    digitalWrite(LED_MOSFET, HIGH);
    Serial.println("Button Pressed! LED Active.");
  } else {
    digitalWrite(LED_MOSFET, LOW);
  }
  
  delay(50);
}
```

---

## Troubleshooting

| Symptom | Probable Cause | Recommended Action |
| :--- | :--- | :--- |
| **Connected 3.3V/5V digital module does not power on** | Buck converter/LDO missing or voltage selector pads not soldered. | Solder LDO/Buck converter or bridge power pads to select output voltage. |
| **Digital communication (I2C/SPI) fails** | Signal level mismatch or wrong JST header used. | Ensure target module is plugged into the **Level-Shifted JST** header or check solder pad configuration for logic translation. |
| **Button LED stays dim or doesn't light up** | Reversed LED polarity on JST-XH socket. | Reverse JST connector wiring (ensure LED positive goes to Pin 1 with 120 Ω resistor and negative to Pin 2 NMOS drain). |
