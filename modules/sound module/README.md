# Sound & Audio Expansion Module

The **Sound & Audio Expansion Module** by **AMlab** is an audio playback module designed for escape rooms, props, and interactive mechanical puzzles.

It features an onboard **DFPlayer Mini** MP3 module controlled over **UART** (`3.3V Logic`), an integrated **I2C Digital Potentiometer** for volume and audio level control, a 3.5mm audio jack, and multiple power routing options supporting **6V–30V DC input**.

---

## Key Features

* **DFPlayer Mini MP3 Player:** Onboard hardware audio decoder supporting MP3/WAV playback directly from a MicroSD card.
* **I2C Digital Volume Control:** Onboard digital potentiometer controlled via I2C interface (`SCL` / `SDA`) for dynamic audio volume adjustments.
* **3.5mm Audio Output Jack:** Stereo line-level audio jack for direct connection to external powered speakers or audio amplifiers.
* **Triple Auxiliary Power Output (3x Terminals):** Exposes three distinct power distribution lines for external accessories:
  * `5V` (Regulated Onboard Supply, **1A Max**)
  * `VIN` (Main System Power Bus line)
  * `VCC_EXT` (Dedicated External Supply Line)
* **Flexible Input Power Architecture (6V–30V DC):**
  * Internal buck converter steps down input voltage (`6V–30V DC`) to power onboard 5V logic and the DFPlayer.
  * **Input Source Selector Jumper (`VIN`):** Connects the main system input (`VIN`) to either **`VCC_ETH`** (Power from Ethernet/RJ45 cable) or **`VCC_EXT`** (External power terminal, **3A Max**).

---

## Technical Specifications

| Parameter | Specification |
| :--- | :--- |
| **Manufacturer** | AMlab |
| **Audio Hardware** | DFPlayer Mini (UART Control) |
| **Audio Output** | 3.5mm Audio Jack (Line Out) |
| **Volume Control** | Integrated I2C Digital Potentiometer |
| **Input Operating Voltage** | **6V – 30V DC (3A Max)** |
| **Regulated 5V Aux Output** | **5V DC @ 1A Max** |
| **External Power Connector** | **3A Max** capacity (`VCC_EXT`) |
| **Control Interfaces** | UART (DFPlayer RX/TX) + I2C (Digital Potentiometer) |
| **Connection to Main Controller** | Standard RJ45 Ethernet Port (CAT5 / CAT6 cable) |

---

## Power Architecture & Jumper Selection

The board uses an onboard buck converter to step down input voltages (up to 30V) down to 5V.

### Main Power Input Jumper (`VIN`)

* **`VCC_ETH` Position:** Directs power from the RJ45 Ethernet cable to the main `VIN` line powering the buck converter.
* **`VCC_EXT` Position:** Directs power from the dedicated high-current external terminal (**6V–30V DC @ 3A Max**) to the main `VIN` line.

### Auxiliary Power Output Terminals

1. **`5V` Terminal:** Onboard regulated 5V rail for auxiliary sensors or small peripherals (**1A Max**).
2. **`VIN` Terminal:** Exposes the raw input voltage fed into the board (6V–30V DC).
3. **`VCC_EXT` Terminal:** Direct connection to the external power connector terminal.

---

## RJ45 Pinout & Signal Mapping

The module communicates with the Raspberry Pi Pico via **UART** and **I2C**:

```
+-------------------------------------------------------------+
|                     RJ45 Cable Pinout                       |
+---------+--------------------+------------------------------+
| Pin     | Signal             | Function                     |
+---------+--------------------+------------------------------+
| Pin 1   | VCC                | System Power (6V–30V DC)     |
| Pin 2   | GND                | System Ground                |
| Pin 3   | Pin 1 (SDA)        | I2C Data Line (DigiPot)      |
| Pin 4   | Pin 2 (SCL)        | I2C Clock Line (DigiPot)     |
| Pin 5   | Pin 3 (DF RX)      | Pico TX -> DFPlayer RX       |
| Pin 6   | Pin 4 (DF TX)      | Pico RX <- DFPlayer TX       |
| Pin 7   | GND                | Ground                       |
| Pin 8   | GND                | Ground                       |
+---------+--------------------+------------------------------+
```

> **Note:** Pin 3 is connected to DFPlayer **RX** (Receive line from Pico) and Pin 4 is connected to DFPlayer **TX** (Transmit line to Pico).

---

## Programming & Software Control Example

```cpp
#include <Wire.h>
#include <SoftwareSerial.h>
#include <DFRobotDFPlayerMini.h>

// I2C Pins
#define SDA_PIN 3 // Signal Pin 1 on RJ45
#define SCL_PIN 4 // Signal Pin 2 on RJ45

// UART Pins for DFPlayer Mini
#define DF_RX_PIN 5  // Connected to DF RX (Signal Pin 3 on RJ45)
#define DF_TX_PIN 6  // Connected to DF TX (Signal Pin 4 on RJ45)

SoftwareSerial dfSerial(DF_RX_PIN, DF_TX_PIN); // RX, TX
DFRobotDFPlayerMini myDFPlayer;

// Digital Potentiometer I2C Address (Typical: 0x2C / 0x2F depending on chip model)
const int DIGIPOT_ADDR = 0x2C; 

void setVolumeLevel(uint8_t level) {
  Wire.beginTransmission(DIGIPOT_ADDR);
  Wire.write(0x00); // Command byte / wiper register
  Wire.write(level); // 0-255 volume attenuation
  Wire.endTransmission();
}

void setup() {
  Wire.begin();
  dfSerial.begin(9600);
  Serial.begin(115200);

  if (!myDFPlayer.begin(dfSerial)) {
    Serial.println(F("DFPlayer Mini not detected!"));
    while(true);
  }

  // Set initial volume via I2C Digital Potentiometer
  setVolumeLevel(128); 

  // Play track 0001.mp3
  myDFPlayer.play(1);
}

void loop() {
  // Audio playback loop
}
```

---

## Troubleshooting

| Symptom | Probable Cause | Recommended Action |
| :--- | :--- | :--- |
| **DFPlayer LED does not turn on** | Incorrect `VIN` jumper setting or missing input voltage. | Verify jumper is set to `VCC_ETH` or `VCC_EXT` and power source voltage is between 6V and 30V DC. |
| **No sound from 3.5mm Audio Jack** | I2C Digital Potentiometer wiper set to maximum attenuation (0). | Send I2C write command to set potentiometer wiper value above zero. |
| **Audio distorts or resets when playing** | Insufficient current on system supply. | Switch `VIN` jumper to `VCC_EXT` and connect a dedicated 12V/24V power supply (up to 3A). |
