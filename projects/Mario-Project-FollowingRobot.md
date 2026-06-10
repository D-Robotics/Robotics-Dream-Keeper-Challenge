# Following Robot

- **Participant:** Mario
- **Stage completed:** 1
- **Repository:** https://github.com/lavinama/rdk-x5-following-robot
- **Demo video:** https://github.com/lavinama/rdk-x5-following-robot/blob/main/docs/images/challenge2-act-led-blink.mp4
- **Community post:** https://discord.com/channels/1300358874280230994/1511058147350151168/1514414310296649849

## Summary

This project is building a vision-only person-following robot: a Yahboom ROSMASTER M1
mecanum base driven by a D-Robotics RDK X5 (4 GB, 10 TOPS BPU). Stage 1 brings the brain
to life, board-only, while the base kit ships.

I flashed RDK OS 3.5.0 Desktop (Ubuntu 22.04, arm64) to a 64 GB microSD with RDK Studio
on macOS and brought the board up over the QuickLink USB-C port — the gadget network has
no DHCP server, so the Mac side gets a static `192.168.128.x` address, then
`ssh sunrise@192.168.128.10`. Two real-world gotchas are documented in the repo: the X5's
two Type-C ports split power and data (one cable can't do both jobs), and the battery-less
RTC wakes up in the year 2000, silently breaking TLS until NTP syncs.

With the M1 kit (and its camera) still in transit and no discrete components on hand, the
sensor challenge went pure sysfs: a Python script reads the SoC's DDR and CPU thermal
sensors every 500 ms while blinking the onboard ACT LED at 1 Hz — borrowing it from the
kernel heartbeat trigger and restoring it on exit.

For the first AI task, YOLO11n detect from rdk_model_zoo ran on-device against the bundled
`bus.jpg`: bus 0.93 and four persons detected, with a **13.1 ms forward pass (~76 FPS) on a
single BPU core** — comfortable headroom for the real-time person-following pipeline
(`camera → YOLO (BPU) → follow controller → cmd_vel`) planned for Stages 2–3.

## Technical Highlights

- **OS / bring-up:** RDK OS 3.5.0 Desktop (Ubuntu 22.04 arm64), MD5-verified, flashed via RDK
  Studio on macOS; fully headless — no monitor or keyboard ever attached.
- **Networking:** USB-C gadget SSH first (static host IP, no DHCP on the gadget), then Wi-Fi
  joined entirely over that SSH session with `nmcli`.
- **Sensor task (sysfs interface):** DDR + CPU thermal zones (`/sys/class/thermal/thermal_zone{0,1}`)
  read in Python while toggling the onboard ACT LED (`/sys/class/leds/ACT`) —
  [`scripts/challenge2_sensor.py`](https://github.com/lavinama/rdk-x5-following-robot/blob/main/scripts/challenge2_sensor.py).
- **On-device AI:** YOLO11n detect (`yolo11n_detect_bayese_640x640_nv12.bin`, NV12 640×640) on
  the X5's Bayes-e BPU — load 149 ms, pre-process 12.1 ms, **forward 13.1 ms (~76 FPS)**,
  post-process 10.0 ms; one BPU core, ~52 °C.
- **Next:** ROS 2 person-follower on the ROSMASTER M1 — BPU YOLO at the core, voice command
  + e-stop as the second workload, headless on 4 GB.

## Links & Evidence

- Screenshot A — flash + SSH session: [`docs/images/screenshot-A-ssh.png`](https://github.com/lavinama/rdk-x5-following-robot/blob/main/docs/images/screenshot-A-ssh.png)
- Screenshot B — ACT LED + thermal log clip: [`docs/images/challenge2-act-led-blink.mp4`](https://github.com/lavinama/rdk-x5-following-robot/blob/main/docs/images/challenge2-act-led-blink.mp4)
- Screenshot C — detection log + annotated output: [`docs/images/screenshot-C-detect-log.png`](https://github.com/lavinama/rdk-x5-following-robot/blob/main/docs/images/screenshot-C-detect-log.png) · [`docs/images/yolo11n-bus-result.jpg`](https://github.com/lavinama/rdk-x5-following-robot/blob/main/docs/images/yolo11n-bus-result.jpg)
- Board photo: [`docs/images/board-bringup.jpg`](https://github.com/lavinama/rdk-x5-following-robot/blob/main/docs/images/board-bringup.jpg)
- Stage 1 working doc (flash notes, commands, dependency list): [README](https://github.com/lavinama/rdk-x5-following-robot/blob/main/README.md)

---

I agree that this showcase document may be used by the Robotics Dream
Keeper Challenge organizers as described in the official README
(promotion, judging, and archives).
