---
layout: single
title: "Autonomous Mapping Rover (AMR)"
permalink: /projects/rover/
author_profile: true
header:
  teaser: /assets/images/AMR.png
---

![Autonomous Mapping Rover](/assets/images/AMR.png)

<div class="project-spec-grid">
  <div class="project-spec-item">
    <div class="spec-label">Domain</div>
    <div class="spec-value">Mobile Robotics & Autonomy</div>
  </div>
  <div class="project-spec-item">
    <div class="spec-label">Chassis</div>
    <div class="spec-value">4WD Omnidirectional Mecanum</div>
  </div>
  <div class="project-spec-item">
    <div class="spec-label">Software Stack</div>
    <div class="spec-value">ROS 2 Humble, Nav2, slam_toolbox, micro-ROS</div>
  </div>
  <div class="project-spec-item">
    <div class="spec-label">Sensors</div>
    <div class="spec-value">RPLiDAR A1/A2, 6-DOF IMU, Optical Encoders</div>
  </div>
</div>

## Executive Summary

The **Autonomous Mapping Rover (AMR)** is a custom-engineered ground robotics platform built from the ground up for GPS-denied indoor localization, 2D SLAM, and autonomous point-to-point obstacle-aware navigation. Utilizing a **4-wheel independent Mecanum drive**, the robot achieves holonomic motion (simultaneous translation in $X/Y$ and yaw rotation $\theta$), allowing it to navigate tight corridors and zero-radius turns with extreme agility.

<div class="tech-pill-container">
  <span class="tech-pill cyan">ROS 2 Humble</span>
  <span class="tech-pill cyan">Nav2 Navigation Stack</span>
  <span class="tech-pill cyan">slam_toolbox</span>
  <span class="tech-pill emerald">Mecanum Kinematics</span>
  <span class="tech-pill emerald">Extended Kalman Filter (EKF)</span>
  <span class="tech-pill amber">micro-ROS Firmware</span>
</div>

---

## Hardware Architecture & Electronics

The rover features a modular dual-deck acrylic and 3D-printed chassis engineered for isolation between noisy motor switching currents and sensitive embedded compute electronics:

### Subsystems Breakdown

1. **Embedded Microcontroller Deck:**
   * **Controller:** 32-bit ESP32 / STM32 microcontroller running real-time motor control loops at 50 Hz.
   * **Motor Drivers:** Dual dual-channel MOSFET H-bridge drivers with decoupling electrolytic filter capacitors, handling up to 10A peak per channel.
   * **Feedback:** Quadrature optical encoders on all 4 gearmotors reading wheel shaft ticks with interrupt-driven counters.
2. **Perception & Sensors:**
   * **LiDAR:** 360-degree RPLiDAR laser scanner mounted on the top deck with an unobstructed $360^\circ$ planar field of view.
   * **IMU:** 6-DOF Inertial Measurement Unit (accel + gyro) with I2C digital interface for real-time angular rate and orientation tracking.
3. **High-Level Compute:**
   * Embedded Linux Single Board Computer (SBC) running Ubuntu 22.04 LTS and ROS 2 Humble.
   * High-speed USB UART serial communication between SBC and the motor microcontroller via micro-ROS.
4. **Power Distribution System:**
   * 3S LiPo battery pack (11.1V – 12.6V nominal).
   * High-efficiency step-down DC-DC buck converters supplying regulated 5V (4A) for SBC and 3.3V for sensors, with shared ground star topology.

---

## Software Pipeline & Autonomy Stack

```
[ RPLiDAR 2D Scan ] ─────────────────────────┐
                                              ▼
[ 4x Encoders ] ──> [ Forward Kinematics ] ──> [ robot_localization EKF ] ──> `/odometry/filtered`
[ 6-DOF IMU ]   ──> [ Bias Calibration ]  ───┘                                     │
                                                                                   ▼
                                                             [ slam_toolbox / AMCL ]
                                                                                   │
                                                                                   ▼
                                                             [ Nav2 Global & Local Costmaps ]
                                                                                   │
                                                                                   ▼
                                                             [ DWB Controller / Trajectory Rollout ]
                                                                                   │
                                                                                   ▼
                                                             [ `/cmd_vel` Twist Commands ]
```

