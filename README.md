# 💉 Smart Vaccine Refrigeration System

## Overview

This project presents a portable thermoelectric refrigeration system designed to maintain safe vaccine storage temperatures using a Peltier cooling module. The system integrates an ESP32 microcontroller, temperature sensing, relay-based cooling control, and IoT monitoring through the Blynk platform.

The objective was to develop a compact, energy-efficient refrigeration unit suitable for transporting temperature-sensitive vaccines in locations where conventional refrigeration may not be readily available.

## Why This Project?

Maintaining the vaccine cold chain is essential for preserving vaccine effectiveness, especially in remote or rural regions.

This project explores an affordable thermoelectric cooling solution that combines mechanical design, embedded systems, and IoT monitoring to provide real-time temperature control and remote status updates.

## Software & Hardware

### Software

- Arduino IDE
- Blynk IoT Platform

### Hardware

- ESP32
- DS18B20 Temperature Sensor
- Peltier Module
- Relay Module
- Cooling Fan
- Heat Sink
- Power Supply

## Working Principle

The refrigeration system continuously monitors the internal temperature using a DS18B20 temperature sensor.

When the temperature rises above the upper threshold, the ESP32 activates a relay to power the Peltier cooling module. Once the desired temperature range is restored, the relay switches the cooling system off, reducing unnecessary power consumption and improving overall efficiency.

The enclosure dimensions and Peltier placement were selected to maximize cooling effectiveness while maintaining a compact and portable design.

## My Contribution

This project was completed as part of a five-member team.

My contributions included:

- Designing the refrigeration enclosure.
- Determining the optimal enclosure dimensions to improve cooling efficiency.
- Researching thermoelectric refrigeration systems and reviewing relevant literature.
- Optimizing the placement of the Peltier module for effective heat transfer.
- Co-designing the electronic circuit and selecting the required sensors and power supply.
- 
