---
layout: single
title: "Robotic Arm & Hand Teleoperation"
permalink: /projects/teleop/
author_profile: true
---

## Overview
A 3D vision-based robotic arm and dexterous hand teleoperation system designed for real-time motion capture and actuation. The system translates operator arm and finger poses directly to robotic hardware with low-latency control.

## System Architecture
* **Vision Pipeline:** Monocular/depth camera capture using MediaPipe and OpenCV for hand and pose landmark estimation.
* **Middleware:** ROS 2 nodes for joint angle computation, filtering, and publisher/subscriber communication.
* **Embedded Processing:** Hardware-accelerated vision and kinematics pipelines running on embedded Linux.
* **Actuation:** Multi-DOF robotic arm paired with a dexterous articulated robotic hand.

## Key Technical Features
* Real-time 3D coordinate mapping from camera space to robot joint angles.
* Latency optimization and smoothing filters to prevent oscillatory robot behavior.
* Containerized development and deployment using Docker.
