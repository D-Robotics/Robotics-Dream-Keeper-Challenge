# Sahayak — IrisBot GuardianAI

**Author:** Vishal Bharti ([@vishal7439](https://github.com/vishal7439) · [Irisrobonium on YouTube](https://www.youtube.com/@Irisrobonium))
**Stage:** Stage 2 — Build Challenge
**Project repo:** https://github.com/vishal7439/sahayak-guardian-ai
**Hardware:** D-Robotics RDK X5 (4 GB, 10 TOPS BPU)

---

## One-line summary

An offline-first, voice-controlled autonomous guardian robot on the RDK X5 that **sees, listens, thinks, moves, and speaks** — running real-time BPU-accelerated AI on-device, with an optional cloud brain for richer reasoning.

## Concept (Challenge 1)

**Scenario:** Indoor home / small office, normal lighting. Fully offline core with an optional online mode. Target detection latency < 200 ms.
**User:** Home occupants and elderly people; primary interaction is voice, with a web remote as backup. No technical skill needed.
**Core AI capabilities:** YOLOv8 perception on the BPU, Whisper offline speech-to-text, a keyword + Gemma 3 1B command router, Piper offline TTS, and a Pico 2W motor/sensor bridge with a firmware safety watchdog.
**Innovation:** A complete perception→action loop on a 4 GB board — hear a command, understand it, move to act, see with the BPU, and report back by voice. Hybrid brain (offline reflexes + optional Gemini reasoning), a firmware dead-man's-switch against real EMI-induced USB disconnects, and voice object-search (rotate → scan → announce).

## Architecture (Challenge 2)

- **System flow, module design, compute allocation:** [docs/ARCHITECTURE.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/ARCHITECTURE.md)
- **ROS 2 node graph design** (nodes, topics, message types, rates): [docs/ROS2_NODEGRAPH.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/ROS2_NODEGRAPH.md)
- **CPU affinity / real-time table** (3 pins verified from runtime logs): [docs/CPU_AFFINITY.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/CPU_AFFINITY.md)

## Engineering Plan (Challenge 3)

- **BOM:** [docs/BOM.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/BOM.md)
- **Roadmap:** [docs/ROADMAP.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/ROADMAP.md)
- **Risk analysis:** [docs/RISKS.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/RISKS.md)
- **Proposal (aggregated):** [docs/PROPOSAL.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/PROPOSAL.md)

## Measured performance

| Metric | Value |
|---|---|
| Model | YOLOv8x (640×640, NV12) on RDK X5 BPU |
| Avg latency | 174 ms |
| Avg FPS | 5.7 |
| Person detection confidence | ~0.95 |
| Motor auto-stop on disconnect | 0.6 s (firmware watchdog) |

Full benchmark: [docs/BENCHMARKS.md](https://github.com/vishal7439/sahayak-guardian-ai/blob/main/docs/BENCHMARKS.md)

## Implemented features

Manual drive · live camera · ultrasonic + DHT22 sensors · offline speech (Piper) · BPU object detection · Guard / Patrol / Follow modes · voice object-search · room check · universal voice command (Whisper + Gemma router) · online scene description and find-anything (Gemini) · firmware safety watchdog · auto-start on boot · live detection panel with confidence bars.
