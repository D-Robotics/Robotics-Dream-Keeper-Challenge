# RDK X5 Autonomous Vehicle

- **Participant:** Vishal Sharma (Pro Know)
- **Stage completed:** 3
- **Repository:** https://github.com/proknowdiy/RDK-X5-Autonomous-Vehicle
- **Community post:** https://discord.com/channels/1300358874280230994/1514335294072164432/1530945741928005832
- **Demo Video:**  https://youtu.be/OJs40JOCPPU

## Summary

RDK X5 Autonomous Vehicle is a 1/10 scale AI-powered autonomous car built using the **D-Robotics RDK X5** and **ROS 2**. The project demonstrates real-time lane following, object detection, and autonomous driving on a custom miniature road network.

The system uses the **Stereo Vision MIPI Camera** to continuously capture the road ahead. An OpenCV-based lane detection pipeline estimates the vehicle's position within the lane, while **YOLOv11** running on the RDK X5 performs real-time object detection, including stop sign and obstacle recognition.

The RDK X5 acts as the high-level decision-making unit, processing sensor data and generating steering and throttle commands. These commands are transmitted over USB serial to an **ESP32-C3**, which controls the steering servo and electronic speed controller (ESC). A RadioLink RC transmitter provides seamless switching between Manual and Autonomous driving modes for testing and safety.

The project demonstrates how classical computer vision and AI can be combined on edge hardware to create a modular and extensible autonomous driving platform for education, robotics research, and rapid prototyping.

## Technical Highlights

- **Board:** D-Robotics RDK X5 (10 TOPS Sunrise 5 BPU)
- **Vehicle Controller:** DFRobot Beetle ESP32-C3
- **AI:** YOLOv11 object detection accelerated on the RDK X5 BPU
- **Vision:** OpenCV-based lane detection with perspective transformation
- **Camera:** D-Robotics Stereo Vision MIPI Camera
- **ROS 2:** `lane_detection` → `autonomous_vehicle` → `vehicle_controller`
- **Communication:** USB Serial (UART) between RDK X5 and ESP32-C3
- **Vehicle Features:** Manual / Autonomous driving modes, lane following, stop sign recognition, obstacle detection with safe vehicle stopping

## Bill of Materials (Estimated)

| Component | Qty | Approx. Cost |
|-----------|----:|-------------:|
| D-Robotics RDK X5 | 1 | ₹18,000 |
| Stereo Vision MIPI Camera | 1 | Included |
| DFRobot Beetle ESP32-C3 | 1 | ₹900 |
| MG996R Servo | 1 | ₹500 |
| RadioLink 90A ESC | 1 | ₹2,500 |
| RS540 Brushed Motor | 1 | ₹1,000 |
| RadioLink RC6GS | 1 | ₹6,500 |
| 3S LiPo Battery | 1 | ₹1,500 |
| 1/10 RC Chassis & 3D Printed Parts | 1 | ~₹5,000 |

**Estimated Total:** ~₹35,000

## Links & Evidence

- Repository: https://github.com/proknowdiy/RDK-X5-Autonomous-Vehicle
- Demo Video: https://youtu.be/OJs40JOCPPU

---

I agree that this showcase document may be used by the Robotics Dream Keeper Challenge organizers as described in the official README (promotion, judging, and archives).