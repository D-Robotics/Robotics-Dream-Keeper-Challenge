# ClassMind — Smart Classroom Intelligence System

- **Participant:** Narendra Andhale
- **Stage completed:** 1
- **Repository:** https://github.com/Nightrider1946/classmind-rdkx5
- **Community post:** https://discordapp.com/channels/1300358874280230994/1511596144327659682/1511596144327659682

## Summary

ClassMind is an intelligent classroom management system 
built on RDK X5 that solves two real problems faced by 
every college — manual attendance and energy waste in 
empty classrooms.

The system uses a single wide-angle camera mounted at 
the front of the classroom. When attendance is triggered, 
the camera captures burst frames for 10-15 seconds while 
a buzzer signals students to face forward. The RDK X5 BPU 
runs YOLO face detection on each frame, tracks unique 
faces across all frames, splits the classroom view into 
zones, and selects the clearest image per student for 
recognition. Complete attendance for 50 students is 
generated in under 20 seconds with zero teacher effort.

The same camera continuously monitors occupancy every 
5 minutes. When zero people are detected, RDK X5 sends 
a signal via ROS 2 to an ESP32 module controlling relay 
switches for lights and fans. Everything turns back on 
automatically when students return.

Have Aim to Deploy in actual college lab at IIIT Nagpur for 
real-world validation. Stage 1 covers RDK X5 setup, 
camera integration, and basic person detection running 
on BPU.

## Technical Highlights

- **Board:** RDK X5 with 10 TOPS Sunrise 5 BPU
- **AI:** YOLO face detection on BPU + DeepFace 
  recognition on CPU with zone-based burst capture
- **Sensors:** Wide angle MIPI camera, DHT22 temperature 
  sensor, MQ2 gas sensor
- **ROS 2:** camera_node → burst_capture_node → 
  zone_splitter_node → detection_node → 
  recognition_node → attendance_node → 
  energy_node → esp32_bridge_node
- **IoT Integration:** ESP32 controls relay module 
  for lights and fan based on occupancy detection
- **Real deployment:** IIIT Nagpur college laboratory

## Links & Evidence

- SSH screenshot: [stage1_ssh_login.png](assets/stage1_ssh_login.png)
- Network proof: [stage1_network_ping.png](assets/stage1_network_ping.png)
- Camera setup: [stage1_camera_setup.png](assets/stage1_camera_setup.png)
- YOLO on board: [stage1_yolo_bpu.png](assets/stage1_yolo_bpu.png)
---

I agree that this showcase document may be used by 
the Robotics Dream Keeper Challenge organizers as 
described in the official README (promotion, judging, 
and archives).
