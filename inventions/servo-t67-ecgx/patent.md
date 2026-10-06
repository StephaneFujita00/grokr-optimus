# Adaptive firmware control for satellite signal transmission through vehicle glazing

## Abstract
A firmware-based system in a vehicle communication module adjusts transmit power, frequency offset, and modulation parameters of a Starlink direct-to-cell transceiver when signal attenuation from windshield coatings is detected. Sensors measure coating conductivity and vehicle orientation to predict path loss. The system compensates in real time without hardware changes.

## Problem
Tesla windshields incorporate infrared-reflective conductive coatings that attenuate radio frequencies above 1 GHz used by Starlink Mobile. Signal strength drops 15-25 dB, preventing reliable connection from inside the vehicle. Software updates alone cannot overcome fixed hardware transmit limits and Doppler compensation under variable attenuation.

## Prior art
- CN110573466B: Glass panel with radio wave transmission regions created by patterned conductive film removal. This invention differs by using firmware adaptation rather than physical patterning of the glass.
- EP3399595B1: Multi-band radio frequency transparency window formed by resonant structures in conductive film. This invention differs by dynamically tuning the transceiver firmware instead of relying on static resonant patterns in the glazing.

## Summary of the invention
The invention provides firmware running on the vehicle's satellite communication controller that receives inputs from conductivity sensors embedded near the windshield edge and from inertial measurement units. It calculates expected attenuation, applies power boost up to regulatory limits, shifts carrier frequency by up to 200 kHz to exploit coating resonances, and selects lower-order modulation. Failure modes such as sensor drift are handled by fallback to maximum power and periodic recalibration.

## Claims
1. A method for satellite communication in a vehicle comprising: measuring conductivity of a windshield coating with at least one sensor; calculating expected attenuation at 1.5-2.0 GHz based on measured conductivity and incidence angle; adjusting transmit power of a direct-to-cell transceiver by up to 6 dB; and applying a frequency offset of 50-200 kHz to compensate for coating resonance.
2. The method of claim 1 further comprising selecting modulation order based on measured signal-to-noise ratio after adjustment.
3. The method of claim 1 wherein the conductivity sensor is a capacitive probe operating at 10 kHz placed within 50 mm of the glazing edge.
4. The method of claim 1 wherein the frequency offset is recalculated every 500 ms using vehicle pitch and roll from an IMU.
5. The method of claim 1 further comprising entering a fallback mode that sets maximum allowed power and disables frequency offset when sensor data is unavailable for more than 2 seconds.
6. The method of claim 5 wherein fallback mode is exited after successful receipt of an acknowledgment packet from the satellite.

## Brief description of the drawings
FIG. 1 shows the windshield assembly with embedded conductivity sensor and controller connections.
FIG. 2 shows the signal processing flow in the firmware module.

## Detailed description
The vehicle windshield (10) comprises outer glass ply (12), interlayer (14), inner glass ply (16), and infrared-reflective coating (18) of thickness 150 nm with sheet resistance 4-8 ohms per square. Conductivity sensor (20) is a 5 mm diameter capacitive electrode mounted on the inner ply (16) 30 mm from the top edge and connected by shielded cable (22) to controller (24). Controller (24) executes firmware that samples sensor (20) at 100 Hz and computes coating conductivity sigma using a lookup table calibrated for 20-80 degrees Celsius. Incidence angle theta is derived from IMU (26) pitch and roll values. Expected attenuation A in dB is computed as A = 18 + 0.12 * sigma + 0.3 * (theta - 45). If A exceeds 12 dB the firmware increases transceiver (28) output power from nominal 23 dBm to 29 dBm and applies frequency offset delta_f = 120 kHz * sin(theta) within the 1.8 GHz band. Modulation switches from 16-QAM to QPSK when post-adjustment SNR falls below 12 dB. Sensor drift is detected if conductivity reading changes more than 15 percent over 10 seconds without corresponding temperature change; in that case fallback mode activates maximum power and zero offset until an ACK is received from the satellite. All numerical values are also stated in the claims. Dimensions of sensor electrode are 5 mm diameter with 0.5 mm tolerance. Recalculation interval is 500 ms with plus or minus 10 ms tolerance. Power boost limit is 6 dB.