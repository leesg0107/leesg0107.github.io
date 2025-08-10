---
layout: post
title: "Soltrone Development Log #3"
subtitle: "Control link and arming validation with ArduPilot on Speedbee F4 Plus"
tags: [soltrone, drone, raspberry-pi, ardupilot, mavlink, ros2, hardware, dev-log]
author: solgyu
category: journey
subcategory: dev
thumbnail-img: /assets/img/thumb.png
---

## Objective

Establish a reliable end‑to‑end control link between the desktop and the drone via Raspberry Pi 5 (not just web server reachability), and verify arming and basic control.

## Symptoms

- Desktop could reach the Raspberry Pi 5 web server, but there was no actual control path to the flight controller (FC).
- Mission Planner wired connection could not ARM the vehicle.

## Root causes

1. Firmware mismatch on the FC (Speedbee F4 Plus)
   - There is no dedicated ArduPilot firmware build for this exact board. Flashing a different brand’s target caused errors such as “config error, fix problem and reboot.”
   - Resolution: Flash the generic `OMNIBUSF4` ArduPilot target so the FC is recognized correctly.

2. Arming checks failing due to unsupported/absent sensors
   - The low-cost Speedbee F4 Plus lacks several sensors. Related parameters must be disabled, otherwise arming fails.
   - Resolution: In Setup → Full Parameter List, identify parameters flagged in Messages and set the missing‑sensor checks to 0 (disabled) as appropriate.

## Fix steps

1. Flash ArduPilot `OMNIBUSF4` to the FC and confirm no persistent config errors.
2. Review Messages in Mission Planner; disable arming checks for sensors not present on the board via Full Parameter List.
3. Reconnect and verify ARM status with props off.

## Validation

- Connected from desktop to the Raspberry Pi 5 via TCP using the Pi’s IP and confirmed telemetry/control flow to the FC.
- Performed limited indoor throttle/arming tests (no props/short hover attempts). Full outdoor testing pending.

## Next actions

- Securely mount the Raspberry Pi 5 (current temporary mount risks prop contact).
- Conduct outdoor flight tests for full control validation.
- Begin ROS2 integration toward autonomous behaviors once the control link is proven stable.

## Status

Control link established and arming verified after firmware/parameter fixes; outdoor validation and hardware mounting improvements are next.

## Media

![Soltrone Log 3 - Snapshot 1](/assets/img/soltrone_log3/94C0E86B-600B-4E81-9E32-154134C01321_4_5005_c.jpeg)
*Bench setup / wiring snapshot*

![Soltrone Log 3 - Snapshot 2](/assets/img/soltrone_log3/9FB5D57B-D629-4347-BE63-5407D4385288_4_5005_c.jpeg)
*Bench test / controller snapshot*

