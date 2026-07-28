# BETL-001

- **Participant:** IsaacRF
- **Stage completed:** 3
- **Repository:** https://github.com/RFisaac/BETL-001
- **Demo video:** https://youtu.be/_VSdEOZrcUg
- **Community post:** https://discord.com/channels/1300358874280230994/1509220927462969575/1514489192158330931

## Summary

BETL-001 ("BPU-Education, Transport, Lidar" — pronounced "Beetle") is my scratch-built,
bio-inspired expressive lidar mobile robot on the D-Robotics RDK X5. For Stage 3, it drives
under Steam Deck teleop, runs YOLOv8n object detection on the BPU concurrently with a
CPU-side dead-reckoning and lidar-reprojection pipeline, and builds a 3D point cloud from a
single 2D RPLIDAR A1 canted 90° on an encoder-driven rotating stage — a "poor-man's
spherical scanner" reprojected through the live TF chain.

The two required concurrent workloads run on physically separate compute: **YOLOv8n INT8 on
the BPU** and **wheel+gyro odometry plus lidar-cloud accumulation on the CPU**. Measured
benchmark: BPU inference held at 7.25 ms mean isolated vs. 7.47 ms mean under concurrent
CPU load — a 3% delta — while CPU-side pipeline stages degraded 19–39% over the same run,
which is the concurrent-workload story in one number.

I'm being explicit that pose comes from **wheel+gyro dead reckoning, not SLAM**: RTAB-Map
was the original plan, but enabling RealSense depth for it pushed the board to a 16.4 load
average and broke everything downstream, so it was pulled from the live chain in favor of a
simpler, working dead-reckoning pipeline. RTAB-Map's CPU cost is still what the concurrent
benchmark load represents. Safety is a single arbitrated path to the four drive motors —
Deck deadman, command watchdog, a software e-stop topic, the ODrive firmware watchdog, and
an independent hardware e-stop, in that order.

This submission is going up after the stated Stage 3 window closed — the honest status is
in the repo, not smoothed over here.

## Technical Highlights

- **Board:** RDK X5, Ubuntu 22.04 / ROS 2 Humble (tros.b)
- **AI:** YOLOv8n INT8, 640×640, on the BPU via `dnn_node_example`, fed from RealSense RGB
- **Concurrent workloads:** BPU detection + CPU dead-reckoning/lidar-reprojection, on
  separate compute units (see Architecture below)
- **Drivetrain:** 4-wheel skid steer, 4× Flipsky ODESC V4.2 (USB), closed-loop velocity
  control only, wheel-speed clamp applied proportionally across both sides
- **Odometry:** ODESC wheel encoders (forward velocity) + RealSense D435i gyro (yaw rate),
  fused with a zero-velocity-update bias tracker — dead reckoning, no loop closure
- **Lidar:** RPLIDAR A1 canted 90° on a stepper-driven rotating stage, reprojected to 3D
  via a single TF lookup per scan segment
- **Safety:** hardware e-stop + software `/estop` + Deck deadman + command watchdog + ODrive
  firmware watchdog, single arbitrated path to the motors
- **Bring-up:** gated supervisor (`beetle_up.py`) that verifies each stage before starting
  the next, rather than a fixed-delay launch file
- **Benchmark:** BPU inference 7.25 ms mean isolated → 7.47 ms mean concurrent (+3%); CPU
  pipeline stages −19% to −39% over the same run

## Links & Evidence

- **Architecture, TF chain, node graph:** https://github.com/RFisaac/BETL-001/blob/main/docs/architecture.md
- **Measured calibration constants:** https://github.com/RFisaac/BETL-001/blob/main/docs/calibration.md
- **Benchmark methodology + tables:** https://github.com/RFisaac/BETL-001/blob/main/docs/benchmark.md
- **Known issues / failure recovery:** https://github.com/RFisaac/BETL-001/blob/main/docs/failure-recovery.md
- **Stage 2 proposal (Challenges 1–3, diagrams, BOM, risks):** https://github.com/RFisaac/BETL-001/blob/main/PROPOSAL.md
- **Stage 1 documentation:** https://github.com/RFisaac/BETL-001#stage-1--ignite-challenge

## Benchmark table

| Metric | Isolated | Concurrent | Δ |
|---|---|---|---|
| BPU inference (`predict_infer`), mean | 7.25 ms | 7.47 ms | +3.0% |
| `preprocess` (CPU), mean | 8.85 ms | 12.29 ms | +38.9% |
| `postprocess` (CPU), mean | 4.21 ms | 5.03 ms | +19.5% |
| Node-reported FPS, mean | 23.3 | 18.18 | −22.0% |

Model: YOLOv8n INT8, 640×640. Concurrent load: `rtabmap_ros` (installed, not driving the
live map — see `docs/architecture.md` for why). BPU utilization independently confirmed via
a 120-sample `hrut_somstatus` trace, `bpu0` steady in a 4–8% band throughout. Raw evidence
(`bench/*.json`, `logs/bpu_proof.log`, `logs/versions.txt`) is committed in the repo. Full
per-stage p95/median/min/max and the reproduction script:
[`docs/benchmark.md`](https://github.com/RFisaac/BETL-001/blob/main/docs/benchmark.md).

## License

Apache License 2.0.

---

I agree that this showcase document may be used by the Robotics Dream Keeper Challenge organizers as described in the official README (promotion, judging, and archives).
