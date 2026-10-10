---
layout: single
title: "Engineering Projects"
permalink: /projects/
author_profile: true
feature_row_robotics:
  - image_path: /assets/images/teleop.jpg
    alt: "Robotic Teleoperation System"
    title: "Robotic Arm & Hand Teleoperation"
    excerpt: "3D vision-based arm and dexterous hand teleoperation proof-of-concept utilizing MediaPipe landmark tracking, ROS 2, and low-latency joint mapping."
    url: "/projects/teleop/"
  - image_path: /assets/images/AMR.png
    alt: "Autonomous Mapping Rover"
    title: "Autonomous Mapping Rover (AMR)"
    excerpt: "Omnidirectional 4WD Mecanum indoor mapping robot featuring RPLiDAR, IMU, Kalman filtering, slam_toolbox, and the Nav2 navigation stack."
    url: "/projects/rover/"
feature_row_embedded:
  - image_path: /assets/images/mock_driving.png
    alt: "Mock Driving Simulator"
    title: "Automotive Mock Driving Simulator"
    excerpt: "Embedded Hardware-in-the-Loop (HIL) simulator integrating an OEM automotive cluster, drive-by-wire pedal ADC, and dual STM32 MCUs over CAN, SPI, and I2C."
    url: "/projects/mock_driving_simulator/"
  - image_path: /assets/images/pick_and_place.jpg
    alt: "Vision Pick and Place Robot"
    title: "Autonomous Vision Pick & Place"
    excerpt: "Multi-DOF robotic manipulator performing automated color-based sorting and classification using OpenCV, Inverse Kinematics (IK), and state-machine control."
    url: "/projects/pick_and_place/"
---

Welcome to my portfolio of engineering projects spanning **autonomous robotics**, **embedded systems**, **computer vision**, and **hardware design**. Every project featured here represents a complete, physical hardware prototype built, wired, and programmed from initial schematic to working demonstration.

---

## 🤖 Robotics & Autonomous Navigation

Systems engineered for real-time spatial awareness, motion planning, and human-robot interaction.

{% include feature_row id="feature_row_robotics" %}

---

## ⚡ Embedded Systems & Perception

Hardware-in-the-loop testbenches, real-time communications, and vision-guided manipulation workcells.

{% include feature_row id="feature_row_embedded" %}

---

<div class="highlight-box">
  <p>
    <strong>Need technical details or source code?</strong> Each project write-up contains detailed block diagrams, hardware specifications, schematic breakdowns, and algorithm descriptions. You can also view my complete work history on the <a href="/resume/">Experience & Resume</a> page.
  </p>
</div>
