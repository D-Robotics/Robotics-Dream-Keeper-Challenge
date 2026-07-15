# TuntunClaw RDK X5

- **Participant:** Kewei Chen
- **Stage completed:** 3 (final media link pending)
- **Repository:** https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot
- **Demo video:** Pending final 3-7 minute upload
- **Community post:** https://discord.com/channels/1300358874280230994/1503706103752429618/threads/1506248828523905105

## Summary

TuntunClaw RDK X5 is a memory-aware household inventory and manipulation
assistant designed around RDK X5, OpenClaw, and a real robotic arm. The system
perceives household supplies, maintains quantity and location memory, generates
low-stock reminders, and coordinates safe manipulation tasks.

The completed TuntunClaw MuJoCo prototype demonstrates natural-language task
dispatch, VLM + SAM target segmentation, GraspNet grasp-pose inference,
continuous pick-and-place tasks without scene reset, persistent scene state,
and inventory updates. The challenge implementation adds the physical Magic
Box camera, microphone, speaker, RDK X5 BPU perception, and ROS 2 inventory
state. Real-arm execution is only claimed when visibly demonstrated in the
final video.

The completed prototype runs a live MIPI-camera YOLO model on the RDK X5 BPU, a CPU microphone activity node, and a ROS 2 inventory tracker. The tracker combines detection and audio activity using ROS receive-time alignment, publishes `/inventory/state`, and persists an atomic JSON snapshot once per second.

The live benchmark contains 644 consecutive samples. The `yolo26s_bayese_640x640_nv12` model averages 30.02 smart FPS, 24.61 ms BPU inference latency, and 72.27 ms end-to-end pipeline latency at 960 x 544. The start and stop scripts were both verified on the physical board.

## Technical Highlights

- RDK X5 / Magic Box board running Ubuntu 22.04.5 LTS.
- SSH access, Wi-Fi networking, and XFCE desktop screenshot captured.
- MIPI camera live stream verified through MagicBox YOLO demo.
- BPU runtime verified with `BPU Platform Version(1.3.6)` and `DNN Runtime version = 1.24.5`.
- Static YOLOv5 output image generated with detected `kite` and `person` objects.
- Live ROS 2 detection topic verified through `/hobot_dnn_detection`.
- Built-in microphone and speaker devices enumerated; microphone WAV evidence recorded.
- Concurrent microphone RMS workload publishes `/audio/activity`.
- Fused inventory state publishes `/inventory/state` and persists to JSON.
- Reproducible start and PID-scoped safe shutdown scripts verified on the board.
- Sustained-run temperatures: CPU 58.0 C and DDR 59.4 C.

## Links & Evidence

- TuntunClaw source and tutorial: https://github.com/datawhalechina/every-embodied/tree/main/16-%E4%B8%93%E9%A2%98%E7%BB%84%E9%98%9F%E5%AD%A6%E4%B9%A0/02-OpenClaw%E5%AE%B6%E5%BA%AD%E7%89%A9%E8%B5%84%E5%8A%A9%E6%89%8B/tuntunclaw
- Completed MuJoCo simulation demo: https://www.bilibili.com/video/BV1roAVzaEeZ

- Stage 1 submission package: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/docs/STAGE1_SUBMISSION.md
- Desktop screenshot: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/assets/stage1_rdk_desktop.png
- YOLO output image: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/assets/stage1_yolov5_output_image.jpg
- Microphone recording: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/assets/stage1_magicbox_mic_test.wav
- Board/network log: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/evidence/stage1_board_network_ssh.txt
- Static BPU YOLO log: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/evidence/stage1_static_yolov5_bpu_output.txt
- Live MIPI YOLO log: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/evidence/stage1_live_yolo_mipi_bpu_log.txt
- ROS detection topic log: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/evidence/stage1_ros_detection_topic.txt
- Stage 2 design package: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/docs/STAGE2_SUBMISSION.md
- Stage 3 submission package: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/docs/STAGE3_SUBMISSION.md
- Stage 3 benchmark: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/docs/BENCHMARK.md
- Stage 3 BPU evidence: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/evidence/stage3_live_yolo_bpu.txt
- Stage 3 inventory state: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/evidence/stage3_inventory_state.json

---

I agree that this showcase document may be used by the Robotics Dream Keeper Challenge organizers as described in the official README (promotion, judging, and archives).
