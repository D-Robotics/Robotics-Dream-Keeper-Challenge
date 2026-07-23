# RDK X5 Tri-Cam NavBot — Encoderless Indoor Navigation

- **Participant:** Muhirwa Richard (GitHub: [@MuhirwaRichard1](https://github.com/MuhirwaRichard1))
- **Stage completed:** 3
- **Repository:** https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-
- **Demo video:** https://youtu.be/Z3CnLehWs7o
- **Extended demo (operator-console POV):** https://youtu.be/6Rp8K-f7oq8
- **Live AI inference demo:** https://youtu.be/p5Sa7evwUvI
- **License:** MIT

---

## Summary

The Tri-Cam NavBot is an indoor differential-drive robot built on the D-Robotics
RDK X5 that **maps a space, navigates to a clicked goal, and recovers when it is
picked up and moved** — with **no wheel encoders anywhere in the loop**.

Removing encoders was a deliberate constraint. Most RDK demos close the velocity
loop on wheel ticks; this robot has no electrical feedback from its motors at
all, so odometry has to be *measured against the world*. Translation comes from
**laser scan-matching** (`icp_odometry`, frame-to-map ICP on an RPLidar C1), and
heading is smoothed between the 10 Hz scans by the **MPU6050 gyro** through a
`robot_localization` EKF. `slam_toolbox` builds and later localizes against the
saved map. The result is that battery sag, wheel slip and carpet no longer smear
the map — and when the robot is physically lifted and set down somewhere else, a
lift/scan-jump detector drops it into a **RELOCALIZE** state that rotates in
place until localization re-converges, then resumes the mission.

On top of that, the **BPU runs two concurrent perception workloads**: Depth
Anything V2 (ViT-S) monocular depth and YOLO11m object detection, both through
`hobot_dnn`, both lazy-loaded so an operator who never enables them pays no BPU
cost. Everything is driven from a **PySide6 desktop operator console** that
speaks WebSocket for control and a UDP fast path for video and teleop, with
live camera feeds, an obstacle-sector HUD, map view with click-to-set-goal, and
a hardware-independent E-stop.

Stage 3 delivers the full mission end to end: **map → save → navigate to goal → arrive.**

---

## Technical Highlights

### Compute & AI

- **Board:** D-Robotics RDK X5 — 8× Cortex-A55, ~10 TOPS Bayes-e BPU, TROS (ROS 2 Humble).
- **BPU model 1 — Depth Anything V2 (ViT-S, 392×392):** monocular relative depth,
  converted ONNX → `.bin` with the RDK toolchain (INT8, `compile_mode: latency`).
  Conversion procedure documented in [`docs/depth_anything_conversion.md`](https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/blob/master/docs/depth_anything_conversion.md).
  Runtime: `hobot_dnn.pyeasy_dnn`. Node: `depth_bpu` → `/perception/grid_overlay`.
- **BPU model 2 — YOLO11m (`yolo11m_detect_bayese_640x640_nv12.bin`):** COCO object
  detection from the `rdk_model_zoo` build. Node: `detection_bpu` →
  `/perception/detections`. Front camera every tick; left/right alternate to bound
  BPU time per cycle.
- **BPU model 3 — PIDNet-S (576×768):** floor/not-floor segmentation, used in the
  standalone camera-only avoidance demos (`scripts/07`–`09`).
- **Multi-task:** depth and detection run as **two independent ROS 2 nodes sharing
  the single BPU**, each gated by its own latch topic (`/perception/depth_enable`,
  `/perception/yolo11_enable`) so the operator can toggle either live from the
  console while the navigation stack keeps running on CPU.

### Sensors & sensor fusion

| Sensor | Bus | Rate | Role |
|---|---|---|---|
| RPLidar C1 | USB `/dev/ttyUSB0` | 10 Hz | Scan-matching odometry, SLAM, obstacle sectors |
| MPU6050 IMU | I2C5 @ `0x68` | ~190 Hz | Yaw rate between scans; gyro-bias calibrated on startup |
| HBVCAM OV2710 100° (front) | USB-2 UVC, MJPEG 1280×720 | ~30 fps | Depth + detection input |
| 2× wide-angle USB cameras (L/R) | USB-2 UVC, YUYV 320×240 | ~15 fps | Side surround for the console HUD |
| TF-Luna | I2C5 @ `0x10` | — | Independent forward safety range |

**Sync approach.** The IMU is timestamped **at the moment of the I2C read** rather
than at publish, which removes Python scheduling jitter from the gyro stream. The
`robot_localization` EKF (`src/navbot_slam/config/ekf.yaml`) fuses `/odom_icp`
(x, y, yaw — absolute planar pose from ICP) with `/imu/data` (**yaw rate only** —
this MPU6050 clone reports no absolute orientation, and its accelerometer reads
gravity at ~0.55 g, so the node auto-rescales to 1 g rather than trusting the
datasheet LSB constant). The EKF runs at 30 Hz with a 0.2 s sensor timeout and
publishes `odom → base_link`; `slam_toolbox` supplies `map → odom` on top. All
three USB cameras share one host port, so they are timestamped independently and
consumed asynchronously — no hardware sync is claimed or relied upon.

### ROS 2 graph (navigate mode)

```
sllidar_node ──/scan──┬─► icp_odometry ──/odom_icp──┐
                      │                              ├─► ekf_filter_node ──TF odom→base_link──┐
imu_driver ──/imu/data────────────────────────────────┘                                        │
                      │                                                                        ▼
                      └─► scan_sectors ──/obstacles──┐                            slam_toolbox (localization)
                                                      │                              └── TF map→base_link, /map
hobot_usb_cam ×3 ──/image_raw──┬─► depth_bpu ──/perception/grid_overlay             │
                                └─► detection_bpu ──/perception/detections           │
                                                      │                              │
                     /goal (console click) ───────────┴──────────────────────────────┘
                                                      ▼
                                            goal_navigator ──/cmd_vel──►
                                            safety_gate ──/cmd_vel_safe──►
                                            motor_controller ──PWM──► L298N ──► 2× DC motors
```

Custom interfaces in `navbot_msgs`: `Sectors`, `Detections`, `GridOverlay`.
Behaviour state is published on `/behaviour/state`
(`IDLE / NAVIGATE / AVOID / RELOCALIZE / ARRIVED / LOST`).

### Safety model (three independent layers)

1. **`safety_gate`** — a deliberately dumb, trusted node between planner and
   motors. Blocks forward motion on a lidar return closer than `stop_cm` (30 cm)
   inside the forward half-width, on `/estop`, or on input timeout. It does not
   depend on the AI stack being correct.
2. **`motor_controller` dead-man** — if no `/cmd_vel_safe` arrives within
   `cmd_timeout` (0.2 s), the motors coast to a stop. This is the last line even
   if everything upstream hangs.
3. **Fail-safe sector contract** — `scan_sectors` marks any sector with no usable
   rays as `UNKNOWN`, and **every consumer treats UNKNOWN as blocked**. The robot
   is conservative when blind rather than optimistic.

E-stop is exposed as `ros2 service call /estop std_srvs/srv/SetBool "{data: true}"`,
as a dedicated button in the desktop console, and over the UDP fast path so it
does not queue behind video in a TCP send buffer.

### Engineering decisions worth calling out

- **Encoderless by design** — a measured duty-cycle ↔ velocity LUT
  (`config/drive_lut.yaml`) provides the feed-forward; the loop is closed by SLAM,
  not wheel ticks.
- **Pivot from VIO to lidar scan-matching** — the Stage 2 plan used Depth Anything
  as the primary mapping sensor. In practice, ViT-S 518 could not get a contiguous
  allocation from the stock 320 MB ION pool even with the BPU idle, and ViT-S 392
  tops out near 2.8 Hz — too slow to key RGB-D odometry. The RPLidar C1 took over
  odometry and obstacle sectors; the BPU models stayed on as semantic and depth
  overlays. Documented rather than hidden.
- **UDP fast path** — control and telemetry ride WebSocket; video and teleop ride
  UDP with a per-session 8-byte token, because TCP head-of-line blocking added
  visible latency to teleop.

---

## Benchmarks

Measured on the RDK X5 (TROS / ROS 2 Humble), `hobot_dnn` runtime, INT8 `.bin`
models compiled with the RDK toolchain.

| Workload | Device | Input | Inference | Pipeline rate |
|---|---|---|---|---|
| Depth Anything V2 ViT-S | **BPU** | 392×392 NV12 | ~350 ms/frame | **~2.8 FPS** |
| YOLO11m detect (bayese) | **BPU** | 640×640 NV12 | **~52 ms/frame** | ~10 FPS in the avoidance loop |
| PIDNet-S segmentation | **BPU** | 576×768 | **~9 ms/frame** | ~15 Hz loop |
| `scan_sectors` (RPLidar C1) | CPU | 16 m / 360° | — | 10 Hz |

<!-- TODO: run one `hrut_somstatus` capture and one `top` capture during a
     navigate run with both BPU models enabled, and paste the BPU utilisation
     and per-core CPU figures into this table. That single capture also serves
     as the "BPU path proof" evidence below. -->

**BPU path proof:** `depth_bpu` and `detection_bpu` both load their models via
`hobot_dnn.pyeasy_dnn.dnn.load()`, which prints the D-Robotics runtime banner and
model input/output tensor properties on load — visible in the launch console
output in the [live AI inference demo](https://youtu.be/p5Sa7evwUvI).

---

## Reproducing this

```bash
# 1. Get the code (RPLidar driver is third-party and vendored via gitignore)
git clone https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-.git ~/rdk-x5-navbot
cd ~/rdk-x5-navbot
git clone https://github.com/Slamtec/sllidar_ros2 src/sllidar_ros2

# 2. Dependencies (BPU stack requires numpy 1.x — do not let anything pull numpy 2)
sudo apt install -y ros-humble-slam-toolbox ros-humble-robot-localization ros-humble-rtabmap-odom
sudo pip3 install "numpy==1.26.4" "opencv-python-headless<4.11" smbus2

# 3. Build
source /opt/tros/humble/setup.bash
colcon build --symlink-install
source install/setup.bash            # required in EVERY shell (custom navbot_msgs)

# 4. Run — robot side
sudo ./app/agent/run_agent.sh        # or: sudo systemctl start navbot-agent

# 5. Run — PC side
cd app/desktop && pip install -r requirements.txt && python -m navbot_console
#   → connect to ws://<robot-ip>:8080
```

Board-level one-time config (`dtoverlay_pwm3` for motor PWM, `uvcvideo
quirks=128` for three UVC cameras on one bus) and the full mission walkthrough
are in [`NEXT_STEPS.md`](https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/blob/master/NEXT_STEPS.md).

---

## Links & Evidence

| Item | Link |
|---|---|
| **Stage 3 demo video** (main) | https://youtu.be/Z3CnLehWs7o |
| Extended demo — operator console POV | https://youtu.be/6Rp8K-f7oq8 |
| Live AI inference on BPU | https://youtu.be/p5Sa7evwUvI |
| Stage 3 / Stage 2 repository | https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage- |
| Stage 2 README (architecture entry point) | https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/blob/master/README.md |
| Stage 1 repository | https://github.com/MuhirwaRichard1/RDK-Challenge-Visual-Inertia-Odometry-VIO-Robot |
| Proposal (Stage 2 — concept, architecture, engineering plan) | [`PROPOSAL.md`](https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/blob/master/PROPOSAL.md) |
| Architecture & ADRs | [`docs/architecture.md`](https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/blob/master/docs/architecture.md) |
| Bill of materials & power architecture | [`docs/bom.md`](https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/blob/master/docs/bom.md) |
| Depth Anything → BPU conversion | [`docs/depth_anything_conversion.md`](https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/blob/master/docs/depth_anything_conversion.md) |
| Operator console protocol & safety model | [`docs/operator_app.md`](https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/blob/master/docs/operator_app.md) |
| Build & run from zero | [`NEXT_STEPS.md`](https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/blob/master/NEXT_STEPS.md) |
| Bench test scripts | [`bench_tests/`](https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/tree/master/bench_tests) |
| Saved demo map | [`maps/arena_20260713.*`](https://github.com/MuhirwaRichard1/RDK-X5-Second-Stage-/tree/master/maps) |

---

## Stage 3 Challenge Mapping

| Requirement | Where it is satisfied |
|---|---|
| **C1** — AI models on board | `depth_bpu` + `detection_bpu`, launched in all four bringup launch files |
| **C1** — ROS 2 communication | 7 colcon packages, custom `navbot_msgs`, graph above |
| **C1** — Motor control with safety limits | `safety_gate` (30 cm), `motor_controller` 0.2 s dead-man, `/estop`, UNKNOWN-as-blocked |
| **C1** — Sensor fusion / sync | `robot_localization` EKF over `/odom_icp` + `/imu/data`; sync approach above |
| **C1** — Quick Start + launch entrypoint | README Quick Start; `NEXT_STEPS.md`; `navbot_bringup` launch files |
| **C2** — BPU acceleration, model + runtime named | Depth Anything V2 ViT-S and YOLO11m via `hobot_dnn` |
| **C2** — Real-time continuous inference | Live AI demo video |
| **C2** — Two concurrent workloads | Depth + detection sharing one BPU, independently gated |
| **C2** — Benchmark table | Benchmarks section above |
| **C2** — Live stream with overlays | Console HUD in the extended demo video |
| **C3** — Demo video | Main demo, 3–7 min |
| **C3** — Public repo, license, docs, reproducible launch | MIT; `docs/`; reproduction steps above |
| **C3** — Technical documentation | `PROPOSAL.md`, `docs/architecture.md`, `docs/bom.md`, `docs/operator_app.md` |
| **C3** — Showcase PR | This file |

---

I agree that this showcase document may be used by the Robotics Dream Keeper
Challenge organizers as described in the official README (promotion, judging,
and archives).
