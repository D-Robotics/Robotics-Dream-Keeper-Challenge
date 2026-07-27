Vision Based Cleaner Robot - Stage 2 Proposal 
Participant : Sandun
Stage Completed: 2
Repository : https://github.com/SandunGitHub/D-Robotics/tree/main/Stage%202

Stage 2 — Build up your own intelligent robot
Challenge 2 – Build Challenge
Challenges
Scenario: 
#### Operating environment and constraints
-	Indoor floors such as flat floors, narrow corridors and doorways, furniture and dynamic obstacles, stairs and drop-offs.
-	Reliable operation under vacuum cleaner green LED condition (20-50 lux).
-	Inference performance:  >= 15 FPS for real-time dust detection.
-	Maximum end-to-end latency: ~30 s  latency from image capture to power motor adjustment. 

##### Who benefits and primary interaction mode
-	Target Users: cleaners having disabilities, elderly individuals who may have difficulty in regular cleaning, non-commercial cleaning services.
-	Interaction Mode: IR-based remote controller.
-	Success Criterion: User can initiate a cleaning task and robot completes it without any further intervention.
##### Core AI capabilities: 
1.	Real-time dust and debris detection via a computer vision model.
2.	Classify the dust density as low, medium, or high.
3.	Intelligent adjustment of vacuum power based on the nature of cleaning load.
Innovation/differentiation
Adaptive cleaning strategy that balances cleaning performance, battery life and operational efficiency.
#####  Success Criteria
-	Dust detection accuracy: 
-	Vacuum power adjustment latency:
-	Battery power utilization:
-	Cleaning task completion:
-	Obstacle avoidance rate:

Challenge 2 – AI System architecture
1.	System flow diagram
a.	Non-AI system
i.	Path control and obstacle avoidance

<img width="821" height="572" alt="image" src="https://github.com/user-attachments/assets/0c190fdf-7f1e-4bce-8116-53b245fee5bd" />


b.	AI-System
i.	Dust detection and mapping algorithm

<img width="575" height="371" alt="image" src="https://github.com/user-attachments/assets/de849035-ca90-4e54-85ae-7f9a9390b685" />

2.	Module Design

<img width="940" height="770" alt="image" src="https://github.com/user-attachments/assets/2c40b081-ac3c-4400-bb7f-eb268a85632d" />

3.	ROS2 Node graphs and Compute Allocation

<img width="803" height="457" alt="image" src="https://github.com/user-attachments/assets/7a80e317-3e6d-4d69-a5a6-861876c26567" />

## Software Module Allocation

| Major Module | Execution Unit | Compute Unit | Real-Time Requirement | Recommended Timing / Priority |
|--------------|----------------|--------------|-----------------------|-------------------------------|
| YOLOv5 Dirt Detection | Separate ROS 2 process | BPU | Soft real-time | Process the newest frame; avoid building a frame backlog. |
| Dirt Tracking / Decision Logic | Separate ROS 2 process (or callback within the detection node) | BPU | Soft real-time | Update movement decisions within **100–300 ms**. |
| IR Decoder Node | Separate ROS 2 process with a GPIO-reading thread | Arduino Nano | Firm real-time | Decode button presses within **50–100 ms**. |
| Ultrasonic Obstacle Sensor Node | Separate ROS 2 process | CPU | Firm real-time | Update at **10–20 Hz**; reject stale measurements. |
| Command / Mode Manager | Separate ROS 2 process | CPU | Firm real-time | Select manual, autonomous, or emergency commands within **20–50 ms**. |
| Motion Controller | Separate ROS 2 process | CPU | Firm real-time | Execute the control loop at **20–50 Hz** with low timing jitter. |
| Motor Driver Interface | Dedicated thread or separate process | CPU | Firm real-time | Apply stop and direction commands within **20 ms**. |
| Wheel Feedback / Encoder Reader *(optional)* | Dedicated thread | CPU | Firm real-time | Sample encoder data at **50–100 Hz**. |
| ROS 2 Logging and Diagnostics | Shared background process/thread | CPU | Non-real-time | Must not interfere with sensing or motion control tasks. |
| ROS 2 DDS Communication | Middleware-managed threads | CPU | Soft real-time | Use the default scheduler; consider CPU affinity only after system profiling. |

Challenge 3 — Engineering Plan

## Bill of Materials (BOM)

| Item | Qty | Supplier / SKU | Voltage | Interface | Notes |
|------|:---:|----------------|---------|-----------|------|
| RDK X5 Development Board | 1 | D-Robotics RDK X5 | 5 V DC | USB, Ethernet, GPIO, MIPI CSI | Main embedded AI computer running Ubuntu and ROS 2 |
| MicroSD Card (32–64 GB) | 1 | SanDisk Ultra / Samsung EVO | N/A | SDIO | Stores Ubuntu, ROS 2, and application software |
| USB Camera | 1 | Logitech | 5 V | USB | Captures images for YOLOv5 dirt detection |
| IR Receiver Module (38 kHz) | 1 | Keyestudio | 3.3–5 V | GPIO | Receives NEC infrared remote commands |
| IR Remote Controller | 1 | Keyestudio | Battery | Infrared | Manual robot control |
| DC Geared Motors | 4 | Robot Chassis Motors | 6–12 V | PWM | Drives the four-wheel mobile platform |
| RoboClaw Motor Driver | 1 | RoboClaw 2×15A | 6–30 V | UART | Controls left and right motor pairs |
| 4WD Robot Chassis | 1 | Acrylic/Metal Chassis | N/A | Mechanical | Supports all electronics and mechanical components |
| Wheels | 4 | Compatible with Chassis | N/A | Mechanical | Four drive wheels |
| Battery Pack | 1 | Li-ion/LiPo | 12 V | Power | Primary power source |
| DC–DC Buck Converter | 1 | LM2596 Module | 12 V → 5 V | Power | Supplies regulated 5 V power to the RDK X5 |
| Connecting Wires | 1 Set | Dupont/JST Kit | N/A | GPIO / Power | Electrical connections between modules |
| Mounting Hardware | 1 Set | M3 Screws, Nuts, Standoffs | N/A | Mechanical | Secures electronic and mechanical components |


Link to the timeline : https://github.com/SandunGitHub/D-Robotics/blob/main/Stage%202/ROADMAP.md

Please note that due to late arrival of some products the project repo is still being developed and soon will be updated under Stage 2/project/ on 19th of July 2026.








