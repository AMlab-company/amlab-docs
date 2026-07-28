# RFID Reader Module

The **RFID Reader Module** by **AMlab** is an intelligent, standalone RFID identification module designed for escape room props, automated puzzle locks, and inventory detection systems.

Featuring an onboard **Raspberry Pi RP2040 microcontroller**, the module handles all high-speed **PN532 SPI communication**, tag reading, UID parsing, and validation logic locally. This offloads complex processing from the main controller—requiring only a single digital signal read to determine if the correct RFID tag is present.

---

## Key Features

* **Onboard RP2040 Microcontroller:** Fully manages RFID polling, card UID comparison, and onboard status feedback via an addressable RGB LED.
* **PN532 NFC/RFID Controller:** High-frequency 13.56MHz RFID reader communicating with the RP2040 via SPI for maximum reliability and speed.
* **Simple Single-Pin Interface:** Outputs a simple logic signal back to the Main Control Module on **Pin 1 (RJ45 Signal 1)**:
  * **HIGH (`3.3V / 5V`):** Correct / Matching RFID Tag present.
  * **LOW (`0V`):** Incorrect, unauthorized, or no RFID tag present.
* **Visual RGB LED Feedback:** Onboard addressable RGB LED (WS2812B/SK6812) visually indicates status (e.g., green for correct tag, red for wrong tag, blue for programming mode).
* **Easy RFID Tag Learning Mode:** Intuitive card programming sequence managed directly via the Main Control Module—no recompiling or code modifications required to add new tags!

---

## Technical Specifications

| Parameter | Specification |
| :--- | :--- |
| **Manufacturer** | AMlab |
| **Core Microcontroller** | Raspberry Pi RP2040 |
| **RFID Reader Chip** | PN532 (SPI Interface) |
| **Supported Frequency** | 13.56 MHz (Mifare 1K, S50, NTAG21x, NFC tags) |
| **Visual Indicator** | Addressable RGB LED (WS2812B) |
| **Primary Output Signal** | Digital Pin (`HIGH` = Correct Tag, `LOW` = Incorrect/No Tag) |
| **Connection Port** | Standard RJ45 Port (Connected to Main Controller) |

---

## RJ45 Pinout & Signal Mapping

Standardized pinout layout across all AMlab modules:

```
+-------------------------------------------------------------+
|                     RJ45 Cable Pinout                       |
+---------+--------------------+------------------------------+
| Pin     | Signal             | Function                     |
+---------+--------------------+------------------------------+
| Pin 1   | VCC                | System Power (6V–30V DC)     |
| Pin 2   | GND                | System Ground                |
| Pin 3   | Pin 1 (OUT)        | High = Correct Tag, Low = Incorrect/None |
| Pin 4   | Pin 2 (N/C)        | Reserved / Not Connected     |
| Pin 5   | Pin 3 (N/C)        | Reserved / Not Connected     |
| Pin 6   | Pin 4 (N/C)        | Reserved / Not Connected     |
| Pin 7   | GND                | Power Ground                 |
| Pin 8   | GND                | Power Ground                 |
+---------+--------------------+------------------------------+
```

---

## How to Program / Add New Cards

Follow these steps to teach a new RFID tag to the module:

1. **Turn OFF** power to the device/module.
2. **Press and hold the button** on the **RFID module** itself.
3. **Power ON** the module while continuing to hold the button to enter Programming Mode.
4. **Release the button.**
5. **Place the tag** near the PN532 reader. The onboard RGB LED will indicate that the tag has been successfully saved.
6. **Power cycle** the module (turn OFF and back ON) to return to normal operation.

---

## Main Controller Programming Example (Arduino / C++)

Since the RP2040 chip on the RFID module processes all the PN532 SPI logic, the user code on the main control module only needs to read the digital state of the assigned signal pin!

```cpp
// Example: Main Control Module reading RFID state
const int RFID_SIGNAL_PIN = 3; // Signal Pin 1 on RJ45 (e.g. Pin 3)

void setup() {
  pinMode(RFID_SIGNAL_PIN, INPUT);
  Serial.begin(115200);
  Serial.println("AMlab RFID Puzzle Initialized...");
}

void loop() {
  int rfidStatus = digitalRead(RFID_SIGNAL_PIN);

  if (rfidStatus == HIGH) {
    // Correct tag detected! Unlock magnetic lock or trigger puzzle success
    Serial.println("STATUS: Correct Tag Present! [UNLOCK]");
  } else {
    // Incorrect tag or no tag present
    Serial.println("STATUS: Waiting for correct tag...");
  }

  delay(200);
}
```

---

## Troubleshooting

| Symptom | Probable Cause | Recommended Action |
| :--- | :--- | :--- |
| **RGB LED stays off** | Power input missing on Pin 1/Pin 2. | Check RJ45 connection cable and verify Main Control Module power output. |
| **Tag read always returns LOW** | Card UID not saved in memory or unformatted tag. | Enter Learning Mode (Steps 1–9) to store the tag UID into memory. |
| **Red LED blinks during learning mode but doesn't save** | Reader disconnected or SPI communication error between RP2040 and PN532. | Ensure PN532 module is properly seated and SPI connections are intact. |
