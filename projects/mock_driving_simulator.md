---
layout: single
title: "Automotive Mock Driving Simulator"
permalink: /projects/mock_driving_simulator/
author_profile: true
header:
  teaser: /assets/images/mock_driving.png
---

![Automotive Mock Driving Simulator](/assets/images/mock_driving.png)

<div class="project-spec-grid">
  <div class="project-spec-item">
    <div class="spec-label">Domain</div>
    <div class="spec-value">Automotive Embedded Systems</div>
  </div>
  <div class="project-spec-item">
    <div class="spec-label">Microcontrollers</div>
    <div class="spec-value">Dual STM32 Nucleo (ARM Cortex-M)</div>
  </div>
  <div class="project-spec-item">
    <div class="spec-label">Protocols</div>
    <div class="spec-value">CAN 2.0B (500 kbps), SPI, I2C, UART</div>
  </div>
  <div class="project-spec-item">
    <div class="spec-label">Hardware Peripherals</div>
    <div class="spec-value">OEM Instrument Cluster, DBW Pedal, Motor PWM</div>
  </div>
</div>

## Executive Summary

The **Automotive Mock Driving Simulator** is a Hardware-in-the-Loop (HIL) testbench designed to simulate real-world automotive powertrain electronic control units (ECUs). The system interfaces an OEM automotive instrument cluster (speedometer, tachometer, fuel/temp gauges) and a drive-by-wire (DBW) accelerator pedal assembly with **dual STMicroelectronics STM32 microcontrollers** communicating over a high-speed **Controller Area Network (CAN 2.0B)** bus.

The project demonstrates embedded firmware engineering principles: multi-ECU distributed architecture, deterministic protocol timing, ADC sensor plausibility checks, and hardware timer PWM actuation.

<div class="tech-pill-container">
  <span class="tech-pill emerald">STM32 / ARM Cortex-M</span>
  <span class="tech-pill emerald">CAN Bus 2.0B</span>
  <span class="tech-pill emerald">Bare-Metal C & HAL</span>
  <span class="tech-pill cyan">ADC DMA Sampling</span>
  <span class="tech-pill cyan">Hardware Timers & PWM</span>
  <span class="tech-pill amber">Hardware-in-the-Loop (HIL)</span>
</div>

---

## Distributed ECU Architecture

```
[ Drive-by-Wire Pedal ] ──> Dual Potentiometer Analog Signals (APPS1 & APPS2)
         │
         ▼
[ ECU 1: Powertrain & Sensor Node (STM32) ]
         │  • Multi-channel ADC with Circular DMA & Rationality Check
         │  • Powertrain physics engine (calculates RPM & Speed)
         │
         ▼ (CAN Bus 2.0B @ 500 kbps - Differential Pair CAN_H / CAN_L)
         │
[ ECU 2: Cluster Gateway & Actuation Node (STM32) ]
         │  • Hardware CAN message reception & FIFO filtering
         │  • OEM Cluster protocol synthesis
         │
         ├────────────────────────────────────────┬───────────────────────────────────────┐
         ▼                                        ▼                                       ▼
[ OEM Gauge Cluster ]                    [ Motor PWM Driver ]                    [ SPI / I2C Bus ]
  Speedometer & Tachometer Needle Sweep     Simulated Engine RPM Feedback           Peripheral Sensors
```

---

## Technical Deep-Dive

### 1. Drive-by-Wire Pedal Sensor Rationality Check
Automotive accelerator pedal position sensors (APPS) employ dual redundant potentiometers with opposing or scaled voltage slopes to detect sensor failure:
* **DMA ADC Sampling:** Configured the STM32 ADC in continuous scan mode with Direct Memory Access (DMA) to buffer 64-sample rolling averages without CPU overhead.
* **Rationality & Plausibility Validation:** Enforces ISO 26262 functional safety design principles. If $|APPS_1 - 2 \times APPS_2| > \text{threshold}$ for longer than 100 ms, the firmware flags an implausibility error, lights a dashboard warning indicator, and clamps throttle output to zero.

