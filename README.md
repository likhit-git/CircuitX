# Simple Door Lock Application Using VSDSquadron Mini (Level 1)
## Introduction
Due to the advancement of science and technology worldwide, there may be a consequent growth in the fee and sophistication of crime. As a result, it is essential to ensure the safety of oneself and one’s treasured belongings; despite mechanical locks, the crime price has multiplied since those locks are effortlessly damaged. Consequently, traditional locking systems that use a mechanical lock and key are nowadays replaced with new, advanced locking systems. With the advancement of the digital age, these systems are being replaced by digital systems that rely on inputs like pins, fingerprints, image processing, or a combination of these. 

## Overview
This project integrates mechanical and electronic devices and is highly programable and compatible. Here, we will be developing a simple prototype of the lock for the Level 1 of the Hackathon - Developing an Application (complexity - medium). One of the prominent features of these innovative locks is their simplicity and high efficiency. This work is of an electronic combination lock with a keyboard mounted on the door for keying in the secret code. This automatic lock system consists of an electronic control assembly, which controls the output load through a password. This output load here is a stepper motor that controls the door (to reduce the BOM, we are using LEDs instead of the motor here). LCD is attached to the lock system, which displays the lock's status. The coding unit, which operates with a 12-switch (matrix) keypad, was designed to control an electromechanical door lock with five–digit code. When the keypad is fed a correct password, the door will open for 10 seconds and then close. 
Additional features that can be added to this simple lock in the project:
A separate counter can be added to tell how many people have entered the room. Unlike other locks, this lock can be constructed so that once the wrong keys are pressed, it resets automatically, making it harder for an intruder to break in. It can be programmed to show the number of attempts the person makes before making an alarm and to blow an alarm after three error count entries. We need to add  This password-based "Secure Lock Using VSDSquadron Mini" can thus be used in places where we need more security.

## Components or BOM (Rs.100 – Rs.200)
- VSD Squadron Mini development board with CH32V003F4U6 chip with 32-bit RISC-V core based on RV32EC instruction set
- 16x2 LCD Module
- I2C Adapter Module for LCD
- 4x3 Keypad Module
- 2 LEDs (Red and Green)
- 2 Resistors (220 ohm)
- Bread Board
- Jumper Wires

## Circuit Connection


## Table of Pin Connection
Keypad Pin | VSD Squadron Mini Pin | Function
| ------------- | ------------- |------------- |
R1 | PD0 | Row 1
R2 | PD1 | Row 2
R3 | PD2 | Row 3
R4 | PD3 | Row 4
C1 | PD4 | Column 1
C2 | PD5 | Column 2
C3 | PD6 | Column 3

LCD I2C Adapter Pin | VSD Squadron Mini Pin
| ------------- |-------------|
VCC | 5V
GND | GND
SDA | PC1
SCL | PC2

LED (Anode) Pin | VSD Squadron Mini Pin | LED colour | Function
| ------------- |-------------| ------------- |-------------|
LED1 | PD7 | Green | Door Open
LED2 | PC0 | Red | Door Closed





