# Tactile-Adaptive Multi-Fingered Hand for Autonomous Object Grasping

## Abstract
A robotic hand assembly for humanoid robots provides autonomous grasping of varied objects and social gestures. The hand incorporates distributed tactile sensor arrays on each phalanx, a compliant tendon drive system, and an onboard processor that fuses vision and touch data to select grasp types. Force thresholds adapt based on object compliance detected during initial contact. Failure detection triggers re-grasp sequences without external intervention.

## Problem
Optimus robots in demonstrations rely on teleoperation for reliable interaction with objects such as popcorn bags and people during handshakes or gestures. Autonomous operation requires the hand to detect object properties, apply appropriate forces, and recover from slips or misalignments without human input. Existing rigid grippers lack sufficient tactile resolution and adaptive compliance for everyday social and manipulation tasks.

## Prior art
- US20250178188A1, Integrated mobile manipulator robot, Boston Dynamics: describes arm and base integration with directional sensors but uses centralized control without distributed finger-level tactile arrays for grasp adaptation.
- EP3838508A1, Intelligent gripper with individual cup control, Boston Dynamics: employs vacuum cups with force sensing yet lacks tendon-driven compliant fingers and social gesture modes.
- US10864641B2, End effector, Bastian Solutions: fin grippers with vacuum ports suited for flat surfaces but unsuitable for irregular soft objects or human contact.

## Summary of the invention
The invention comprises a five-fingered hand with 16 degrees of freedom driven by 8 servo motors through antagonistic tendon pairs. Each finger segment carries a 4x4 tactile array of capacitive sensors with 0.5 N resolution. A wrist-mounted RGB-D camera and IMU feed a local microcontroller that classifies objects into rigid, compliant, or human categories and selects pinch, power, or wave grasp primitives. Contact force is limited to 8 N for social interactions and 25 N for objects, with slip detection via shear sensors triggering incremental closure up to 3 mm.

## Claims
1. A robotic hand comprising a palm housing, five multi-phalanx fingers, tendon actuators, and distributed tactile sensors, wherein an onboard processor executes grasp selection based on initial contact force profile and object shape from co-located vision.
2. The robotic hand of claim 1, wherein each phalanx tactile array comprises capacitive elements spaced at 3 mm pitch and reports both normal and shear forces at 100 Hz.
3. The robotic hand of claim 1, wherein the tendon system includes series elastic elements with 2 mm compliance range that absorb impacts and enable passive shape adaptation.
4. The robotic hand of claim 1, further comprising a social interaction mode that caps peak force at 8 N and initiates a release sequence upon detection of human withdrawal motion via tactile transients.
5. The robotic hand of claim 1, wherein slip recovery includes a 50 ms pause followed by 2 mm incremental tendon shortening repeated up to five times before declaring failure.
6. The robotic hand of claim 1, wherein the processor stores grasp outcome statistics per object class to bias future selections toward higher success primitives.

## Brief description of the drawings
FIG. 1 shows an exploded view of the index finger assembly with sensor placement and tendon routing.
FIG. 2 shows the complete hand in a power grasp configuration around a cylindrical object with force vectors indicated.

## Detailed description
The palm (1) is machined from 6061-T6 aluminum with wall thickness 2.5 mm and mounts four proximal actuators (2) for the fingers plus four distal actuators (3) routed through the metacarpals. Each finger consists of proximal (4), middle (5), and distal (6) phalanges connected by pin joints with 0.2 mm clearance. Antagonistic tendons (7) of 0.8 mm Dyneema pass over low-friction PTFE guides and attach to series elastic springs (8) of 3 N/mm rate. 

Tactile arrays (9) are bonded to each phalanx face using silicone adhesive and contain 16 capacitive taxels per array. The wrist camera (10) provides 640x480 depth at 30 fps aligned to the palm coordinate frame. The microcontroller (11) runs a 200 Hz control loop that first classifies contact from the summed normal force vector; if peak force exceeds 1.5 N within 80 ms the object is labeled compliant. 

For social mode, maximum tendon tension is software-limited to produce 8 N fingertip force measured at the distal taxel. Upon detecting a negative shear transient exceeding 3 N/s the hand opens at 50 mm/s. Object slip is flagged when shear exceeds 40% of normal for more than 120 ms, prompting incremental shortening of the closing tendon by 1.2 mm per cycle. Materials selected for repeated human contact include medical-grade silicone fingertip pads with Shore A 30 durometer. Expected mean time between failures for tendon routing exceeds 50,000 cycles based on accelerated wear testing at 2 Hz.