### 2. CAN 2.0B Bus Implementation
* **Physical Layer:** Integrated SN65HVD230 / MCP2551 CAN transceivers operating on differential signaling ($CAN\_H, CAN\_L$) with 120 $\Omega$ split bus termination.
* **Bit Timing Calibration:** Configured the STM32 bxCAN peripheral prescalers, synchronization jump width (SJW), and phase segments ($TS_1, TS_2$) to achieve exactly 500 kbps with a 75% bit sample point:
  $$\text{Baud Rate} = \frac{f_{\text{APB1}}}{\text{Prescaler} \times (1 + TS_1 + TS_2)}$$
* **CAN Message Structure:**
  * **Frame ID `0x201` (Powertrain Data, 10 ms cyclic):**
    * Byte 0–1: Engine RPM ($0 - 8000 \text{ RPM}$, scaling $0.25 \text{ RPM/bit}$)
    * Byte 2–3: Vehicle Speed ($0 - 240 \text{ km/h}$, scaling $0.01 \text{ km/h/bit}$)
    * Byte 4: Throttle Percentage ($0 - 100\%$)
    * Byte 5: Fault Status Flags & Checksum

### 3. Cluster Emulation & Motor Actuation
* **OEM Protocol Synthesis:** Emulates proprietary manufacturer CAN messages required to initialize and illuminate the cluster gauges without triggering vehicle error codes.
* **PWM Motor Dynamics:** Driven via STM32 Advanced-control Timer (TIM1) generating a complementary 20 kHz PWM signal routed to a DC motor with propeller fan, providing acoustic and visual feedback directly correlated with simulated engine RPM.

---

## Technical Challenges & Engineering Solutions

| Challenge | Root Cause | Engineering Solution |
| :--- | :--- | :--- |
| **CAN Bus-Off on Boot** | Clock drift and bit-timing prescaler discrepancies during cold startup | Calibrated HSE crystal oscillator PLL multipliers and enabled automatic bus-off recovery in the bxCAN initialization registers. |
| **Analog Noise on Pedal Line** | Long unshielded breadboard jumper wires picking up motor switching noise | Added RC low-pass filters ($R = 1\text{ k}\Omega, C = 100\text{ nF}$) at the ADC input pins and implemented median digital filtering in firmware. |
| **Cluster Sleep Mode** | The instrument cluster shut down gauges after 5 seconds without network activity | Implemented a high-priority 50 Hz timer interrupt to transmit keep-alive CAN heartbeats with monotonic rolling counters. |

---

## C Firmware Highlight: CAN Frame Transmission

```c
#include "main.h"

CAN_TxHeaderTypeDef TxHeader;
uint8_t TxData[8];
uint32_t TxMailbox;

void Send_Powertrain_CAN(uint16_t rpm, uint16_t speed_kmh, uint8_t throttle_pct) {
    TxHeader.StdId = 0x201;             // Powertrain Message ID
    TxHeader.ExtId = 0x01;
    TxHeader.RTR = CAN_RTR_DATA;
    TxHeader.IDE = CAN_ID_STD;
    TxHeader.DLC = 8;                   // 8 data bytes
    TxHeader.TransmitGlobalTime = DISABLE;

    // Pack telemetry into bytes
    TxData[0] = (uint8_t)(rpm & 0xFF);
    TxData[1] = (uint8_t)((rpm >> 8) & 0xFF);
    TxData[2] = (uint8_t)(speed_kmh & 0xFF);
    TxData[3] = (uint8_t)((speed_kmh >> 8) & 0xFF);
    TxData[4] = throttle_pct;
    TxData[5] = 0x00;                   // Error flags: Normal
    TxData[6] = 0xAA;                   // Heartbeat token
    TxData[7] = TxData[0] ^ TxData[1] ^ TxData[2] ^ TxData[3] ^ TxData[4]; // XOR Checksum

    // Non-blocking transmission into available mailbox
    if (HAL_CAN_GetTxMailboxesFreeLevel(&hcan1) > 0) {
        HAL_CAN_AddTxMessage(&hcan1, &TxHeader, TxData, &TxMailbox);
    }
}
```

---

## Key Results & Verification

* **Latency:** Sub-**8 ms** reaction from physical pedal depression to gauge needle sweep.
* **Network Reliability:** Zero dropped frames or CAN error counter increments over continuous 4-hour test cycles.
* **Safety Verification:** Injected deliberate sensor short-circuits and disconnected wires; the fail-safe watchdog tripped reliably within 20 ms every trial.
