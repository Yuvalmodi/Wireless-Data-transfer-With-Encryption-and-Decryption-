# Hardware-Based Wireless Data Transfer with Stream XOR Encryption

An entirely hardware-driven digital communication system that implements secure, real-time wireless data transmission using discrete digital logic components. By bypassing microcontrollers and software abstraction layers, this project operates entirely at the silicon level—utilizing synchronous shift registers, a discrete clock distribution network, and combinational logic ICs to demonstrate foundational circuit engineering, timing analysis, and hardware-level security concepts.

---

## 🎯 Engineering Objectives & Core Concepts

This project was engineered to showcase a deep, practical understanding of digital electronics, timing constraints, propagation delays, and hardware design principles:

* **Symmetric Stream Ciphering:** Implements real-time bitwise hardware encryption and decryption using the intrinsic properties of the Exclusive-OR (XOR) logic function.
* **Synchronous Serialization & Deserialization (SerDes):** Uses discrete shift registers to transform parallel user data into a serial bitstream for RF transmission, and back into parallel data at the receiving end.
* **Clock Distribution & Synchronization:** Manages clock propagation and phase shifts across separate transmitter and receiver circuits to prevent data corruption during sampling window margins.
* **RF Signal Modulation:** Interfaces raw digital bitstreams directly with hardware-level Amplitude Shift Keying (ASK) wireless transceivers.

---

## 🔐 Cryptographic Logic Architecture (Hardware XOR Cipher)

The core security of the system leverages the mathematical properties of the XOR gate ($Y = A \oplus B$) to achieve perfect symmetric stream encryption. In digital logic, an XOR gate acts as a controlled inverter:

```
Transmitter (Encryption): Data Bit (D) ⊕ Key Bit (K) = Ciphertext (C)
Receiver (Decryption):    Ciphertext (C) ⊕ Key Bit (K) = Data Bit (D)
```

Because $(D \oplus K) \oplus K = D$, applying the exact same secret key sequence at both the transmission and reception nodes perfectly reconstructs the original payload without requiring heavy software processing algorithms.

### Truth Table Mapping
| Input Data (D) | Encryption Key (K) | Ciphertext (C) | Decrypted Data (D) |
| :---: | :---: | :---: | :---: |
| 0 | 0 | 0 | 0 |
| 1 | 0 | 1 | 1 |
| 0 | 1 | 1 | 0 |
| 1 | 1 | 0 | 1 |

---

## 🛠️ System Block Diagram

```
[Parallel Input] ---> [74HC165 Shift Register] ---> [74HC86 XOR] ---> [433MHz TX]
(DIP Switches)         (Parallel to Serial)         (Encryption)        (Wireless Link)
                                                                          |
                                                                          v
[LED Array Out] <--- [74HC595 Shift Register] <--- [74HC86 XOR] <--- [433MHz RX]
(Parallel Output)      (Serial to Parallel)         (Decryption)       (Wireless Link)
```

---

## 📋 Hardware Bill of Materials (BOM)

| Component Name | Part ID / Type | Quantity | Technical Purpose |
| :--- | :--- | :---: | :--- |
| **Quad 2-Input XOR Gate** | 74HC86 | 2 | Executes the high-speed bitwise encryption and decryption matrix. |
| **8-Bit PISO Shift Register** | 74HC165 | 1 | Captures static 8-bit user data from switches and serializes it. |
| **8-Bit SIPO Shift Register** | 74HC595 | 1 | Latches incoming decrypted serial streams back onto a parallel bus. |
| **Precision Timer** | NE555 | 1 | Generates the master synchronous clock pulse for the system. |
| **433MHz RF Transmitter** | FS1000A | 1 | Performs raw Amplitude Shift Keying (ASK) radio modulation. |
| **433MHz RF Receiver** | XY-MK-5V | 1 | Captures raw RF signals and outputs demodulated digital pulses. |
| **Schmitt Trigger Inverter** | 74HC14 | 1 | Squares up distorted analog RF signals into clean digital square waves. |
| **Input Selector Array** | 8-Way DIP Switch | 2 | Sets the input data byte and the manual encryption keys. |
| **Diagnostic Display** | 8-LED Bar Graph | 1 | Provides real-time visual verification of the decrypted output. |

---

## 🔌 Circuit Interconnections & Pin Mapping

