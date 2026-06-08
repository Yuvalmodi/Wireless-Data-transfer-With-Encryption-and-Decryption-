# Hardware-Based Wireless Data Transfer with Stream XOR Encryption

An entirely hardware-driven digital communication system that implements secure, real-time wireless data transmission using discrete digital logic components. By bypassing microcontrollers and software abstraction, this project operates entirely at the silicon level—utilizing synchronous shift registers, a discrete clock distribution network, and combinational logic ICs to demonstrate foundational circuit engineering and hardware security concepts.

---

## 🎯 Engineering Objectives & Core Concepts

This project was engineered to showcase a deep, practical understanding of digital electronics, timing constraints, and hardware design principles:

* **Symmetric Stream Ciphering:** Implements real-time bitwise hardware encryption and decryption using the properties of the Exclusive-OR (XOR) logic function.
* **Synchronous Serialization & Deserialization (SerDes):** Uses discrete shift registers to transform parallel user data into a serial bitstream for RF transmission, and back into parallel data at the receiver.
* **Clock Distribution & Synchronization:** Manages clock propagation delays across separate transmitter and receiver circuits to prevent data corruption during sampling window margins.
* **RF Signal Modulation:** Interfaces raw digital bitstreams directly with hardware-level Amplitude Shift Keying (ASK) wireless modules.

---

## 🔐 The Cryptographic Logic (Hardware XOR Cipher)

The system leverages the mathematical properties of the XOR gate ($Y = A \oplus B$) to achieve perfect symmetric encryption. In digital logic, XOR acts as a controlled inverter:

    Transmitter (Encryption): Data Bit (D) ⊕ Key Bit (K) = Ciphertext (C)
    Receiver (Decryption):    Ciphertext (C) ⊕ Key Bit (K) = Data Bit (D)

Because $(D \oplus K) \oplus K = D$, applying the exact same secret key sequence at both ends perfectly reconstructs the original payload without requiring complex software decryption algorithms.

### Truth Table Mapping
| Input Data (D) | Encryption Key (K) | Ciphertext (C) | Decrypted Data (D) |
| :---: | :---: | :---: | :---: |
| 0 | 0 | 0 | 0 |
| 1 | 0 | 1 | 1 |
| 0 | 1 | 1 | 0 |
| 1 | 1 | 0 | 1 |

---

## 🛠️ System Architecture & Hardware Bill of Materials
