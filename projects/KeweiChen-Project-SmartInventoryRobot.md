# RDK X5 Smart Household Inventory Robot

- **Participant:** Kewei Chen
- **Stage completed:** 1
- **Repository:** https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot
- **Demo video:** Stage 1 evidence package: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/docs/STAGE1_SUBMISSION.md
- **Community post:** https://discord.com/channels/1300358874280230994/1503706103752429618/threads/1506248828523905105

## Summary

This project designs an RDK X5-powered smart household inventory assistant with a real robotic arm. The target scenario is smart life robotics: the system will perceive household supplies, maintain inventory records, generate low-stock reminders, and later trigger robotic arm actions for simple item interaction, sorting, or demonstration tasks.

For Stage 1, the Magic Box / RDK X5 board was brought up and verified through SSH, Wi-Fi networking, desktop capture, audio device inspection, microphone recording, MIPI camera operation, and BPU-accelerated YOLO inference. The board runs Ubuntu 22.04.5 on aarch64 and is reachable through `ssh sunrise@192.168.127.10`. Wi-Fi networking was verified with successful public connectivity to GitHub.

The AI evidence includes both a static YOLOv5 sample and a live MIPI-camera YOLO pipeline. The live pipeline publishes ROS topics such as `/hbmem_img`, `/image`, `/hobot_dnn_detection`, and `/hobot_dnn_detection_info`, and reports about 29-30 FPS with BPU inference on the `yolo26s_bayese_640x640_nv12` model.

## Technical Highlights

- RDK X5 / Magic Box board running Ubuntu 22.04.5 LTS.
- SSH access, Wi-Fi networking, and XFCE desktop screenshot captured.
- MIPI camera live stream verified through MagicBox YOLO demo.
- BPU runtime verified with `BPU Platform Version(1.3.6)` and `DNN Runtime version = 1.24.5`.
- Static YOLOv5 output image generated with detected `kite` and `person` objects.
- Live ROS 2 detection topic verified through `/hobot_dnn_detection`.
- Built-in microphone and speaker devices enumerated; microphone WAV evidence recorded.

## Links & Evidence

- Stage 1 submission package: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/docs/STAGE1_SUBMISSION.md
- Desktop screenshot: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/assets/stage1_rdk_desktop.png
- YOLO output image: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/assets/stage1_yolov5_output_image.jpg
- Microphone recording: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/assets/stage1_magicbox_mic_test.wav
- Board/network log: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/evidence/stage1_board_network_ssh.txt
- Static BPU YOLO log: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/evidence/stage1_static_yolov5_bpu_output.txt
- Live MIPI YOLO log: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/evidence/stage1_live_yolo_mipi_bpu_log.txt
- ROS detection topic log: https://github.com/Suibian-YY-pro/rdk-x5-smart-inventory-robot/blob/master/evidence/stage1_ros_detection_topic.txt

---

I agree that this showcase document may be used by the Robotics Dream Keeper Challenge organizers as described in the official README (promotion, judging, and archives).

