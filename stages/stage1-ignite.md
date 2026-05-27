# Stage 1 — Ignite Challenge

| Field | Value |
|--------|--------|
| **Suggested duration** | 10 days |
| **Slogan** | Power On Your AI Robot's Brain |
| **Hardware baseline** | D-Robotics **RDK X5** |

---

## Core Objective

Go from **first contact with RDK X5** to **independently running your first on-device AI task** (flash → network → SSH → community → sensors → chosen AI task).

---

## Challenges

### Challenge 1 — Board Bring-Up (“Wake the board”)

Complete the following **in order**, with evidence ready for submission:

1. **System image flash**  
   - Use **RDK Studio** (or the officially documented equivalent workflow) to flash a **supported OS image** for RDK X5.  
   - **Standard of completion:** Device boots successfully after flash; serial / display output shows a normal login prompt or desktop as documented.

2. **Network connectivity**  
   - Configure **Ethernet or Wi-Fi** so the board obtains a valid IP and can reach the public internet (e.g. DNS resolution works).  
   - **Standard of completion:** From the board, you can `ping` a public host or `curl` an HTTPS endpoint successfully.

3. **SSH login**  
   - Enable and use **SSH** from your PC to the board with a known user.  
   - **Standard of completion:** You can open an interactive shell remotely and run commands (e.g. `uname -a`, `htop`).

4. **Community connection**  
   - Join the **official challenge / RDK Discord (or other announced channel)** and complete any **self-introduction or Stage 1 check-in** template posted by organizers.  
   - **Standard of completion:** A **shareable link** to your check-in post or thread (permalink).

---

### Challenge 2 — Sensor Explorer

Demonstrate hands-on I/O by **successfully driving or reading** at least **one** of the following categories on RDK X5 (hardware or officially supported accessory as applicable):

- Camera  
- IMU  
- GPIO  
- Microphone  
- Motor (driver + motor or actuator kit)

**Standard of completion:**  
- A **short log or photo** showing the sensor/actuator responding (e.g. camera preview window, IMU stream values, GPIO toggling LED, mic level meter, motor spin).  
- Brief note in your repo **which interface** you used (e.g. MIPI, I2C, sysfs, ROS 2 node name).

---

### Challenge 3 — First AI Task

Pick **exactly one** of the following and run it **on the board** with a visible result:

- **YOLO** object detection  
- **Image classification**  
- **Face recognition**  
- **Speech recognition**

**Standard of completion:**  
- Model loads and runs **on-device** (not only on PC).  
- Output is **observable** (annotated image, terminal labels, or recognized text).  
- Reference implementation may start from **[rdk_model_zoo](https://github.com/D-Robotics/rdk_model_zoo)** or official docs, but your submission must show **your** run.

---

## Core Skills (Competency Checklist)

Align your study plan with the official **stage competency table** (published with the campaign). Stage 1 typically covers:

- RDK Studio flash and recovery basics  
- Linux shell, systemd basics, and package install on board  
- Network, SSH, and file transfer (`scp` / `rsync`)  
- Camera / sensor bring-up at demo level  
- Running a **BPU / board-supported** vision or speech demo end-to-end  

---

## Deliverables (Submission Spec)

Submit **all** of the following (links must be **publicly accessible** unless organizers announce a private upload channel):

| # | Item | Requirement |
|---|------|-------------|
| 1 | **Screenshot A** | RDK Studio (or documented tool) **after successful flash**, plus **SSH session** logged into the board (same or composite image is acceptable if legible). |
| 2 | **Screenshot B** | Proof of **sensor / actuator** activity from Challenge 2. |
| 3 | **Screenshot C** | **AI task running on board** (e.g. detection visualization, classification output, face ID UI, or ASR transcript). |
| 4 | **GitHub repository** | Your **personal** repo with README: flash notes, network/SSH steps, sensor command or launch line, AI run command, and dependency list. |
| 5 | **Community post** | Permalink to your **Stage 1 completion** or showcase thread. |

**Format rules**

- Images: **PNG or JPG**, readable text, **1080p width** recommended.  
- Videos (optional): **≤ 3 minutes**, 720p+, clear capture of screen or camera output.  
- Links: no expired shares; prefer GitHub / YouTube / permanent Discord permalink.

**PR to this repo:** add your showcase file under `projects/` per [projects/README.md](../projects/README.md) and [docs/github-pr-guide.md](../docs/github-pr-guide.md).

---

## Completion Rewards

- Title: **RDK Explorer**  
- Unlocks **Stage 2** eligibility  
- **Bootcamp Badge** and **dedicated Discord role** (as announced for the season)
