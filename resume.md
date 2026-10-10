---
layout: single
title: "Experience & Resume"
permalink: /resume/
author_profile: true
---

<div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 1.5rem; flex-wrap: wrap; gap: 1rem;">
  <div>
    <h2 style="margin: 0;">Eugenio Pasos</h2>
    <span style="color: var(--accent-cyan); font-weight: 500;">Electrical & Robotics Systems Engineer</span>
  </div>
  <div>
    <a href="mailto:eugiep@gmail.com" class="btn--primary"><i class="fas fa-envelope"></i> Contact Me</a>
  </div>
</div>

<div class="highlight-box">
  <p>
    <em>Note: This is an interactive overview of my background, skills, and engineering experience. All placeholder dates and institutions can be tailored to match your specific background.</em>
  </p>
</div>

---

## 🛠 Technical Competency Matrix

| Category | Technologies & Tools |
| :--- | :--- |
| **Robotics Frameworks** | ROS 2 (Humble/Iron), Nav2, slam_toolbox, MoveIt 2, URDF/Xacro, Gazebo, micro-ROS |
| **Embedded & Microcontrollers** | STM32 (ARM Cortex-M), ESP32, Arduino, FreeRTOS, Bare-Metal C/C++, DMA, Timers |
| **Protocols & Buses** | CAN Bus (CAN 2.0B / ISO 11898), SPI, I2C, UART/USART, PWM, TCP/IP, UDP |
| **Vision & Perception** | OpenCV (C++/Python), Google MediaPipe, RPLiDAR 2D, RGB-D Depth Cameras, Homography |
| **Programming Languages** | C, C++, Python, MATLAB, Bash Shell Scripting, Git, Markdown |
| **Hardware & Lab Equipment** | Digital Storage Oscilloscopes, Logic Analyzers, Multimeters, SMD Soldering, PCB Prototyping |
| **Software & DevOps** | Linux (Ubuntu), Docker Containerization, CMake, VS Code, Git/GitHub, STM32CubeIDE |

---

## 💼 Engineering Experience

<div class="timeline">

  <div class="timeline-item">
    <div class="timeline-title">Robotics & Embedded Systems Engineer</div>
    <div class="timeline-meta">Autonomous Systems Lab / Prototyping &bull; 2023 &ndash; Present</div>
    <div class="timeline-desc">
      <ul>
        <li>Designed and fabricated end-to-end autonomous mobile robot (AMR) platforms incorporating 4WD Mecanum drivetrains, RPLiDAR laser scanners, and dual-tier power regulation systems.</li>
        <li>Implemented ROS 2 Humble Nav2 and <code>slam_toolbox</code> pipelines, achieving robust sub-5 cm 2D occupancy grid mapping and dynamic obstacle avoidance in GPS-denied environments.</li>
        <li>Developed micro-ROS and serial communication bridges between embedded motor control MCUs and high-level Linux SBCs, reducing command latency to &lt;10 ms.</li>
      </ul>
    </div>
  </div>

  <div class="timeline-item">
    <div class="timeline-title">Embedded Firmware & Hardware Engineer (Intern)</div>
    <div class="timeline-meta">Automotive & Embedded Technology &bull; 2022 &ndash; 2023</div>
    <div class="timeline-desc">
      <ul>
        <li>Developed bare-metal C firmware for STM32 microcontrollers communicating over CAN 2.0B at 500 kbps to emulate OEM automotive instrument cluster gauges and telemetry broadcasts.</li>
        <li>Implemented dual-channel ADC with circular DMA buffering and functional safety rationality checks for drive-by-wire electronic pedal sensors.</li>
        <li>Utilized USB logic analyzers and oscilloscopes to debug physical-layer CAN bus signal integrity, bit timing prescalers, and termination reflection issues.</li>
      </ul>
    </div>
  </div>

  <div class="timeline-item">
    <div class="timeline-title">Computer Vision & Robotics Researcher</div>
    <div class="timeline-meta">University Robotics Laboratory &bull; 2021 &ndash; 2022</div>
    <div class="timeline-desc">
      <ul>
        <li>Engineered a contactless 3D vision teleoperation interface mapping human skeletal hand landmarks via MediaPipe and OpenCV directly to robotic arm actuators.</li>
        <li>Designed analytical inverse kinematics (IK) solvers and velocity-adaptive filtering algorithms to eliminate hand tremor and tracking noise in real time.</li>
        <li>Programmed automated color segmentation and homography perspective transformation for vision-guided pick-and-place manipulation workcells.</li>
      </ul>
    </div>
  </div>

</div>

---

## 🎓 Education

<div class="timeline">
  <div class="timeline-item">
    <div class="timeline-title">Bachelor of Science in Electrical Engineering</div>
    <div class="timeline-meta">University Name &bull; Graduated 2023 <em>(Customizable)</em></div>
    <div class="timeline-desc">
      <ul>
        <li><strong>Focus:</strong> Embedded Systems, Control Theory, Robotics, and Signal Processing.</li>
        <li><strong>Key Coursework:</strong> Modern Control Systems, Microprocessor System Design, Digital Signal Processing, Robotic Kinematics, Power Electronics, Computer Vision, Embedded Real-Time Operating Systems.</li>
      </ul>
    </div>
  </div>
</div>

---

## 🏆 Key Engineering Achievements

* **Omnidirectional Navigation Platform:** Built and tuned a complete 4-wheel Mecanum AMR utilizing EKF sensor fusion to eliminate odometry slip drift.
* **Low-Latency Teleoperation Pipeline:** Realized an end-to-end latency of &lt;35 ms between human hand gesture capture and physical robotic joint movement.
* **Safety-Critical Firmware:** Formulated fail-safe watchdog routines and plausibility algorithms adhering to automotive functional safety standards.
