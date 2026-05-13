# Stage 3 — Launch Challenge

| Field | Value |
|--------|--------|
| **Suggested duration** | 20 days |
| **Slogan** | Bring Your Robot to Life |
| **Hardware baseline** | D-Robotics **RDK X5** + sensors/actuators per your Stage 2 plan |

---

## Core Objective

Deliver a **working, demonstrable, shareable** AI / robotics demo: integrated stack, **BPU-accelerated** real-time perception, multi-task execution where applicable, and professional packaging (video, repo, docs, live walkthrough).

---

## Challenges

### Challenge 1 — Prototype Integration

Integrate **all** of the following into one coherent system:

- **AI model(s)** on board  
- **ROS 2** communication between relevant nodes  
- **Motor / actuator control** with safety limits documented  
- **Sensor fusion** or multi-sensor timing (e.g. camera + IMU, or mic + vision) with a short description of sync approach

**Standard of completion:**  
- `README.md` **Quick Start**: clone → build → launch commands.  
- **Launch file** or script entrypoint documented.  
- **Safe shutdown / e-stop** behavior described if motors are involved.

---

### Challenge 2 — Real-Time AI Inference

Your demo **must** clearly exhibit:

- **BPU acceleration** for at least one major perception model (state model name and runtime).  
- **Real-time detection or equivalent** (continuous stream, not single-frame only).  
- **Multi-task** capability: e.g. detection + tracking, or detection + light control, or ASR + UI update — **two concurrent workloads** with brief CPU/BPU notes.

**Standard of completion:**  
- Short **benchmark table** in `docs/` (resolution, FPS or latency, model name, tool versions).  
- **Screenshot or short clip** of live stream with overlays.

---

### Challenge 3 — Final Demo & Packaging

Produce a **release-grade** presentation package:

- **Demo video** (see deliverables)  
- **GitHub repository** (tags recommended, e.g. `v1.0-demo`)  
- **Technical documentation** (`docs/`: architecture, calibration, known issues)  
- **Live online demo** — scheduled session or async video walkthrough per season rules

**Standard of completion:** Video shows **end-to-end** story in **one take** or clearly edited sections; README links all assets.

---

## Core Skills (Competency Checklist)

Map to the full **robot deployment & optimization** competency table:

- End-to-end bring-up and reproducible builds  
- Model conversion / deployment best practices for RDK  
- ROS 2 performance tuning (QoS, executor, intra-process if used)  
- System profiling and thermal / power awareness  

---

## Deliverables (Submission Spec)

Submit the **full project bundle** checklist (organizers may automate verification):

| # | Item | Requirement |
|---|------|-------------|
| 1 | **Demo video** | **3–7 minutes**, 1080p preferred; show hardware, UI/overlay, and 30+ s continuous AI run. |
| 2 | **GitHub repo link** | Public; includes license, README, `docs/`, reproducible launch. |
| 3 | **Technical PDF or Markdown** | Architecture, interfaces, calibration, failure recovery. |
| 4 | **Benchmark / evidence** | BPU path proof (tool log or documented flag). |
| 5 | **Community post** | Announcement + embedded media or links. |
| 6 | **Showcase PR** | File in `projects/` linking to all of the above. |

**Format rules**

- Video: YouTube **public** or **unlisted** with stable link.  
- Code: pin dependency versions where possible.  
- No secrets in repo (Wi-Fi passwords, keys).

---

## Completion Rewards

- Title: **Robotics Dream Keeper**  
- **Global Top Creators** (seasonal ranking): additional prizes such as **Shenzhen innovation trip**, **official showcase**, **DGP incubation** consideration, **developer spotlight interview**, and **official collaboration** pathways — exact list per **season announcement**.

---

## Reference

- Sample models and pipelines: [rdk_model_zoo](https://github.com/D-Robotics/rdk_model_zoo)