### 1. Holonomic Kinematics & micro-ROS Firmware
For a 4-wheel Mecanum drive with wheel radius $r$ and chassis half-dimensions $l_x, l_y$, the inverse kinematics mapping linear velocities $(v_x, v_y)$ and angular velocity $\omega_z$ to wheel angular velocities $\omega_i$ is calculated onboard:

$$\begin{bmatrix} \omega_1 \\ \omega_2 \\ \omega_3 \\ \omega_4 \end{bmatrix} = \frac{1}{r} \begin{bmatrix} 1 & -1 & -(l_x + l_y) \\ 1 & 1 & (l_x + l_y) \\ 1 & 1 & -(l_x + l_y) \\ 1 & -1 & (l_x + l_y) \end{bmatrix} \begin{bmatrix} v_x \\ v_y \\ \omega_z \end{bmatrix}$$

Each wheel runs a closed-loop discrete PID velocity controller to track target angular velocities across varying floor textures.

### 2. Sensor Fusion with Extended Kalman Filter (EKF)
Mecanum rollers are inherently subject to slip on smooth surfaces. Relying purely on wheel odometry produces accumulated rotational drift. 
* By configuring `robot_localization` with an **Extended Kalman Filter (EKF)**, the system fuses high-frequency IMU yaw velocity with raw wheel encoders.
* This ensures stable, drift-resistant dead reckoning published to the `/odometry/filtered` topic and dynamic `odom -> base_link` TF transforms.

### 3. SLAM & Autonomous Navigation (Nav2)
* **Mapping:** `slam_toolbox` in asynchronous mode generates 2D occupancy grid maps (`nav_msgs/OccupancyGrid`) at 5 cm resolution, handling loop closures automatically.
* **Costmaps:** Nav2 maintains multi-layered global and local 2D costmaps, featuring static map layers, obstacle detection layers from LiDAR rays, and inflation buffers matching the robot's physical footprint.
* **Path Planning & Control:** 
  * Global Planner: NavFn Dijkstra / A* path solver.
  * Local Controller: DWB (Dynamic Window Approach) tuned specifically for holonomic velocity profiles, allowing sideways crab-walk maneuvers to bypass obstacles without needing to turn.

---

## Technical Challenges & Solutions

| Challenge | Root Cause | Engineering Solution |
| :--- | :--- | :--- |
| **Encoder Slip Drift** | Mecanum 45° rollers experience micro-slippage during rapid acceleration | Fused 6-DOF IMU gyroscope Z-axis rates with wheel odometry using the EKF, reducing angular position drift by 88%. |
| **Roller Vibration Noise** | Vibrations from 45° Mecanum contact rollers propagating to LiDAR scans | Designed 3D-printed TPU vibration dampening isolators under the LiDAR riser deck and enabled software range median filtering. |
| **Power Brownouts** | Simultaneous 4-motor stall current spikes causing voltage drops on 5V SBC rail | Implemented isolated buck regulation with separate LC filtering and a Schottky diode reverse-bias barrier. |

---

## Results & Benchmarks

* **Mapping Precision:** Successfully generated coherent maps of a 180 m² multi-room test floor with under 4 cm dimensional error.
* **Autonomous Goal Reaching:** Achieved 100% path completion across 25 dynamic obstacle avoidance obstacle-course trials.
* **Holonomic Agility:** Reduced navigation transit times in cramped spaces by 30% compared to standard differential-drive rovers.

---

## Future Roadmap

* [ ] Add an RGB-D depth camera (Intel RealSense) for 3D point cloud obstacle clearing and negative obstacle (stairs) detection.
* [ ] Integrate visual-inertial odometry (VIO) for redundant positioning when wheel slippage is severe.
* [ ] Develop a web-based ROS 2 dashboard using WebSockets and Foxglove Studio for live telemetry monitoring.
