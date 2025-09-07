---
layout: post
title: "Soltrone Development Log #2"
subtitle: "Communication breakthrough: From Betaflight limitations to ArduPilot success"
tags: [soltrone, drone, raspberry-pi, ardupilot, betaflight, communication]
author: solgyu
category: journey
subcategory: dev
---

I used to think “if the motors spin, I’m basically done.” Then I tried to make the drone talk to everything else. The week started with Betaflight humming along nicely—motors armed, basic control behaved—and ended with me switching the entire firmware stack to ArduPilot because I kept head‑butting the limits I didn’t know were there.

## Phase 1: Betaflight Success (With a Side of Sparks)

Starting simple worked: Betaflight, FC, ESCs—all alive. For a minute it felt like the project had turned a corner.

**Then came the soldering drama.** 

Those messy solder joints I mentioned last time? They weren’t done with me. Vibration turned one into a short and another ESC retired early.

*Lesson learned: Good soldering isn't optional, it's survival.*

The fix was straightforward but humbling:
- Replace the burnt ESC (again)
- Wrap everything in insulation tape like I should have done from the start
- Test thoroughly before assuming anything works

Sometimes the simplest solutions are the ones we overlook in our rush to get things working.

## Phase 2: Raspberry Pi 5 Integration

Once the hardware stopped lighting warning candles, I plugged the Raspberry Pi 5 into the mix.

Clean power turned out to be the quiet hero: a UBEC 5A kept the Pi stable and the gremlins out.

![Soltrone Hardware Setup](/assets/img/soltrone_log2/hardware_setup.jpeg)
*The improved hardware setup: Raspberry Pi 5 with cooling, flight controller, clean wiring with proper insulation, and stable power distribution*

The Pi-to-FC connection worked beautifully. Data flowing back and forth, telemetry looking good, everything seemed ready for the next step.

**But then came the communication roadblock.**

## The Betaflight Wall

Then I tried to bring a phone into the loop and hit a wall.

After hours of debugging, documentation diving, and general frustration, I realized something important: **Betaflight has limitations.**

For basic flight control, Betaflight is fantastic. But when you start thinking about the bigger picture - multiple sensors, complex mission modes, custom communication protocols - those limitations become walls.

I need flexibility. I need extensibility. I need something that won't box me in as Soltrone evolves.

## Phase 3: The ArduPilot Migration

I resisted the “switch firmware” button for days. It felt like starting over. It was actually the shortcut.

ArduPilot changed the conversation.

The Pi‑to‑phone link that fought me for days worked on the first try. Sometimes the answer is “use the tool built for the job.”

## Current Architecture Success

Here’s what the current setup looks like—and actually works:

**Hardware Communication Chain:** Phone ↔ Raspberry Pi 5 ↔ Flight Controller

**What actually works now:**
- **Stable wired communication** between Raspberry Pi 5 and FC
- **Bidirectional data flow** for telemetry and control
- **Web server** running on the Pi for remote access
- **Phone-based remote control** via designated URL
- **Real-time drone control** from mobile device

I can open a browser on my phone, hit the Pi’s IP, and move the drone from a web page. After the week I had, it honestly felt like a magic trick.

![DARE Drone Control Dashboard](/assets/img/soltrone_log2/dashboard_interface.jpeg)
*The web-based control dashboard showing successful connection, telemetry data, and remote control capabilities*

![Terminal Output Success](/assets/img/soltrone_log2/terminal_output.jpeg)
*Terminal logs showing successful ArduPilot connection, server initialization, and mobile accessibility confirmation*

## The Current Bottleneck: Power Management

Naturally, the next blocker is the least glamorous: **charging.**

My LiPo batteries are completely drained from all the testing, and I don't have a proper LiPo charger yet. So while I have a fully functional communication system, I can't actually test flight operations.

Ferrari, empty tank.

The charger is on the way, and once it arrives, I'll finally be able to do proper flight tests with the full communication stack running.

## Technical Insights

Why ArduPilot made the difference:
- More flexible communication protocols
- Better support for companion computers
- Extensible architecture for custom features
- Active development community
- Built for complex missions, not just racing

The power of stepping back:
Sometimes the best debugging technique is admitting that your current approach isn't working. The Betaflight-to-ArduPilot switch felt like giving up, but it was actually the breakthrough moment.

## What's Next

With communication sorted, the roadmap becomes clearer:

1. **Flight testing** (as soon as that charger arrives)
2. **Basic autonomous modes** (waypoint navigation)
3. **Camera integration** (finally putting those vision plans to work)
4. **Mission-specific behaviors** (the fun stuff!)

## Issues, root causes, and fixes

- Repeated ESC failures: Solder shorts and vibration-induced contact.
  - Fix: Re-solder with proper tip/temp, use flux, continuity check; apply heat-shrink and secure with zip ties; isolate ESCs with foam pads.
- Unreliable phone connection on Betaflight: Firmware constraints for companion-computer workflows.
  - Fix: Migrate to ArduPilot for richer MAVLink + companion support; verify serial params (e.g., `SERIALx_PROTOCOL=2`, baud rates) and heartbeats.
- Power instability on Raspberry Pi 5: Brownouts under load.
  - Fix: Use UBEC 5A with adequate headroom; keep wiring short; monitor voltage/current.

## Verified working configuration

- Communication chain: Phone ↔ Raspberry Pi 5 ↔ Flight Controller (ArduPilot)
- Stable telemetry/control over web dashboard served on Pi
- Bidirectional MAVLink confirmed via logs

---

*Next update: Flight tests with full communication stack (battery permitting).* 🔋

**Status**: ArduPilot comms stable; waiting on LiPo charger to begin flight tests. 🚁📡