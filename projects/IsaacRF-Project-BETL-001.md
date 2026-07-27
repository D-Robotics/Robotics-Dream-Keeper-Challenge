# BETL-001 — BPU Education Transport & Lidar Robot

- **Participant:** IsaacRF
- **Stage completed:** 2
- **Repository:** https://github.com/RFisaac/BETL-001
- **Community post:** https://discord.com/channels/1300358874280230994/1509220927462969575/1514489192158330931

## Summary

BETL-001 ("BPU-Education, Transport, Lidar" — pronounced "Beetle") is my scratch-built,
bio-inspired expressive lidar mobile robot on the D-Robotics RDK X5: a modular research
platform with personality baked in from the start. The long-term goal is an educational
transport robot combining BPU-accelerated vision with lidar-based navigation.

**Stage 1 (Ignite) — brain online.** Starting from first contact with the board, I flashed a
supported OS image with RDK Studio, brought up Wi-Fi with verified public DNS resolution, and
established SSH for remote development. For sensor bring-up I went beyond a basic camera
preview: using the D-Robotics GS130W MIPI stereo camera I ran on-device stereo depth
(DStereoV2.4, BPU-accelerated) at ~14.6 fps, and ran YOLOv8n object detection on the X5's BPU
via ROS 2 with annotated output (person detection in my workshop). I documented a real
integration quirk: the GS130W mounts rotated 90 degrees and only that orientation has valid GDC
rectification, so the depth preview renders upside-down — cosmetic only, the fix is mount
design, not software rotation.

**Stage 2 (Build) — full robot architecture.** Stage 2 moves BETL-001 from running demos to a
complete design: a 4-wheel skid-steer base on hoverboard hub motors driven by four ODESC
controllers over the X5's native CAN FD bus, with the two required concurrent workloads being
YOLOv8s detection on the BPU and slam_toolbox 2D SLAM on the CPU — separate compute units.
Forward depth fuses the D-Robotics stereo camera with a RealSense D435i so their failure modes
cancel; an optional "poor-man's spherical lidar" payload spins an RPLIDAR A1 on a
closed-loop-stepper slip-ring stage and encoder-reprojects it to a 3D cloud. The architecture
rests on a baseline sensor tier as a hard safety contract plus software-declared optional
payloads, and a single arbitrated path to the motors with soft + hardware e-stop.

## Technical Highlights

**Stage 1 (demonstrated on hardware)**
- **Platform:** RDK X5 (Ubuntu 22.04.5, ROS 2 Humble / tros.b), flashed via RDK Studio; Wi-Fi + SSH workflow
- **Sensor:** D-Robotics GS130W MIPI stereo (dual SC132GS global-shutter, MIPI CSI, I2C 0x32/0x33)
- **Depth:** DStereoV2.4_int16 (IGEV-based) on the BPU — ~14.6 fps at ~95-100% BPU utilization
- **AI task:** YOLOv8n (640x640 NV12, stock .bin) on the BPU via dnn_node_example, fed from the stereo left eye

**Stage 2 (designed architecture)**
- **Concurrent workloads:** YOLOv8s INT8 640x640 on the **BPU** (target >=30 FPS) + slam_toolbox 2D SLAM on the **CPU** — separate units
- **Drivetrain:** 4-wheel skid steer, hoverboard hub motors, 4x Flipsky ODESC V4.2 on the X5's **native CAN FD** bus; AS5600 wheel odometry
- **Sensing:** D-Robotics stereo + RealSense D435i (active-IR + base IMU), fused; 2x side 8x8 ToF (VL53L7CX); optional spinning-lidar payload via M6 cheese plate
- **Architecture:** baseline-vs-optional capability tiers (software-declared); single cmd_mux path to motors with soft + hardware e-stop
- **Microcontrollers:** RP2350/Pico 2 class (stage, odometry, power, load cell, LED) over micro-ROS
- **On-robot learning (goals):** mass-conditioned energy-efficient locomotion + goal-conditioned Y-horn ball-pushing, trained in Isaac Lab with roller-dyno sim-to-real calibration

## Links & Evidence

- **Stage 2 proposal** (Challenges 1-3, diagrams, BOM, risks): https://github.com/RFisaac/BETL-001/blob/main/PROPOSAL.md
- **Roadmap** (milestones through July 15): https://github.com/RFisaac/BETL-001/blob/main/ROADMAP.md
- **Stage 1 screenshots:** https://github.com/RFisaac/BETL-001/tree/main/media/stage1
- **Stage 1 documentation:** https://github.com/RFisaac/BETL-001#stage-1--ignite-challenge

---

I agree that this showcase document may be used by the Robotics Dream Keeper Challenge organizers as described in the official README (promotion, judging, and archives).