### 1. Transmitter Block (Encryption Side)
* **Master Clock Generation:** An NE555 timer is configured in Astable mode to output a stable ~1 kHz square wave. Output **Pin 3** routes directly to **Pin 2 (CLK)** of the 74HC165 shift register.
* **Parallel Data Input:** An 8-way DIP switch bank is pulled low via a $10\text{k}\Omega$ resistor network and connected to parallel input pins **D0 through D7** of the 74HC165.
* **Encryption Matrix:** The Serial Output (**Pin 9**) of the 74HC165 is tied to **Pin 1 (Input A)** of the first 74HC86 XOR gate. **Pin 2 (Input B)** of the XOR gate connects to the Encryption Key toggle switch.
* **RF Injection:** The compiled Ciphertext output from XOR **Pin 3** is injected directly into the **DATA Pin** of the FS1000A 433MHz Transmitter.

### 2. Receiver Block (Decryption Side)
* **RF Capture Stage:** The **DATA Pin** of the XY-MK-5V RF receiver passes through a 74HC14 Schmitt Trigger inverter to eliminate noise, then connects to **Pin 4 (Input A)** of the second 74HC86 XOR gate.
* **Decryption Matrix:** **Pin 5 (Input B)** of the XOR gate is tied to the matching hardware Decryption Key toggle switch.
* **Deserialization & Output:** The decrypted serial data stream from XOR **Pin 6** feeds directly into the Serial Data Input (**Pin 14**) of the 74HC595 shift register. Parallel output pins **Q0 through Q7** drive the 8-LED Bar Graph display via $330\Omega$ current-limiting resistors.

---

## ⚡ Circuit Engineering Challenges & Solutions

### 1. Clock Synchronization Across Transmission Distances
* **The Challenge:** Because this is a pure hardware execution without modern microprocessors, there is no software-based packet header or start-bit detection to synchronize clocks over the airwaves. 
* **The Solution:** A physical dual-line transmission framework or a secondary synchronized hardware phase-locked loop (PLL) environment ensures that the receiving 74HC595 samples the ciphertext precisely at the center of the bit-period window, preventing setup/hold timing violations.

### 2. High-Frequency RF Noise Filtering
* **The Challenge:** When no active transmission carrier is present, superregenerative 433MHz RF receivers naturally output massive amounts of chaotic analog thermal/white noise, which mimics valid digital data transitions and causes ghost triggering.
* **The Solution:** Implemented a physical hardware **Schmitt Trigger** logic stage (74HC14). The hysteresis properties of the Schmitt trigger reject low-amplitude high-frequency noise spikes, clean up the signal edges, and output a pristine, squared-off digital waveform (0V to 5V) to the decryption logic.

### 3. Mechanical Switch Contact Bounce Mitigation
* **The Challenge:** Activating physical DIP switches to load parallel data creates mechanical contact bouncing, causing the shift register to misinterpret a single manual press as multiple rapid clock transitions.
* **The Solution:** Placed a physical RC low-pass filter network ($\tau = R \times C$) across the Parallel Load line to damp out microsecond-scale mechanical oscillations, ensuring single, clean logic state transitions.

---

## 🔬 System Validation & Testing Procedure

To verify signal integrity, data serialization, and cryptographic logic across the physical hardware layers:

1. **Initialize System:** Power up the breadboard rails ($5\text{V DC}$). Ensure both the encryption and decryption keys are toggled to `0` (Logic Low).
2. **Set Sample Payload:** Configure a distinct binary pattern on the transmitter DIP switches (e.g., `10101010`).
3. **Verify Plaintext Link:** Observe that the receiver LED bar graph instantly mirrors the input (`10101010`), validating that the serialization, RF transmission, and deserialization pipelines are completely operational.
4. **Engage Hardware Encryption:** Flip the transmitter's encryption key switch to `1`. Using an oscilloscope, observe that the raw radio waveform over the airwaves instantly flips to its bitwise inverse (`01010101`).
5. **Observe Secure Intercept Failure:** Leave the receiver's decryption key set to `0`. Notice that the output LEDs display the scrambled ciphertext (`01010101`), proving that an interceptor without the proper key cannot read the original data payload.
6. **Complete Secure Decryption Link:** Toggle the receiver's decryption key switch to `1`. The output LED array instantly snaps back to display the original pattern (`10101010`), successfully confirming proper real-time hardware stream decryption.
