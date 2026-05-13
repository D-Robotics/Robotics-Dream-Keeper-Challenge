# RDK X5 Quick Guide (Challenge-Aligned)

This guide is **short by design**. It maps **minimum practical skills** to each challenge stage. For deep dives, follow **[official D-Robotics RDK X5 documentation](https://developer.d-robotics.cc/en/documentation)** and the sample repo **[rdk_model_zoo](https://github.com/D-Robotics/rdk_model_zoo)**.

---

## 1. What You Should Know First

| Topic | Why it matters |
|--------|----------------|
| **RDK Studio** | Flash, monitor serial logs, and manage images reliably. |
| **Ubuntu on RDK** | Most demos assume Linux commands and systemd services. |
| **BPU** | On-board acceleration for vision / selected models — required in Stage 3. |
| **ROS 2 Humble (typical)** | Stage 2–3 architecture expects nodes, topics, and launch files. |

---

## 2. Stage 1 — Minimum Hands-On Path

### 2.1 Flash & First Boot

1. Install **[RDK Studio](https://developer.d-robotics.cc/en/rdkstudio)** on your PC (version per official release notes).  
2. Download the **recommended OS image** for RDK X5 for the challenge season.  
3. Connect board power and USB/UART as documented; enter flash mode if required.  
4. Flash; wait until **verify** step completes; reboot.

> **Tip:** Keep a photo of RDK Studio showing **successful completion** for your submission.

### 2.2 Network & SSH

On your PC:

```bash
ping 192.168.127.10
```

```bash
ssh <user>@<board-ip>
```

Enable SSH if disabled (method depends on image; follow official guide).

### 2.3 Camera Sanity Check (example pattern)

Exact commands vary by driver stack. Typical flow:

```bash
# Example placeholders — replace with your image's documented tool
ls /dev/video*
v4l2-ctl --list-devices   # if v4l-utils is installed
```

Use the **official** camera demo or a ROS 2 camera node as proof for Stage 1.

### 2.4 Run a Packaged AI Demo

```bash
# Pseudocode — use the path from rdk_model_zoo or official samples
cd ~/workspace/rdk_model_zoo
# follow that repo's README for env setup and run script
```

Capture **screenshots** of inference output for submission.

---

## 3. Stage 2 — Design on RDK Constraints

When you draw your architecture, explicitly note:

- **Which models** run on **BPU** vs CPU.  
- **Camera resolution** vs **FPS** target.  
- **ROS 2 executor** model (single vs multi-threaded) for your graph.

---

## 4. Stage 3 — Performance & Reliability

- Record **FPS / latency** at the **operating resolution**.  
- Log **CPU and BPU** utilization using official tools where available.  
- Document **thermal throttling** mitigation (fan, duty cycle reduction, model switch).

---

## 5. Screenshot Checklist (for Docs / PRs)

- Terminal showing **version** and **run command**.  
- **RVIZ** or overlay window if applicable.  
- **Error-free** console tail for the demo segment you show in video.

---

## 6. Where to Get Help

- Challenge **Discord** (see main [README.md](../README.md)).  
- **[FAQ](./faq.md)** for access and PR issues.  
- Official **GitHub org**: search **D-Robotics** for platform repos.
