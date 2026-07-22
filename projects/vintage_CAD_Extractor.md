# Interactive AI CAD Extractor — Stage 3 Launch (Final Demo)

* **Participant:** Physic69
* **Stage completed:** 3
* **Stage 3 Repository:** https://github.com/Physic69/RDK-Challenge
* **Stage 1 & 2 History:** https://github.com/Physic69/RDK-X5-Challenge
* **Demo video:** <INSERT_YOUTUBE_LINK_HERE>

> All images below are hosted on the participant's own repository and are embedded by URL — no image assets are added to the challenge repository.

---

## Summary

**Interactive AI CAD Extractor** is a real-time, multi-task edge computing pipeline built on the **D-Robotics RDK X5**. It transforms physical objects into 3D digital assets by simultaneously running monocular depth estimation and open-vocabulary object detection.

Using a BPU-accelerated **YOLO-World** model, it detects specific target prompts (such as a mouse, bottle, or keyboard) via an offline JSON vocabulary. Concurrently, a BPU-accelerated **MiDaS Small** model generates a dense depth map. A Human-in-the-Loop (HITL) CPU node intercepts this ROS 2 data stream, halts the terminal to prompt the user, and uses a custom "cookie-cutter" algorithm to extrude the target's depth ROI into a perfectly formatted `.obj` CAD file directly to local storage.

For Stage 3, the system is a complete prototype featuring dynamic open-vocabulary filtering, dual-model BPU scheduling, and a resilient ROS 2 data flow that safely handles multi-object NMS arrays without crashing.

---

## Challenge 1 — Prototype Integration

A single coherent system integrating on-board AI, ROS 2, digital actuation, and sensor fusion.

| Requirement                    | How it is met                                                                                                                                   |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **AI model(s) on board**       | YOLO-World (Open-Vocabulary) + MiDaS Small (Depth), both BPU-accelerated on the RDK X5                                                          |
| **ROS 2 communication**        | Nodes communicate over standard `sensor_msgs/Image` and serialized JSON `std_msgs/String` topics                                                |
| **Digital actuation / limits** | Extracts spatial data to local disk as a `.obj` file; safely drops vocabulary targets that lack offline embeddings to prevent inference crashes |
| **Sensor fusion**              | RGB vision fused with AI-inferred spatial depth mappings to create 3D vertices                                                                  |
| **Quick-start README**         | [README.md](https://github.com/Physic69/RDK-Challenge/blob/main/README.md) with clone, build, and launch instructions                           |
| **Documented launch file**     | Launch pipeline consists of two distinct nodes: `vision_node` (Perception) and `cad_writer_node` (Interactive Extrusion)                        |

---

## Challenge 2 — Real-Time AI Inference

* **BPU acceleration:** The primary perception model, **YOLO-World** (`yolo_world.bin`), runs on the RDK X5 BPU via the modern `hbm_runtime`. The depth estimation model, **MiDaS Small** (`midas_small_256_compiled.bin`), runs concurrently on the BPU via the legacy `pyeasy_dnn` interface.
* **Two concurrent workloads:** (1) Continuous monocular depth mapping and dynamic bounding-box generation running on the BPU; (2) an interactive CPU-bound generation loop that awaits keyboard input to calculate Z-axis thickness and plot 3D vertices into a `.obj` file.

### Interactive Workflow

The terminal dynamically prompts the operator based on live BPU detections:

```text
========================================
TARGETS DETECTED IN WORKSPACE:
========================================
[0] BOTTLE (Confidence: 0.87)
[1] MOUSE (Confidence: 0.62)
[2] CANCEL / RE-SCAN

Select target ID to extract CAD [0-2]: 0
[INFO] Extracting bottle...
[INFO] Calculating spatial vertices...
[SUCCESS] Saved 3D asset with 2450 vertices to /root/bottle_extracted.obj
```

Full benchmark data, including resolution, FPS/latency, model names, and tool versions, is available in `docs/architecture.md`.

---

## Challenge 3 — Final Demo & Packaging

| Deliverable                | Link                                                                                                  |
| -------------------------- | ----------------------------------------------------------------------------------------------------- |
| Demo video (YouTube)       | TO ADD                                                                           |
| GitHub repository (public) | https://github.com/Physic69/RDK-Challenge                                                             |
| Technical documentation    | docs/architecture.md                                                                                  |
| Benchmark evidence         | Terminal logs confirm dual `[BPU_PLAT] soc info(x5)` scheduling across `hbm_runtime` and `pyeasy_dnn` |

### Pipeline Startup Order

| Order | Node                           | Purpose                                                                      |
| ----- | ------------------------------ | ---------------------------------------------------------------------------- |
| 1     | `rdk_vision_pkg vision_node`   | BPU depth-map generation and open-vocabulary YOLO-World bounding boxes       |
| 2     | `cad_extruder cad_writer_node` | Interactive HITL terminal, mask isolation, and `.obj` point-cloud generation |

---

## Technical Highlights

* **Models:** YOLO-World (BPU, Open-Vocabulary) + MiDaS Small 256×256 (BPU).
* **Perception → Generation:** RGB feed → dual inference → ROS 2 JSON serialization → grayscale ROI extraction → Z-axis scalar mapping → `.obj` file write.
* **Safety & Recovery:** Explicit handling of NumPy NMS array ambiguities (`len(keep) == 0`) and automatic pre-flight vocabulary checks to strip unavailable text prompts before they reach BPU memory allocation.

---

## Links & Evidence

* **Repository & README:** https://github.com/Physic69/RDK-Challenge
* **Demo video:** <INSERT_YOUTUBE_LINK_HERE>
* **Technical docs & benchmarks:** https://github.com/Physic69/RDK-Challenge/blob/main/docs/architecture.md

---

## Declaration

I agree that this showcase document may be used by the Robotics Dream Keeper Challenge organizers as described in the official README, including for promotion, judging, and archival purposes.
