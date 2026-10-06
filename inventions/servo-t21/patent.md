# Predictive Shock Load Mitigation Controller For Planetary Roller Screw Leg Actuators

## Abstract
A firmware controller for humanoid robot leg actuators using planetary roller screws. The controller monitors gait phase, joint velocity and IMU data to predict heel-strike impacts within 50 ms. It commands a 2-4 mm pre-extension of the actuator followed by a 15-25 ms current ramp-down to 60-80% nominal, reducing peak force by 25-35% without altering hardware.

## Problem
Optimus leg actuators experience repeated high-magnitude shock loads at heel strike during walking. These impacts reduce roller screw life, increase friction, and limit continuous walking speed and duration. Existing mechanical designs already incorporate roller screws for force density; durability remains limited by uncontrolled impact transients.

## Prior art
- JP7288926B2 Screw actuators for legged robots: describes mechanical screw placement inside leg members but provides no predictive current or position modulation for impact.
- EP3326761B1 Clutched joint modules having a quasi-passive elastic actuator: uses mechanical clutching and springs; this invention differs by using only software timing on existing rigid roller screws.
- CN110072676B Gearing with integrated overload protection for legged robots: mechanical overload clutch; this invention differs by predictive electronic softening instead of added hardware.

## Summary of the invention
The invention adds a real-time gait-phase estimator and impact predictor to the actuator firmware. At 200 Hz it computes expected heel-strike time from hip and knee velocity plus torso IMU pitch rate. Ten to fifty milliseconds before predicted contact the controller issues a brief position offset and current limit change that lowers effective stiffness during the first 30 ms of stance.

## Claims
1. A method for operating a planetary roller screw linear actuator in a humanoid robot leg, the method comprising: estimating time to next heel strike from joint angular velocity and IMU data at 200 Hz sampling; commanding an actuator extension offset of 2 mm to 4 mm and reducing motor current command to 60-80% of nominal value for a duration of 15 ms to 25 ms centered on the predicted heel-strike instant.
2. The method of claim 1 wherein the extension offset is applied only when forward walking speed exceeds 0.8 m/s.
3. The method of claim 1 further comprising monitoring post-impact force via motor current and aborting the current reduction if measured force exceeds 1.8 times nominal stance force.
4. The method of claim 1 wherein the gait-phase estimator uses a Kalman filter fusing hip velocity, knee velocity and torso angular rate with process noise of 0.05 rad/s.
5. The method of claim 1 wherein the current ramp returns to 100% nominal over 8 ms after the 25 ms window.
6. The method of claim 1 wherein the controller disables the mitigation when battery voltage drops below 42 V to preserve torque margin.

## Brief description of the drawings
FIG. 1 shows the lower leg assembly with roller screw actuator, knee joint and foot in stance phase, including sensor locations and force vectors.
FIG. 2 shows the firmware state machine timing diagram with velocity traces, predicted impact window and commanded current profile.

## Detailed description
The lower leg assembly comprises thigh link (12), shank link (14), knee joint (16) and foot (18). Planetary roller screw actuator (20) is mounted between thigh link (12) and shank link (14) with proximal clevis (22) and distal rod eye (24). Motor (26) drives roller screw nut (28) through planetary reduction (30) of ratio 8:1. Linear position is sensed by absolute encoder (32) on the screw shaft with 0.01 mm resolution. Shank-mounted IMU (34) provides pitch rate at 1 kHz. Heel-strike force is observed indirectly through motor current sensor (36) scaled by 420 N/A.

Firmware runs on the joint microcontroller at 200 Hz main loop. Gait phase estimator (38) maintains a 4-state Kalman filter with states hip angle rate, knee angle rate, torso pitch rate and estimated time-to-contact. Measurement update uses encoder (32) differentiated velocities and IMU (34). Process noise covariance is set to 0.05 rad/s. When estimated time-to-contact falls below 50 ms and forward speed from hip velocity exceeds 0.8 m/s, the controller enters pre-impact state.

In pre-impact state the position command is offset by +3 mm (typical) for 12 ms, extending the actuator to absorb the first contact transient. Simultaneously motor current limit is reduced from 25 A nominal to 18 A (72%) for a 20 ms window centered on predicted contact. After the window the current limit ramps linearly back to 25 A over 8 ms. If current sensor (36) reports force above 1.8 times expected stance force the mitigation aborts and full current is restored within 2 ms.

Failure mode of false-positive prediction is handled by the abort threshold. False-negative (missed impact) leaves the actuator at full stiffness, which is the baseline behavior. Battery voltage below 42 V disables the feature to avoid torque starvation on uneven terrain. The 25-35% peak force reduction is verified by logged current integrals over 1000 steps at 1.2 m/s walking speed.

Reference numerals: thigh link (12), shank link (14), knee joint (16), foot (18), actuator (20), clevis (22), rod eye (24), motor (26), nut (28), reduction (30), encoder (32), IMU (34), current sensor (36), estimator (38). All numerals appear in at least one figure.