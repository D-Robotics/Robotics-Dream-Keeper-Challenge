# Korosuke (コロ助) — Animatronic AI Robot

- **Participant:** Kazuki Murata
- **Stage completed:** 1
- **Repository:** https://github.com/gurimaruking/corosuke-robot
- **Demo video:** (screenshots below; live demo video to follow)
- **Community post:** https://discord.com/channels/1300358874280230994/1508433443648700516

## Summary

Korosuke (コロ助) is a samurai-robot character from the classic anime *Kiteretsu
Daihyakka*. This project rebuilds him as a real animatronic whose brain is a
**D-Robotics RDK X5** — using the 10 TOPS BPU for vision, ROS 2 for motion, and an
LLM voice so he speaks in his trademark "…nari" style.

For Stage 1 I took the RDK X5 from an empty board to a running on-device AI robot
brain. I flashed RDK OS 3.5.0, brought the board onto Wi-Fi, set up key-based SSH,
registered it in RDK Studio, and verified the BPU. I then drove a USB camera
(`/dev/video0`, UVC) and ran **YOLO11-nano object detection on the BPU** against a
live camera feed — detecting people and workspace objects at ~8.3 FPS end-to-end.

Along the way I hit a real thermal problem (66 °C passive) and solved it with a
40 mm fan, then designed and open-sourced the **first 3D-printable RDK X5 case**
(parametric OpenSCAD, four lids incl. a fan mount) as a contribution back to the
community.

## Technical Highlights

- **Board / compute:** RDK X5 8GB (Sunrise X5, 10 TOPS BPU), Ubuntu 22.04 / RDK OS 3.5.0, aarch64.
- **Sensor:** UVC USB camera via V4L2 (`uvcvideo`, `/dev/video0`), 640×480 MJPG, OpenCV capture.
- **AI model:** `yolo11n_detect_bayese_640x640_nv12` (YOLO11-nano, Bayes-e quantized) on **BPU core 0** via `hbm_runtime.HB_HBMRuntime`; NV12 pre-process + on-device NMS.
- **Performance:** ~8.3 FPS live (capture+decode+inference+draw); capture/decode-bound, BPU has headroom. Static-image inference ~3 s incl. model load.
- **Thermals:** 5 V 40 mm fan off the 40-pin header (pin 4/6) → idle 66 °C → ~45–49 °C, validated by `hrut_somstatus`.
- **Open-source byproduct:** [rdk-x5-modular-case](https://github.com/gurimaruking/rdk-x5-modular-case) — first community 3D-printable RDK X5 case, CC BY 4.0.
- **Next (Stage 2+):** GC9A01 eye displays reacting to detections, "…nari" LLM voice, bipedal QDD-motor motion.

## Links & Evidence

- Stage 1 write-up (commands + dependencies): https://github.com/gurimaruking/corosuke-robot/blob/main/STAGE1.md
- Screenshot A — RDK Studio connected to the board (SSH connected, live remote desktop): https://github.com/gurimaruking/corosuke-robot/blob/main/docs/stage1_evidence/A_flash_ssh.png
- Screenshot B — camera preview: https://github.com/gurimaruking/corosuke-robot/blob/main/docs/stage1_evidence/B_camera_preview.jpg
- Screenshot C — live YOLO11 detection on-device: https://github.com/gurimaruking/corosuke-robot/blob/main/docs/stage1_evidence/C_yolo_live_detection.jpg
- Live demo (GIF): https://github.com/gurimaruking/corosuke-robot/blob/main/docs/stage1_evidence/D_yolo_live_demo.gif
- Open-source case: https://github.com/gurimaruking/rdk-x5-modular-case

---

I agree that this showcase document may be used by the Robotics Dream Keeper Challenge organizers as described in the official README (promotion, judging, and archives).
