# Firmware-Based Tendon Tension Compensation for Humanoid Robot Hands

## Abstract
A firmware module in the Optimus hand controller continuously monitors tendon encoder positions and motor currents to compute real-time tension offsets. It applies corrective PWM signals to each actuator to maintain target tension within 0.2 N, compensating for stretch and friction without hardware changes.

## Problem
Optimus hands exhibit inconsistent grasp force and finger positioning during scale-up due to cumulative tendon elongation and joint friction variations exceeding 15 percent across production units. These variations cause grasp failures when commanded positions deviate by more than 3 mm at the fingertip.

## Prior art
- US11312012B2 Software compensated robotics: uses image feedback for end-effector correction; this invention differs by relying solely on internal tendon encoders and current sensors for closed-loop tension without external vision.
- US9014853B2 Calibration method and calibration system for robot: performs static joint calibration; this invention differs by executing continuous dynamic compensation during operation at 1 kHz.

## Summary of the invention
The invention adds a tension compensation loop inside the existing hand firmware. Each of the 16 tendons per hand has a dedicated PID controller that adjusts motor torque based on measured versus modeled tension. Model parameters are updated from a 30-second self-calibration routine executed at power-up.

## Claims
1. A method in a robot hand controller comprising: reading tendon position encoders (12) and motor current sensors (14) at 1 kHz; computing instantaneous tension T = k*(pos_ref - pos_meas) + b*current; applying a correction voltage V_corr = Kp*(T_target - T) + Ki*integral to each motor driver; wherein Kp is 0.8 V/N and Ki is 12 V/(N*s).
2. The method of claim 1 further comprising performing an initial calibration by commanding each finger to full extension and flexion while recording encoder drift and storing offset values in non-volatile memory.
3. The method of claim 1 wherein the tension target T_target for each tendon is adjusted by a learned friction map stored as a 16-by-8 lookup table indexed by joint angle and velocity.
4. The method of claim 1 further comprising detecting failure when tension error exceeds 1.5 N for more than 200 ms and switching the affected finger to a limp mode by setting motor current to zero.
5. The method of claim 1 wherein the compensation loop is disabled below 5 N commanded tension to avoid oscillation near zero force.

## Brief description of the drawings
FIG. 1 shows the tendon routing and sensor placement in a single finger assembly.
FIG. 2 shows the firmware data flow diagram with reference numerals.

## Detailed description
The hand assembly contains four fingers, each with four tendons routed through PTFE-lined guides (20). Proximal tendon encoder (12) measures displacement with 0.05 mm resolution. Motor current sensor (14) on the brushless driver board provides 10 mA resolution. The firmware runs on the existing STM32H7 microcontroller at 168 MHz. At each 1 ms tick the main loop reads all 16 encoders and currents. Tension is computed using the equation above with spring constant k = 180 N/m derived from Dyneema tendon modulus. The PID output is added to the nominal PWM command before the motor driver stage. During self-calibration the fingers cycle three times between 0 and 90 degree joint angles. Offsets are averaged and written to flash. The friction map is populated by commanding constant velocity sweeps and recording current differences. Failure mode of sensor dropout is handled by freezing the last valid tension value for up to 50 ms before entering limp mode. Temperature compensation is applied by scaling k by (1 + 0.002*(T-25)) where T is read from an onboard thermistor. All numeric constants are stored in a single header file for OTA updates.