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

I thought I had control because the desktop could ping the Pi’s web server. Turns out that’s like waving at the tower and calling it flying. The FC didn’t care about my browser; the link to the actual controller wasn’t there yet. Mission Planner wouldn’t ARM either, which sent me spelunking through parameters and firmware flavors.

The FC is a Speedbee F4 Plus, which doesn’t have a comfy first‑class seat in ArduPilot. I’d flashed a similar brand’s target before and kept getting those passive‑aggressive “config error, fix problem and reboot” messages. Switching to the generic `OMNIBUSF4` finally made the board feel seen.

Then came the arming checks. This board simply doesn’t have all the sensors ArduPilot dreams about. Once I stopped arguing with reality and disabled the checks for the missing parts via the Full Parameter List, the ARM light finally said hello. Props stayed off; I like my fingers.

From the desktop, I hit the Pi over TCP, watched telemetry wake up, and—carefully—tickled the motors. Not a full flight, just enough to prove the path works end‑to‑end. Outdoor testing will carry the rest.

Next up: mount the Pi like I mean it, keep cables away from props, and take this outside where it belongs. When the link survives the wind, I’ll bring ROS2 back into the picture and start teaching the drone something more interesting than “don’t spin into things.”

## Media

![Soltrone Log 3 - Snapshot 1](/assets/img/soltrone_log3/94C0E86B-600B-4E81-9E32-154134C01321_4_5005_c.jpeg)
*Bench setup / wiring snapshot*

![Soltrone Log 3 - Snapshot 2](/assets/img/soltrone_log3/9FB5D57B-D629-4347-BE63-5407D4385288_4_5005_c.jpeg)
*Bench test / controller snapshot*

