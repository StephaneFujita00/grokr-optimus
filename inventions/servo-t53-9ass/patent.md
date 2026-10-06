# Firmware-Based Adaptive Tendon Tensioning for Humanoid Robot Hands

## Abstract
A firmware module in the robot hand controller monitors tendon elongation and joint backlash via encoder differentials and applies per-cycle tension offsets to maintain grasp force within 5% of target. The system compensates for material creep and assembly variance without physical sensors in the fingers.

## Problem
Optimus hands lose grasp reliability after repeated cycles due to tendon stretch, joint play, and thermal expansion. Hardware touch sensors add failure points and assembly complexity. Production units exhibit inconsistent finger closure times exceeding 200 ms variance, preventing reliable object manipulation.

## Prior art
No patents returned from searches.

## Summary of the invention
The invention is a firmware routine executing on the hand MCU that measures differential encoder counts between motor and distal joint at 1 kHz, computes cumulative elongation, and issues PWM offset commands to the tensioning motor. Offsets are stored in non-volatile memory and updated each grasp cycle. Failure mode of over-tension is limited by a 15 N force cap derived from motor current.

## Claims
1. A method in a robot hand controller comprising: sampling motor encoder (12) and joint encoder (14) at 1000 Hz; calculating elongation delta as count difference minus calibrated zero; applying tension motor PWM offset of 0.8 times delta scaled by 0.05 N per count when delta exceeds 3 counts; storing updated zero in EEPROM after each grasp.
2. The method of claim 1 wherein the PWM offset is further limited by motor current feedback not to exceed 1.2 A.
3. The method of claim 1 wherein the grasp cycle ends when joint velocity drops below 5 counts per second for 50 ms.
4. The method of claim 1 further comprising a thermal compensation term of 0.2 counts per degree Celsius from a thermistor (22).
5. The method of claim 1 wherein the tensioning motor (18) is driven only between 20% and 80% duty cycle to avoid stall.

## Brief description of the drawings
FIG. 1 shows the hand assembly with encoders, tension motor and tendon routing.

## Detailed description
The hand assembly includes proximal phalanx (2), middle phalanx (4) and distal phalanx (6) linked by revolute joints. Flexor tendon (8) routes from tensioning motor pulley (10) through guides to distal tip. Motor encoder (12) on the drive shaft and joint encoder (14) on the proximal joint provide position feedback. The firmware on MCU (16) executes the loop: read encoders, compute delta = (motor counts - joint counts * gear ratio) - stored zero. If delta > 3 counts, add PWM offset = min(0.8 * delta * 0.05, current limit) to tension motor (18) command. Current is sensed via shunt resistor (20). Thermistor (22) on the tendon guide adds compensation term. After velocity < 5 cps for 50 ms the grasp is declared complete and zero is updated. Over-tension protection cuts power if current > 1.5 A for 100 ms. This restores closure consistency to under 30 ms variance across 5000 cycles.