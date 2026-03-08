# Real-Time Embedded Motor Controller (TI MSP432)
**Hardware:** TI MSP432P401R (ARM Cortex-M4) | **Sensors:** Ultrasonic Rangefinder, I2C Compass

![Hardware Integration: TI MSP432 Launchpad mounted on custom mobile chassis](media/launchpad_and_car.png)
*Figure 1: The Embedded Control Subsystem (MSP432) integrated with the drivetrain.*

## 1. Project Overview
This repository contains the embedded drivers and control logic developed for an autonomous mobile robot. The project demonstrates the progression from simple, sequential blocking logic to a more deterministic, interrupt-driven control system using hardware timers.



## 2. Core Implementation
* **Closed-Loop Velocity Control:** Implemented a 10Hz Feedforward + Proportional (P) controller. The feedforward baseline handles the non-linear deadband of the DC motors, while the P-controller compensates for battery voltage drop and load variances to maintain a steady speed.
* **Hardware Timer Encoders:** Configured `Timer_A` modules in Continuous Capture Mode. Rather than relying on standard GPIO interrupts which can miss high-speed pulses, the hardware timers latch the exact tick of the quadrature encoders, providing accurate data for the velocity calculations.
* **Register-Level I2C Interface:** Developed a custom I2C driver to communicate with the onboard compass and ultrasonic sensors without relying on external libraries.

## 3. Engineering Retrospective
* **The Challenge:** The I2C bus required atomic access during start conditions, but high-speed encoder interrupts risked corrupting the data pipeline.
* **The Solution:** Implemented critical sections (`Interrupt_disableMaster()`) around the start and address assignment routines in `i2c_driver.c` to protect the register writes. 
* **Future Iteration:** The current I2C driver relies on blocking `__delay_cycles()`. In a future revision, I would replace these blocking delays with an interrupt-driven state machine to free up CPU clock cycles for high-level path planning.
