---
layout: single
title: "Robotic Arm & Hand Teleoperation"
permalink: /projects/teleop/
author_profile: true
header:
  teaser: /assets/images/teleop.jpg
---

![Robotic Arm & Hand Teleoperation](/assets/images/teleop.jpg)

<div class="project-spec-grid">
  <div class="project-spec-item">
    <div class="spec-label">Domain</div>
    <div class="spec-value">Robotics & Vision</div>
  </div>
  <div class="project-spec-item">
    <div class="spec-label">Timeline</div>
    <div class="spec-value">2024 &bull; Completed</div>
  </div>
  <div class="project-spec-item">
    <div class="spec-label">Core Stack</div>
    <div class="spec-value">ROS 2, MediaPipe, OpenCV, C++, Python</div>
  </div>
  <div class="project-spec-item">
    <div class="spec-label">Hardware</div>
    <div class="spec-value">6-DOF Manipulator, Articulated Hand, RGB-D Camera</div>
  </div>
</div>

## Executive Summary

This project delivers a **contactless, 3D vision-based teleoperation platform** enabling an operator to control a multi-DOF robotic arm and dexterous articulated robotic hand in real time. Rather than relying on cumbersome, expensive motion-capture suits or haptic gloves, the system leverages high-speed computer vision pipelines to extract hand landmarks and skeletal arm poses from optical camera streams, translating human motion into smooth, jitter-free joint commands with **under 35 ms end-to-end latency**.

<div class="tech-pill-container">
  <span class="tech-pill cyan">ROS 2 Humble</span>
  <span class="tech-pill cyan">OpenCV</span>
  <span class="tech-pill cyan">Google MediaPipe</span>
  <span class="tech-pill emerald">Kinematic Solvers</span>
  <span class="tech-pill emerald">Low-Latency Filtering</span>
  <span class="tech-pill amber">Docker Deployment</span>
</div>

---

## System Architecture

The pipeline consists of four modular subsystems connected through high-throughput ROS 2 communication nodes:

```
[ RGB / Depth Camera ] 
         │ (1080p @ 60 FPS)
         ▼
[ MediaPipe / OpenCV Vision Node ] ──> 21 3D Hand Landmarks & Wrist Vector
         │
         ▼
[ Kinematic Mapping & Filter Node ] ──> Exponential Smoothing & Safety Bounds
         │
         ▼
[ ROS 2 Joint Trajectory Controller ] ──> `/joint_trajectory_controller/joint_trajectory`
         │
         ▼
[ Actuator Hardware Interface ] ──> Multi-DOF Arm & Dexterous Robotic Hand
```

### 1. Vision & Landmark Estimation
* Captures high-framerate optical feeds using multithreaded frame buffering to eliminate capture lag.
* Tracks 21 distinct 3D landmarks per hand using Google MediaPipe Hand Pose ML pipelines.
* Computes vector angles between metacarpal (MCP), proximal interphalangeal (PIP), and distal interphalangeal (DIP) joints to derive individual finger bend ratios (0.0 to 1.0).

### 2. Kinematics & Signal Conditioning
* **Workspace Transformation:** Translates camera coordinate frame coordinates into the robot base coordinate frame via homogeneous transformation matrices.
* **Low-Pass Filtering:** Implements Exponential Moving Average (EMA) and deadband thresholding to eliminate hand tremor and optical tracking jitter without adding perceived phase lag.
* **Safety Envelope:** Enforces soft joint limits and maximum velocity/acceleration ramp rates, preventing physical singularities and abrupt arm movements if tracking is momentarily lost.

### 3. Middleware & Hardware Control
* Packaged as ROS 2 Humble packages running within lightweight Docker containers for reproducible deployment.
* Custom ROS 2 message definitions publish synchronized joint states for arm positioning and gripper finger tendon motors.

---

## Key Technical Challenges & Engineering Solutions

| Challenge | Root Cause | Engineering Solution |
| :--- | :--- | :--- |
| **High-Frequency Jitter** | Sub-pixel noise and landmark confidence fluctuations in monocular vision feeds | Designed an adaptive EMA filter that adjusts smoothing factor $\alpha$ dynamically based on movement velocity. |
| **End-to-End Latency** | Pipeline serialization bottlenecks between video capture, inference, and IPC | Separated camera I/O and inference into dedicated lock-free worker threads; utilized zero-copy ROS 2 intra-process transport. |
| **Out-of-Frame Failsafe** | Operator's hand temporarily leaving camera field-of-view causing invalid pose outputs | Implemented a watchdog heartbeat node that ramps actuators to a safe standstill if landmark tracking confidence drops below 0.65. |

---

## Code Highlight: Adaptive Joint Angle Smoothing

Below is a simplified snippet illustrating the velocity-adaptive landmark smoothing node used to ensure fluid actuator motion:

```python
import numpy as np

class AdaptiveJointFilter:
    def __init__(self, alpha_min=0.25, alpha_max=0.85, velocity_threshold=1.5):
        self.alpha_min = alpha_min
        self.alpha_max = alpha_max
        self.v_thresh = velocity_threshold
        self.prev_filtered = None
        self.prev_time = None

    def update(self, raw_angles: np.ndarray, current_time: float) -> np.ndarray:
        if self.prev_filtered is None:
            self.prev_filtered = raw_angles.copy()
            self.prev_time = current_time
            return self.prev_filtered

        dt = max(current_time - self.prev_time, 1e-4)
        velocity = np.linalg.norm((raw_angles - self.prev_filtered) / dt)

        # Dynamic smoothing factor: higher alpha when moving fast, lower alpha when static
        t = np.clip(velocity / self.v_thresh, 0.0, 1.0)
        alpha = self.alpha_min + t * (self.alpha_max - self.alpha_min)

        filtered = alpha * raw_angles + (1.0 - alpha) * self.prev_filtered
        self.prev_filtered = filtered
        self.prev_time = current_time
        return filtered
```

---

## Performance Metrics & Results

* **End-to-End Latency:** Consistently maintained under **32 ms** from image capture to motor response.
* **Tracking Fidelity:** 97.4% successful grasping trials on objects ranging from 20 mm to 90 mm diameter.
* **Operator Usability:** Zero calibration required for new operators; natural intuitive teleoperation within 60 seconds of onboarding.

---

## Next Steps & Future Enhancements

* [ ] Integration of vibrotactile haptic feedback modules to transmit contact force sensations to the human operator.
* [ ] Depth-assisted occlusion handling using dual stereo-camera configurations.
* [ ] Reinforcement learning policy integration for semi-autonomous grasp assistance during teleoperation.
