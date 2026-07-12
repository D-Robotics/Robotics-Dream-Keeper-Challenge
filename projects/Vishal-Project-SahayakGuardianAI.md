# Sahayak — IrisBot GuardianAI

**Author:** Vishal Bharti ([@vishal7439](https://github.com/vishal7439) · [Irisrobonium on YouTube](https://www.youtube.com/@Irisrobonium))
**Stage:** Stage 3 — Launch Challenge (v1.0-demo)
**Project repo:** https://github.com/vishal7439/sahayak-guardian-ai
**Demo video:** https://youtu.be/oQaQGBd4iME
**Hardware:** D-Robotics RDK X5 (4 GB, 10 TOPS BPU)

---

## One-line summary
An offline-first, voice-controlled autonomous guardian robot on the RDK X5 that **sees, listens, thinks, moves, and speaks** — running real-time BPU-accelerated AI on-device, with an optional cloud brain for richer reasoning.

## Concept
**Scenario:** Indoor home / small office, normal lighting. Fully offline core with an optional online mode. Target detection latency < 200 ms.
**User:** Home occupants and elderly people; primary interaction is voice, with a web remote as backup. No technical skill needed.
**Core AI capabilities:** YOLOv8n perception on the BPU, Whisper offline speech-to-text, a keyword + Gemma 3 1B command router, Piper offline TTS, and a Pico 2W motor/sensor bridge with a firmware safety watchdog.
**Innovation:** A complete perception→action loop on a 4 GB board. Hybrid brain (offline reflexes + optional Gemini reasoning), a firmware dead-man's-switch plus server-side auto-reconnect against real EMI-induced USB disconnects, ultrasonic+vision distance fusion, and a hands-free wake-word mode.

## Stage 3 — What's new since Stage 2
- **Ultrasonic + YOLO distance fusion** — "person, ~0.6 m ahead"
- **Explore mode** — rotate, scan, announce objects one-by-one with distance
- **Offline conversational persona** (Gemma 3 1B) for natural replies
- **Hands-free mode** — continuous wake-word ("IrisBot") listening loop
- **Pico EMI auto-reconnect** — recovers dropped USB without restart
- **Real ROS 2 (Humble) nodes** — BPU detections on /sahayak/detections (verified)
- **systemd auto-start** for the robot server and offline Gemma LLM

## Architecture & Engineering
- **System flow / module design / compute allocation:** [docs/ARCHITECTURE.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/ARCHITECTURE.md)
- **ROS 2 node graph design:** [docs/ROS2_NODEGRAPH.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/ROS2_NODEGRAPH.md)
- **ROS 2 running nodes + rationale:** [docs/ROS2_HYBRID.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/ROS2_HYBRID.md) · evidence: [ros2/ROS2_EVIDENCE.txt](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/ros2/ROS2_EVIDENCE.txt)
- **CPU affinity / real-time table:** [docs/CPU_AFFINITY.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/CPU_AFFINITY.md)
- **BOM:** [docs/BOM.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/BOM.md) · **Roadmap:** [docs/ROADMAP.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/ROADMAP.md) · **Risks:** [docs/RISKS.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/RISKS.md) · **Proposal:** [docs/PROPOSAL.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/PROPOSAL.md)

## Measured performance
| Metric | Value |
|---|---|
| Model | YOLOv8n (640×640, NV12) on RDK X5 BPU |
| Avg latency | 174 ms |
| Avg FPS | 5.7 |
| Person detection confidence | ~0.95 |
| Motor auto-stop on disconnect | 0.6 s (firmware watchdog) |

Full benchmark: [docs/BENCHMARKS.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/BENCHMARKS.md)

## Implemented features
Manual drive · live camera · ultrasonic + DHT22 sensors · offline speech (Piper) · BPU object detection · Guard / Patrol / Follow / Explore / Check Room modes · distance fusion · voice object-search · hands-free wake-word mode · offline conversational persona (Gemma) · universal voice command (Whisper + Gemma router) · online scene description and find-anything (Gemini) · ROS 2 detection nodes · firmware safety watchdog + EMI auto-reconnect · systemd auto-start · live detection panel with confidence bars.

## Agreement
I agree that this showcase document may be used by the Robotics Dream Keeper Challenge organizers as described in the official README (promotion, judging, and archives).
