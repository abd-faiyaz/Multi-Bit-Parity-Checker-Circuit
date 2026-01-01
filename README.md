# 🔍 Multi-Level Parity Error Detection Network

<div align="center">

![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)
![IC Count](https://img.shields.io/badge/ICs-5%20Total-blue?style=for-the-badge)
![Logic Gates](https://img.shields.io/badge/Gates-XOR%20|%20NOT%20|%20OR-orange?style=for-the-badge)
![Simulation](https://img.shields.io/badge/Simulation-Logisim-red?style=for-the-badge)

*A fault detection circuit designed to verify data integrity across multiple 4-bit data blocks*

[Features](#-features) • [Circuit Design](#-circuit-design) • [How It Works](#-how-it-works) • [Testing](#-testing) • [Hardware Implementation](#-hardware-implementation)

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Circuit Design](#-circuit-design)
- [How It Works](#-how-it-works)
- [IC Requirements](#-ic-requirements)
- [Pin Configuration](#-pin-configuration)
- [Testing](#-testing)
- [Hardware Implementation](#-hardware-implementation)
- [Simulation](#-simulation)
- [Sample Results](#-sample-results)
- [Future Enhancements](#-future-enhancements)

---

## 🎯 Overview

This project implements a **hierarchical parity error detection system** that monitors the integrity of three independent 4-bit data blocks (A, B, C). Each block carries its own parity bit and undergoes individual verification. The system then performs a system-level check to detect any overall inconsistency, triggering a visual indicator (LED) when faults are detected.

### Key Highlights

- ✅ **Block-Level Verification**: Individual parity checking for each 4-bit data block
- ✅ **System-Level Monitoring**: Aggregate fault detection across all blocks
- ✅ **Visual Feedback**: LED indicator for immediate fault notification
- ✅ **Scalable Design**: Can be extended to monitor additional data blocks
- ✅ **Standard ICs**: Uses only common 74xx series logic ICs

---

## ✨ Features

### 🔹 Multi-Level Error Detection
- **First Level**: Block-level parity verification using XOR cascades
- **Second Level**: System-wide fault aggregation using OR gates

### 🔹 Even Parity Configuration
- Supports even parity checking (can be modified for odd parity)
- Detects single-bit and multi-bit errors per block

### 🔹 Real-Time Status Indicators
- **Block Status Outputs**: Individual OK/FAULT status for each block
  - `1` = Block Correct
  - `0` = Block Faulty
- **System Fault LED**: Illuminates when ANY block has an error

### 🔹 Compact Implementation
- Only **5 ICs** required for complete 3-block system
- Minimal component count and power consumption

---

## 🔧 Circuit Design

### Architecture Overview

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Block A   │     │   Block B   │     │   Block C   │
│ (4-bit + P) │     │ (4-bit + P) │     │ (4-bit + P) │
└──────┬──────┘     └──────┬──────┘     └──────┬──────┘
       │                   │                   │
    ┌──▼──┐             ┌──▼──┐             ┌──▼──┐
    │ XOR │             │ XOR │             │ XOR │
    │Cascade            │Cascade            │Cascade
    └──┬──┘             └──┬──┘             └──┬──┘
       │                   │                   │
     ErrA                ErrB                ErrC
       │                   │                   │
       ├───────┬───────────┼───────────────────┘
       │       │           │
    ┌──▼──┐ ┌──▼──┐     ┌──▼──┐
    │ NOT │ │ OR  │     │ OR  │
    └──┬──┘ └──┬──┘     └──┬──┘
       │       │           │
   StatusA  ┌──▼───────────▼──┐
   StatusB  │  System Fault   │
   StatusC  │      LED        │
            └─────────────────┘
```

### Logic Flow

1. **Input Stage**: Each block receives 4 data bits + 1 parity bit
2. **Parity Check**: XOR cascade verifies block parity
   - Output `0` = Correct parity
   - Output `1` = Parity error
3. **Status Generation**: NOT gates invert error signals for display
   - Status `1` = Block OK
   - Status `0` = Block Fault
4. **System Fault Detection**: OR gates combine all error signals
   - System Fault `1` = At least one block has error
   - System Fault `0` = All blocks correct

---

## 💾 IC Requirements

| IC Number | Type | Description | Quantity | Gates Used |
|-----------|------|-------------|----------|------------|
| **7486** | Quad 2-input XOR | Parity calculation | 3 | 12/12 |
| **7404** | Hex Inverter | Status generation | 1 | 3/6 |
| **7432** | Quad 2-input OR | System fault detection | 1 | 2/4 |

**Total IC Count**: 5 ICs  
**Total Power Consumption**: ~50mW (typical)

---

## 📌 Pin Configuration

### Block A - 7486 IC #1

| Pin | Function | Connection |
|-----|----------|------------|
| 1 | Input A1 | A3 (Data bit 3) |
| 2 | Input B1 | A2 (Data bit 2) |
| 3 | Output Y1 | Result1 → Pin 4 |
| 4 | Input A2 | Result1 (from Pin 3) |
| 5 | Input B2 | A1 (Data bit 1) |
| 6 | Output Y2 | Result2 → Pin 9 |
| 7 | GND | Ground |
| 8 | Output Y3 | Result3 → Pin 12 |
| 9 | Input A3 | Result2 (from Pin 6) |
| 10 | Input B3 | A0 (Data bit 0) |
| 11 | Output Y4 | **ErrA** (Block A error signal) |
| 12 | Input A4 | Result3 (from Pin 8) |
| 13 | Input B4 | pA (Parity bit) |
| 14 | VCC | +5V |

*Blocks B and C follow the same pattern on separate 7486 ICs*

### Status & Fault Detection - 7404 & 7432

**7404 (NOT Gate)**
- Input: ErrA, ErrB, ErrC
- Output: StatusA, StatusB, StatusC

**7432 (OR Gate)**
- First OR: ErrA + ErrB → Temp
- Second OR: Temp + ErrC → System Fault → LED

---

## 🧪 Testing

### Test Case 1: All Blocks Correct ✅

**Input:**
```
Block A: 1100, pA = 0
Block B: 1010, pB = 0
Block C: 0101, pC = 0
```

**Expected Output:**
```
ErrA = 0, ErrB = 0, ErrC = 0
StatusA = 1 (OK), StatusB = 1 (OK), StatusC = 1 (OK)
System Fault = 0 (LED OFF)
```

**Parity Verification:**
- Block A: 1⊕1⊕0⊕0⊕0 = 0 ✓
- Block B: 1⊕0⊕1⊕0⊕0 = 0 ✓
- Block C: 0⊕1⊕0⊕1⊕0 = 0 ✓

---

### Test Case 2: Block A Has Error ❌

**Input:**
```
Block A: 1100, pA = 1  (Incorrect parity!)
Block B: 1010, pB = 0
Block C: 0101, pC = 0
```

**Expected Output:**
```
ErrA = 1, ErrB = 0, ErrC = 0
StatusA = 0 (FAULT), StatusB = 1 (OK), StatusC = 1 (OK)
System Fault = 1 (LED ON) 🔴
```

**Parity Verification:**
- Block A: 1⊕1⊕0⊕0⊕1 = 1 ✗ **ERROR DETECTED**
- Block B: 1⊕0⊕1⊕0⊕0 = 0 ✓
- Block C: 0⊕1⊕0⊕1⊕0 = 0 ✓

---

### Test Case 3: Block C Has Error ❌

**Input:**
```
Block A: 1101, pA = 1
Block B: 1010, pB = 0
Block C: 0111, pC = 0  (Incorrect parity!)
```

**Expected Output:**
```
ErrA = 0, ErrB = 0, ErrC = 1
StatusA = 1 (OK), StatusB = 1 (OK), StatusC = 0 (FAULT)
System Fault = 1 (LED ON) 🔴
```

---

### Test Case 4: Multiple Blocks Have Errors ❌❌

**Input:**
```
Block A: 1111, pA = 1  (Incorrect!)
Block B: 0000, pB = 1  (Incorrect!)
Block C: 1010, pC = 0
```

**Expected Output:**
```
ErrA = 1, ErrB = 1, ErrC = 0
StatusA = 0 (FAULT), StatusB = 0 (FAULT), StatusC = 1 (OK)
System Fault = 1 (LED ON) 🔴
```

---

### Test Case 5: All Blocks Have Errors ❌❌❌

**Input:**
```
Block A: 1111, pA = 1
Block B: 0000, pB = 1
Block C: 1010, pC = 1
```

**Expected Output:**
```
ErrA = 1, ErrB = 1, ErrC = 1
StatusA = 0 (FAULT), StatusB = 0 (FAULT), StatusC = 0 (FAULT)
System Fault = 1 (LED ON) 🔴
```

---

## 🛠️ Hardware Implementation

### Components Required

#### ICs
- 3× 7486 (Quad 2-input XOR Gate)
- 1× 7404 (Hex Inverter)
- 1× 7432 (Quad 2-input OR Gate)

#### Additional Components
- 1× LED (Red, 5mm)
- 1× Resistor (220Ω - 1kΩ for LED current limiting)
- 15× DIP Switches (for inputs)
- 1× Breadboard or PCB
- Jumper wires
- 1× 5V Power Supply

### Assembly Steps

1. **Power Distribution**
   - Connect all IC pin 14 to +5V rail
   - Connect all IC pin 7 to GND rail
   - Add decoupling capacitors (0.1µF) near each IC

2. **Input Stage**
   - Connect 15 DIP switches to inputs (A3-A0, pA, B3-B0, pB, C3-C0, pC)
   - Add pull-down resistors (10kΩ) to ensure defined logic levels

3. **XOR Cascades**
   - Wire the three 7486 ICs according to pin configuration table
   - Double-check output-to-input feedback connections

4. **Status & Fault Logic**
   - Connect error signals to 7404 inputs for status generation
   - Wire error signals to 7432 for system fault detection

5. **LED Indicator**
   - Connect LED anode to System Fault output through 330Ω resistor
   - Connect LED cathode to GND

---

## 💻 Simulation

### Logisim Evolution Setup

1. **Download the Circuit**
   ```bash
   git clone https://github.com/abd-faiyaz/Multi-Bit-Parity-Checker-Circuit.git

   ```

2. **Open in Logisim**
   - Launch Logisim (2.7.1)
   - Open `parity_detection.circ`

3. **Run Simulation**
   - Enable simulation (Ctrl+E or Simulate → Simulation Enabled)
   - Toggle input switches to test different scenarios
   - Observe Status outputs and System Fault LED

### Verification Checklist

- [ ] All green wires when inputs are set
- [ ] No red error lines
- [ ] ErrA/B/C signals match parity calculations
- [ ] Status signals properly inverted
- [ ] System Fault LED lights when any block has error
- [ ] System Fault LED off when all blocks correct

---

## 📊 Sample Results

### Result Summary Table

| Test # | Block A | Block B | Block C | ErrA | ErrB | ErrC | StatusA | StatusB | StatusC | System Fault |
|--------|---------|---------|---------|------|------|------|---------|---------|---------|--------------|
| 1 | 1100,0 | 1010,0 | 0101,0 | 0 | 0 | 0 | 1 | 1 | 1 | 0 (OFF) |
| 2 | 1100,1 | 1010,0 | 0101,0 | 1 | 0 | 0 | 0 | 1 | 1 | 1 (ON) |
| 3 | 1101,1 | 1010,0 | 0111,0 | 0 | 0 | 1 | 1 | 1 | 0 | 1 (ON) |
| 4 | 1111,1 | 0000,1 | 1010,0 | 1 | 1 | 0 | 0 | 0 | 1 | 1 (ON) |
| 5 | 1111,1 | 0000,1 | 1010,1 | 1 | 1 | 1 | 0 | 0 | 0 | 1 (ON) |

---

## 🚀 Future Enhancements

### Potential Improvements

1. **Expand to More Blocks**
   - Scale to 4, 8, or 16 data blocks
   - Use tree structure for efficient OR gate utilization

2. **Add Error Correction**
   - Implement Hamming code for single-bit error correction
   - Store original data for comparison

3. **Display Integration**
   - Add 7-segment displays to show block numbers with errors
   - Use LCD to display detailed error information

4. **Logging Capability**
   - Add shift registers to store error history
   - Interface with microcontroller for data logging

5. **Odd Parity Support**
   - Add configuration jumper to select even/odd parity
   - Use XNOR gates for odd parity checking

6. **Performance Optimization**
   - Reduce propagation delay with faster IC families (74HC, 74AC)
   - Implement pipelined error checking for high-speed data

---

## 📝 Notes

### Design Considerations

- **Propagation Delay**: Total delay ≈ 4× XOR delay + NOT delay + 2× OR delay ≈ 100-150ns
- **Power Consumption**: ~10mW per IC at 5V
- **Fan-out**: Standard TTL allows driving up to 10 TTL inputs
- **Noise Immunity**: Add 0.1µF decoupling capacitors near each IC

### Known Limitations

- Only detects errors, does not correct them
- Cannot identify which specific bit is faulty within a block
- Even number of bit flips in a block will go undetected (parity limitation)

---

## 📄 License

This project is open source and available for educational purposes.

---

## 👥 Contributors

*Abdullah Faiyaz*

---

## 🙏 Acknowledgments

- Circuit design based on digital logic principles
- Simulated using Logisim Evolution
- Inspired by error detection techniques used in computer memory systems

---

<div align="center">

**⭐ Star this repository if you found it helpful! ⭐**

Made with ❤️ and logic gates

</div>
