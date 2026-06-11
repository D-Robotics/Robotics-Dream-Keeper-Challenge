# BETL-001 — BPU Education Transport & Lidar Robot

- **Participant:** IsaacRF
- **Stage completed:** 1
- **Repository:** https://github.com/RFisaac/BETL-001
- **Community post:** https://discord.com/channels/1300358874280230994/1509220927462969575/1514489192158330931

## Summary

BETL-001 (BPU Education Transport & Lidar robot) is my entry platform for the Robotics Dream Keeper Challenge, built on the D-Robotics RDK X5. The long-term goal is an educational transport robot combining BPU-accelerated vision with lidar-based navigation; Stage 1 focused on bringing the brain online.

Starting from first contact with the board, I flashed a supported OS image with RDK Studio, brought up Wi-Fi networking with verified public DNS resolution from the board, and established SSH access for remote development. For sensor bring-up I went beyond a basic camera preview: using the D-Robotics GS130W MIPI stereo camera, I ran on-device stereo depth estimation (DStereoV2.4, BPU-accelerated) producing a live colorized depth map at ~14.6 fps. For the first AI task I ran YOLOv8n object detection using the stock on-board model, executing on the X5's BPU via ROS 2 with annotated bounding-box output (person detection in my workshop).

Along the way I documented a real-world integration quirk: the GS130W mounts rotated 90° and only that orientation has valid GDC rectification calibration, so the depth preview renders upside-down — cosmetic only, the depth data is geometrically correct — and the fix is physical mount design, not software rotation. Stage 2 moves BETL-001 from running demos to a defined robot architecture integrating depth, detection, and lidar for transport tasks. All bring-up steps, run commands, dependencies, and evidence screenshots are documented in the linked repository.

## Technical Highlights

- **Platform:** D-Robotics RDK X5 (Ubuntu 22.04.5, ROS 2 Humble / tros.b), flashed via RDK Studio; Wi-Fi + SSH remote development workflow
- **Sensor:** D-Robotics GS130W MIPI stereo camera (dual SC132GS global-shutter sensors, MIPI CSI dual-lane, I2C 0x32/0x33) via hobot_mipi_cam / hobot_stereonet
- **Depth:** DStereoV2.4_int16 (IGEV-based) on the BPU — ~14.6 fps at ~95–100% BPU utilization
- **AI task:** YOLOv8n (640×640 NV12, stock on-board .bin) on the X5 BPU via dnn_node_example, fed live from the stereo left eye
- **Roadmap:** Stage 2 architecture integrating depth + detection + lidar for an educational transport robot

## Links & Evidence

- Screenshots: https://github.com/RFisaac/BETL-001/tree/main/media/stage1
- Stage 1 documentation: https://github.com/RFisaac/BETL-001#stage-1--ignite-challenge

---

I agree that this showcase document may be used by the Robotics Dream Keeper Challenge organizers as described in the official README (promotion, judging, and archives).
