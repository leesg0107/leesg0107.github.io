---
layout: post
title: "Soltrone Development Log #2"
subtitle: "Communication breakthrough: From Betaflight limitations to ArduPilot success"
tags: [soltrone, drone, raspberry-pi, ardupilot, betaflight, communication]
author: solgyu
category: journey
subcategory: dev
---

## The Communication Challenge Conquered

Remember my last update where I was nursing burnt ESCs and debugging hardware disasters? Well, I'm back with some actual good news for once! **Soltrone can now communicate properly.**

The journey from basic flight control to full communication has been... educational. Let's just say I've learned more about drone firmware limitations than I ever wanted to.

## Phase 1: Betaflight Success (With a Side of Sparks)

Starting with the basics, I managed to get Betaflight working with my FC and ESC setup. The basic control tests were successful - motors spinning, flight controller responding, everything looking promising.

**Then came the soldering drama.** 

Those messy solder joints I mentioned last time? They came back to haunt me. Turbulence in the solder connections kept causing shorts, and sure enough, another ESC bit the dust. 

*Lesson learned: Good soldering isn't optional, it's survival.*

The fix was straightforward but humbling:
- Replace the burnt ESC (again)
- Wrap everything in insulation tape like I should have done from the start
- Test thoroughly before assuming anything works

Sometimes the simplest solutions are the ones we overlook in our rush to get things working.

## Phase 2: Raspberry Pi 5 Integration

With the hardware finally stable, it was time for the next challenge: integrating the Raspberry Pi 5.

**Power management was crucial here.** I used a UBEC 5A converter to provide stable power from the main battery to the Raspberry Pi 5. Clean power delivery = happy Pi = fewer mysterious crashes.

![Soltrone Hardware Setup](/assets/img/soltrone_log2/hardware_setup.jpeg)
*The improved hardware setup: Raspberry Pi 5 with cooling, flight controller, clean wiring with proper insulation, and stable power distribution*

The Pi-to-FC connection worked beautifully. Data flowing back and forth, telemetry looking good, everything seemed ready for the next step.

**But then came the communication roadblock.**

## The Betaflight Wall

Here's where things got interesting. I could get the Raspberry Pi 5 talking to the flight controller just fine, but connecting to my phone? Nope. Not happening.

After hours of debugging, documentation diving, and general frustration, I realized something important: **Betaflight has limitations.**

For basic flight control, Betaflight is fantastic. But when you start thinking about the bigger picture - multiple sensors, complex mission modes, custom communication protocols - those limitations become walls.

I need flexibility. I need extensibility. I need something that won't box me in as Soltrone evolves.

## Phase 3: The ArduPilot Migration

This was the "nuclear option" I'd been avoiding. Switching firmware mid-project feels like starting over, but sometimes you have to step back to move forward.

**ArduPilot changed everything.**

Suddenly, the communication barriers that seemed insurmountable with Betaflight just... disappeared. The Pi-to-phone connection that had been fighting me for days worked on the first try.

## Current Architecture Success

Here's what I've achieved with the ArduPilot setup:

**Hardware Communication Chain:**
```
Phone ↔ Raspberry Pi 5 ↔ Flight Controller
```

**What actually works now:**
- **Stable wired communication** between Raspberry Pi 5 and FC
- **Bidirectional data flow** for telemetry and control
- **Web server** running on the Pi for remote access
- **Phone-based remote control** via designated URL
- **Real-time drone control** from mobile device

I can literally open a browser on my phone, navigate to the Pi's IP address, and control the drone remotely. It feels like magic after all the debugging.

![DARE Drone Control Dashboard](/assets/img/soltrone_log2/dashboard_interface.jpeg)
*The web-based control dashboard showing successful connection, telemetry data, and remote control capabilities*

![Terminal Output Success](/assets/img/soltrone_log2/terminal_output.jpeg)
*Terminal logs showing successful ArduPilot connection, server initialization, and mobile accessibility confirmation*

## The Current Bottleneck: Power Management

Of course, there's always something. Right now, I'm stuck on the most mundane issue possible: **battery charging.**

My LiPo batteries are completely drained from all the testing, and I don't have a proper LiPo charger yet. So while I have a fully functional communication system, I can't actually test flight operations.

It's like having a Ferrari with an empty gas tank.

The charger is on the way, and once it arrives, I'll finally be able to do proper flight tests with the full communication stack running.

## Technical Insights

**Why ArduPilot made the difference:**
- More flexible communication protocols
- Better support for companion computers
- Extensible architecture for custom features
- Active development community
- Built for complex missions, not just racing

**The power of stepping back:**
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