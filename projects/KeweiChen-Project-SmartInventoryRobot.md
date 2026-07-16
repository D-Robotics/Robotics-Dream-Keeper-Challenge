# TuntunClaw RDK X5

- **Participant:** Kewei Chen
- **Stage completed:** 3
- **Repository:** https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot
- **Demo video:** https://youtu.be/mVvQPtZMKm4
- **Community post:** https://discord.com/channels/1300358874280230994/1503706103752429618/threads/1506248828523905105

## Summary

TuntunClaw is a memory-aware household inventory and manipulation assistant.
The completed system combines RDK X5 BPU perception and Magic Box speech with
a trained SmolVLA policy, ROKAE xMate ER3 Pro arm, Lebai LMG90 gripper,
persistent SQLite inventory, a live tablet dashboard, and low-stock voice
warnings.

The project team completed SmolVLA fine-tuning and deployment on an NVIDIA RTX PRO 6000 96 GB GPU.
During the physical workflow, two live RGB views, current seven-joint state,
and the natural-language instruction feed SmolVLA. Online model actions pass
through conservative joint limits and a two-degree per-step safety gate before
xCoreSDK and Modbus execution. The submitted real-robot policy path does not
use trajectory replay.

The model-driven gripper close and release reports only a delivery candidate.
The RDK fixed camera then confirms a stable increase in delivery-tray occupancy
across multiple frames before the inventory service commits one unit. It writes
SQLite, evaluates `quantity <= threshold`, updates the tablet immediately
through SSE, and asks the RDK X5 Magic Box to announce a warning on a new
low-stock transition. An empty grasp therefore cannot decrement inventory.

In the demonstrated task, Oreo changes from five to four and remains above its
threshold of two. Coffee changes from seven to six, reaches its threshold of
six, and triggers the tablet alert and Magic Box replenishment warning.

The completed MuJoCo system also includes OpenClaw task planning, VLM + SAM
object understanding, GraspNet grasp-pose inference, continuous pick-and-place
without scene reset, and persistent object-location and inventory memory.

## Technical Highlights

- RDK X5 / Magic Box, Ubuntu 22.04.5, TogetheROS Humble.
- MIPI-camera `yolo26s_bayese_640x640_nv12` on Bayes BPU.
- 644-sample benchmark: 30.02 FPS, 24.61 ms BPU inference, 72.27 ms end-to-end.
- Fine-tuned SmolVLA through LeRobot 0.6.0, deployed on NVIDIA RTX PRO 6000 96 GB.
- ROKAE xMate ER3 Pro through xCoreSDK Python 0.7.0.
- Lebai LMG90 through 24 V USB-RS485 / Modbus RTU.
- SQLite quantities/events, idempotent visually verified tasks, REST API and SSE UI.
- Magic Box `audio_io` TTS for threshold-triggered warnings.
- Dry-run default, explicit motion confirmation, software bounds and physical emergency stop.

## Reproducibility and Evidence

- [Quick Start and project map](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot)
- [Stage 2 engineering package](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/docs/STAGE2_SUBMISSION.md)
- [Stage 3 final package](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/docs/STAGE3_SUBMISSION.md)
- [Implemented architecture, interfaces and rates](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/docs/ARCHITECTURE.md)
- [Stage 2 to Stage 3 traceability](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/docs/TRACEABILITY.md)
- [Final BOM and safety limits](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/hardware/BOM.md)
- [SmolVLA training and deployment](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/tree/master/smolvla)
- [Inventory, tablet and voice implementation](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/tree/master/inventory_web)
- [RDK delivery visual confirmation](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/docs/VISUAL_CONFIRMATION.md)
- [RDK benchmark](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/docs/BENCHMARK.md)
- [Raw BPU evidence](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/evidence/stage3_live_yolo_bpu.txt)
- [Live RDK empty-grasp rejection and delivery confirmation](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/evidence/stage3_rdk_roi_verifier.txt)
- [Physical setup](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/assets/realworld_setup.jpg)
- [Coffee low-stock result](https://github.com/Ethan-Chen-plus/rdk-x5-smart-inventory-robot/blob/master/assets/realworld_low_stock_alert.jpg)

Checkpoint weights, training recordings, and the proprietary ROKAE SDK/license
are external artifacts. Their public conversion, training, configuration and
runtime interfaces are provided in the project repository.

---

I agree that this showcase document may be used by the Robotics Dream Keeper
Challenge organizers as described in the official README.
