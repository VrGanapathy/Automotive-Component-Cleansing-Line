# Automated Automotive Component Cleansing Line

## Project Overview
This project simulates the sequential control logic for an automated industrial parts washing and drying line. The logic was developed using Siemens SIMATIC Manager (S7-300 platform) and demonstrates the application of fail-safe principles, edge detection, and precise timing controls for industrial automation.

This project translates real-world physical processes into reliable ladder logic, highlighting a foundational understanding of PLC programming and industrial safety standards.

## System State Machine & Order of Operations

The control system acts as a state machine, progressing through the following phases:

1.  **System Idle (Safety Latch):** The system relies on a Reset-Dominant (SR) Master Start/Stop Latch. The E-Stop and Stop buttons are wired normally closed (NC) in the physical world, translated into the PLC logic to ensure a fail-safe shutdown. If the master latch is off, all machinery is disabled.
2.  **Transport Phase:** With the system active, the conveyor motor engages to move the dirty component toward the washing chamber.
3.  **Entry & Precise Wash Phase:** 
    *   A photoelectric sensor detects the part. **Positive Edge Detection -(P)-** is used to ensure that sensor flicker (caused by a wet/vibrating part) does not re-trigger the cycle.
    *   The conveyor pauses, and a 2-second **On-Delay Timer (S_ODT)** allows the part to settle perfectly under the nozzles.
    *   A 5-second **Extended Pulse Timer (S_PEXT)** drives the chemical spray valve. The extended pulse guarantees an uninterrupted 5-second wash even if the sensor momentarily loses sight of the part.
4.  **Exit & Dry Phase:**
    *   The part exits the wash zone and passes the exit sensor. **Negative Edge Detection -(N)-** identifies the exact moment the part clears the sensor.
    *   A high-power air dryer engages, driven by a 7-second **Off-Delay Timer (S_OFFDT)**. This ensures all trailing moisture is stripped off, while shutting the blower down afterward to conserve factory energy.

## I/O Memory Mapping Table

| Tag Name | Address | Data Type | Description |
| :--- | :--- | :--- | :--- |
| **Emergency Stop** | `I0.0` | Input | Physical NC Mushroom Button (Fail-Safe) |
| **Stop Button** | `I0.1` | Input | Standard NC Push-Button |
| **Start Button** | `I0.2` | Input | Standard NO Push-Button |
| **Wash Zone Sensor** | `I0.3` | Input | Photoelectric Entry Sensor |
| **Exit Sensor** | `I0.4` | Input | Photoelectric Exit/Drying Sensor |
| **Master Latch** | `M0.0` | Memory Bit | System Active Marker |
| **Wash Flag** | `M0.1` | Memory Bit | Active Wash Cycle Marker |
| **Edge Marker 1** | `M1.0` | Memory Bit | State Tracker for Wash Sensor (Positive Edge) |
| **Edge Marker 2** | `M1.1` | Memory Bit | State Tracker for Exit Sensor (Negative Edge) |
| **Conveyor Motor** | `Q0.0` | Output Coil | Drives the main transport belt |
| **Spray Valve** | `Q0.2` | Output Coil | Triggers the 5-second chemical wash |
| **Air Dryer** | `Q0.3` | Output Coil | Triggers the 7-second high-power blower |

*Note: Q0.1 was initially reserved for the conveyor, but was shifted to Q0.0 during development to streamline the System Active status visualization.*

## Timers Utilized

| Timer | Type | Duration | Function |
| :--- | :--- | :--- | :--- |
| **T1** | `S_ODT` | 2 Seconds | Settling delay to stop conveyor sway before spraying. |
| **T2** | `S_PEXT` | 5 Seconds | Uninterrupted chemical wash pulse. |
| **T3** | `S_OFFDT` | 7 Seconds | Energy-saving blower delay after the part exits. |

## Repository Structure

*   `Automotive_Cleansing_Project.zip`: The archived SIMATIC project source files.
*   `Automotive_Cleansing_Line_Logic.pdf`: A fully readable, printed export of the Ladder Logic networks for quick review.

## Viewing the Logic
You do not need Siemens software to review the logic. Please open the provided PDF document to review the network-by-network construction of the SR flip-flops, edge detection circuits, and timer branches.
