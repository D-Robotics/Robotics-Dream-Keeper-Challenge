# Korosuke (コロ助) — Animatronic AI Robot

- **Participant:** Kazuki Murata — GitHub: [@gurimaruking](https://github.com/gurimaruking) (uecken)
- **Stage completed:** 3 (Launch)
- **Repository:** https://github.com/gurimaruking/corosuke-robot
- **Stage 3 showcase:** https://github.com/gurimaruking/corosuke-robot/blob/main/STAGE3.md
- **Design proposal (Stage 2):** https://github.com/gurimaruking/corosuke-robot/blob/main/PROPOSAL.md
- **Roadmap:** https://github.com/gurimaruking/corosuke-robot/blob/main/ROADMAP.md
- **Demo video (3–7 min):** https://youtu.be/NJwj6Iazd20
- **Community post (Stage 2):** https://discord.com/channels/1300358874280230994/1508433443648700516/1523261786034540704

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

## Stage 2 — Build (System Design)

For Stage 2 I turned the proven Stage-1 brain into a full **intelligent-robot design**.
The complete proposal is in [PROPOSAL.md](https://github.com/gurimaruking/corosuke-robot/blob/main/PROPOSAL.md); highlights:

- **Concept & measurable goals** — Korosuke *sees → turns → greets → emotes → gestures*, all on-device. Acceptance criteria are quantified (e.g. G1: ≥10 FPS on BPU, detection→action <150 ms; G3: wake→speech <3.0 s), grounded in Stage-1 measurements.
- **AI architecture** — a **ROS 2 graph** with the RDK X5 as the single cognitive core: `vision_node` (BPU YOLO11 + face) → `ai_brain_node` → `dialogue_node` (LLM + "…nari" persona) → `voice_node` (TTS) / `expression_node` (eyes) / `motion_node`, bridged over UART to ESP32 sub-controllers. Includes system flow + node-graph Mermaid diagrams and a **BPU/CPU-core compute-allocation table**.
- **The key pivot** — the pre-challenge design (3× ESP32 + a home server doing the AI) is re-wired so the RDK X5 owns cognition and the ESP32s become dumb real-time actuator drivers. This is *re-wiring, not re-inventing* — most subsystems already exist.
- **Engineering plan** — [BOM](https://github.com/gurimaruking/corosuke-robot/blob/main/PROPOSAL.md#31-bill-of-materials-summary), ROS 2 workspace layout, a week-by-week [ROADMAP.md](https://github.com/gurimaruking/corosuke-robot/blob/main/ROADMAP.md) through the 7/15 demo, and a top-8 **risk table** with mitigations and pivot triggers (bipedal locomotion is explicitly a decoupled stretch, never on the MVP critical path).

Progress already banked toward Stage 3: the full body has been **redesigned for color-split 3D printing**, and the **eye coprocessor is alive** — 2× GC9A01 round LCDs driven by an ESP32-S3 (LovyanGFX) with emotion/gaze/blink commands over UART.

## Stage 3 — Launch (Fully On-Device Interactive Robot)

Stage 3 delivered the working, packaged robot — a ~46 cm Korosuke that **wakes up and
greets you, sees and tracks you, listens, thinks, talks back, smiles, ponders, reacts to
gestures, waves its arms, and shuts itself down safely — 100 %
on-device, no cloud, no dev PC at runtime.** Full showcase (benchmarks, Mermaid
architecture, known-issues / failure-recovery, requirement-coverage table):
**[STAGE3.md](https://github.com/gurimaruking/corosuke-robot/blob/main/STAGE3.md)**.

Highlights:
- **On-device AI pipeline** — BPU YOLO11n-pose (~19.5 FPS) for perception; CPU sherpa-onnx
  STT (RTF 0.44) → TinySwallow-1.5B LLM (llama.cpp) → Open JTalk TTS for dialogue. **The
  BPU does perception, the CPU does language** — I verified from D-Robotics' *own* X5
  packages that the X5 BPU **cannot** accelerate an LLM (an S100/Nash-only feature), so
  this split is the correct architecture, not a shortfall.
- **Two integrations of one on-device pipeline** — a **ROS 2 graph** (6 nodes + custom
  `FacePose`/`EyeCmd` messages, `ros2 launch korosuke_nodes korosuke.launch.py`) whose
  `dialogue_node`/`voice_node` run the **same on-device stack as the monolith (TinySwallow
  via llama.cpp + Open JTalk, no cloud)**, and a low-latency monolithic `korosuke-monitor`
  service (adds the web dashboard, audio DSP and 8-state eyes). All live testing and the
  demo video use the monolith; in the ROS graph the vision→eyes path is HW-verified and the
  full dialogue graph is code-complete (end-to-end board run pending) — see STAGE3 §5.1.
- **Custom kernel work to miniaturize audio** — the oversized speakers were replaced by a
  φ50 driver on a **MAX98357A I2S amp**, for which I built an **out-of-tree ALSA codec
  driver + device-tree overlay** the vendor kernel didn't ship, plus a playback DSP so the
  small speaker is loud without clipping.
- **Ships like a product** — power-on auto-start (systemd), a physical **safe-shutdown
  button** (goodnight voice → ✕✕ "safe-to-unplug" eyes), a self-healing audio card, a
  static maintenance IP, and expressive **smile / thinking / ✕✕** eyes.
- **Multi-task on one board** — BPU vision + CPU STT/LLM/TTS run concurrently at
  **≈50 °C** (< 60 °C target) even with the LLM pinning ~600 % CPU.

Engineering investigations produced along the way (all in `docs/`):
[why BPU-LLM is S100-only](https://github.com/gurimaruking/corosuke-robot/blob/main/docs/rdk_x5_bpu_llm.md),
the [I2S amp build](https://github.com/gurimaruking/corosuke-robot/blob/main/docs/rdk_x5_40pin_i2s_max98357a.md),
and a [power/USB brown-out troubleshooting guide](https://github.com/gurimaruking/corosuke-robot/blob/main/docs/power_usb_troubleshooting.md).

## Links & Evidence

- Stage 1 write-up (commands + dependencies): https://github.com/gurimaruking/corosuke-robot/blob/main/STAGE1.md
- Screenshot A — RDK Studio connected to the board (SSH connected, live remote desktop): https://github.com/gurimaruking/corosuke-robot/blob/main/docs/stage1_evidence/A_flash_ssh.png
- Screenshot B — camera preview: https://github.com/gurimaruking/corosuke-robot/blob/main/docs/stage1_evidence/B_camera_preview.jpg
- Screenshot C — live YOLO11 detection on-device: https://github.com/gurimaruking/corosuke-robot/blob/main/docs/stage1_evidence/C_yolo_live_detection.jpg
- Live demo (GIF): https://github.com/gurimaruking/corosuke-robot/blob/main/docs/stage1_evidence/D_yolo_live_demo.gif
- Open-source case: https://github.com/gurimaruking/rdk-x5-modular-case

---

I agree that this showcase document may be used by the Robotics Dream Keeper Challenge organizers as described in the official README (promotion, judging, and archives).
