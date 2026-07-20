# VLM Police Patrol — Stage 3 Launch (Final Demo)

- **Participant:** Brian
- **Stage completed:** 3
- **Repository:** <https://github.com/zimbot97/VLM-Police-Patrol>
- **Demo video:** <https://www.youtube.com/watch?v=03x9mwHFfY8>

> All images below are hosted on the participant's own repository
> (`raw.githubusercontent.com/zimbot97/VLM-Police-Patrol`) and are embedded by URL — no
> image assets are added to the challenge repository.

![VLM Police Patrol](https://raw.githubusercontent.com/zimbot97/VLM-Police-Patrol/main/docs/images/banner.png)

---

## Summary

**VLM Police Patrol** is a fully working autonomous patrol robot built on the **D-Robotics
RDK X5**. It detects people on-board with a BPU-accelerated **YOLO11n** detector, triages
each candidate against an operator-supplied reference photo using an on-device
**Vision-Language Model (InternVL2.5-1B)**, and localizes confirmed matches on a live SLAM
map — all driven from a Flask web dashboard with holonomic mecanum teleop. Matching is
*surface-feature triage only* (clothing colour/type, hairstyle) — not face recognition.

For Stage 3 the system is a complete, demonstrable prototype: a 3-layer mecanum chassis
with RP2040 micro-ROS motion control, EKF sensor fusion (wheel odometry + MPU-9250 IMU), a
hardware RF E-stop, off-board `slam_gmapping` mapping with on-board **AMCL** localization,
and a two-tier AI pipeline where continuous BPU detection gates the slower VLM appearance
match. A single `master.sh` brings the whole stack up in nine ordered nodes. The demo video
shows the hardware plus 30+ seconds of continuous on-board AI operation (live YOLO stream
while the VLM runs), with the RDK X5 at ~70 °C / ~90 % CPU yet teleop staying smooth,
because motion control lives on the Pico firmware rather than the X5 CPU.

---

## Challenge 1 — Prototype Integration

A single coherent system integrating on-board AI, ROS 2, motor control with safety limits,
and multi-sensor fusion.

| Requirement | How it's met |
|-------------|--------------|
| **AI model(s) on board** | YOLO11n person detector + InternVL2.5-1B VLM, both BPU-accelerated on the RDK X5 |
| **ROS 2 communication** | All nodes talk over ROS 2 Humble (TROS) topics/services |
| **Motor / actuator control + safety limits** | RP2040 (Pico) mecanum firmware over micro-ROS with `cmd_vel` watchdog + hardware RF E-stop |
| **Sensor fusion / multi-sensor timing** | `robot_localization` EKF fuses wheel odometry + MPU-9250 IMU; depth + detection time-paired for localization |
| **Quick-start README** | [README.md](https://github.com/zimbot97/VLM-Police-Patrol/blob/main/README.md) (clone → build → launch) |
| **Documented launch file** | [`sh/master.sh`](https://github.com/zimbot97/VLM-Police-Patrol/blob/main/sh/master.sh) — one-shot, 9 ordered nodes |
| **Safe shutdown / E-stop** | 0.5 s `cmd_vel` watchdog brakes on connection loss; RF relay physically cuts drive power |

| Real robot | URDF model (RViz) |
|------------|-------------------|
| ![Assembled robot](https://raw.githubusercontent.com/zimbot97/VLM-Police-Patrol/main/docs/images/robot.jpeg) | ![Robot URDF model](https://raw.githubusercontent.com/zimbot97/VLM-Police-Patrol/main/src/police_patrol_bot_description/img/urdf.png) |

*Left: the assembled robot — 3-layer acrylic chassis, 4 mecanum wheels, RGB-D camera bar,
and the RF key-fob hardware E-stop. Right: the matching URDF model / TF tree.*

**Wiring schematic**

![Wiring schematic](https://raw.githubusercontent.com/zimbot97/VLM-Police-Patrol/main/docs/images/schematic.svg)

**Hardware E-stop (RF relay in series with the motor-driver power feed):**

![RF relay E-stop wiring](https://raw.githubusercontent.com/zimbot97/VLM-Police-Patrol/main/docs/images/estop_relay_wiring.png)

The RF relay's `LOAD` is the motor driver: the 12 V rail passes through `COM → NO`, so a
key-fob press de-energizes the driver and the wheels stop immediately — independent of
ROS/software. Logic power (X5 / Pico) is not routed through it, so the software `cmd_vel`
watchdog stays active too.

---

## Challenge 2 — Real-Time AI Inference

- **BPU acceleration:** the primary perception model, **YOLO11n**
  (`yolo11n_detect_bayese_640x640_nv12.bin`, 640×640 NV12, int8), runs on the RDK X5 **BPU**
  via `hobot_dnn`. The VLM's vision encoder (InternVL2.5-1B ViT, int16) also runs on the
  BPU; its Qwen2.5-0.5B language head runs on CPU via llama.cpp.
- **Two concurrent workloads:** (1) continuous YOLO detection on the BPU, publishing
  annotated frames to `/yolo/image_annotated`; (2) on-demand VLM appearance matching —
  ViT encode on BPU, LLM decode on CPU — so the match overlaps detection without stalling
  the live stream.

**Live patrol console** — the Flask dashboard streaming real-time YOLO person detection
(`person: 0.94`), the SLAM map with robot pose, and holonomic teleop:

![Patrol console with live YOLO detection](https://raw.githubusercontent.com/zimbot97/VLM-Police-Patrol/main/docs/images/yolo.png)

**Suspect feature match** — the VLM compares a reference photo against the captured
candidate by clothing colour/type and hairstyle:

| ✅ Match (3/3 fields) | ❌ No match (1/3 fields) |
|----------------------|--------------------------|
| ![Suspect match](https://raw.githubusercontent.com/zimbot97/VLM-Police-Patrol/main/docs/images/match.png) | ![Suspect no match](https://raw.githubusercontent.com/zimbot97/VLM-Police-Patrol/main/docs/images/Notmatch.png) |

Same suspect → `blue / jacket / short black hair` matches on all three fields; a different
person differs on clothing colour and type → **NO MATCH**.

**Observed load (full stack + VLM running):** ~70 °C and ~90 % CPU on the RDK X5 while the
whole stack runs and the VLM is active — yet teleop stays smooth, because motion control
lives on the Pico firmware, not the X5 CPU. Full benchmark table (resolution, FPS/latency,
model names, tool versions) in
[docs/benchmarks.md](https://github.com/zimbot97/VLM-Police-Patrol/blob/main/docs/benchmarks.md).

---

## Challenge 3 — Final Demo & Packaging

| Deliverable | Link |
|-------------|------|
| **Demo video** (YouTube) | <https://www.youtube.com/watch?v=03x9mwHFfY8> |
| **GitHub repo** (public) | <https://github.com/zimbot97/VLM-Police-Patrol> |
| **Technical documentation** | [docs/technical.md](https://github.com/zimbot97/VLM-Police-Patrol/blob/main/docs/technical.md) |
| **Benchmark evidence** | [docs/benchmarks.md](https://github.com/zimbot97/VLM-Police-Patrol/blob/main/docs/benchmarks.md) |
| **One-shot launch** | [`sh/master.sh`](https://github.com/zimbot97/VLM-Police-Patrol/blob/main/sh/master.sh) |

[![Watch the full demo on YouTube](https://img.youtube.com/vi/03x9mwHFfY8/maxresdefault.jpg)](https://www.youtube.com/watch?v=03x9mwHFfY8)

### What `master.sh` starts, in order

| # | Node | Purpose |
|---|------|---------|
| 1 | `police_patrol_bot bringup` | Robot base, micro-ROS, TF, IMU/EKF |
| 2 | `police_patrol_bot camera` | Astra RGB-D camera driver |
| 3 | `suspect_matcher yolo_detect` | BPU person detection + candidate crops |
| 4 | `hobot_llamacpp` | VLM appearance-attribute extraction |
| 5 | `suspect_matcher compare` | Reference vs. candidate attribute compare |
| 6 | `police_patrol_bot amcl` | Map + AMCL localization |
| 7 | `suspect_matcher suspect_localizer` | Freeze suspect map coordinate on match |
| 8 | `dashboard_flask flask_node` | Web dashboard / teleop |
| 9 | `oled_status` | On-board status + IP display |

---

## Technical Highlights

- **Models:** YOLO11n (BPU, int8) + InternVL2.5-1B VLM (ViT on BPU int16, Qwen2.5-0.5B LLM on CPU).
- **Sensors:** LeTMC-520 RGB-D camera, 4× wheel encoders, MPU-9250 9-axis IMU.
- **Perception → localization:** RGB-D depth → `depthimage_to_laserscan` → off-board
  `slam_gmapping` mapping → on-board **AMCL** localization; matches frozen at capture time
  and projected into the map frame.
- **Motion & safety:** RP2040 micro-ROS mecanum firmware (PID, `MAX_W` 20 rad/s, 0.5 s
  `cmd_vel` watchdog) + hardware RF E-stop in series with drive power.
- **Sensor fusion:** `robot_localization` EKF @ 30 Hz fusing wheel-odom velocities + IMU
  (`two_d_mode`), publishing `odom → base_footprint`.
- **Metrics:** RDK X5 ~70 °C / ~90 % CPU under full stack + VLM, teleop still smooth
  (motion control offloaded to Pico).

## Links & Evidence

- Repository & README: <https://github.com/zimbot97/VLM-Police-Patrol>
- Demo video: <https://www.youtube.com/watch?v=03x9mwHFfY8>
- Technical docs: <https://github.com/zimbot97/VLM-Police-Patrol/blob/main/docs/technical.md>
- Benchmarks: <https://github.com/zimbot97/VLM-Police-Patrol/blob/main/docs/benchmarks.md>

---

*I agree that this showcase document may be used by the Robotics Dream Keeper Challenge
organizers as described in the official README (promotion, judging, and archives).*
