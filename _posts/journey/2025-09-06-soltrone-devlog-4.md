---
layout: post
title: "Soltrone Development Log #4"
subtitle: "web control achieved — but safety discipline failed"
tags: [soltrone, drone, raspberry-pi, ardupilot, mavlink, ros2, hardware, dev-log]
author: solgyu
category: journey
subcategory: dev
thumbnail-img: /assets/img/thumb.png
---

I finally got the “no app needed, just a device and a browser” control working. That part was great. The part where I tested it in my living room was not. A brief wobble, a surge, and one prop came back looking like it had a meeting with a soldering iron. Lesson delivered, with smell.

![Damaged propeller after indoor test](/assets/img/AB17546F-F3DC-4489-AD65-10B30D902F98_1_105_c.jpeg)
*Propeller damage from unsafe indoor test; motor heat melted the blade root*

What actually went wrong wasn’t mysterious. I took a shortcut on safety because the tech was exciting, and I paid for it. Mission Planner felt laggy, so I jumped straight to the web controller instead of doing the boring-but-right thing: stepwise tests.

Underneath that are the real culprits: no formal pre‑flight routine, no aggressive throttle limit for first flights, and a Pi 5 mount that was more “temporary arts and crafts” than aerospace. Hot motors and loose cables don’t make friends.

So here’s the grown‑up plan, stated out loud so I can’t ignore it later: bench with props off, then tethered hover, then outdoors and small. Throttle stays limited, geofence on, link‑loss disarms, kill‑switch tested. Hardware gets the treatment too—rigid mount, heat‑shrink, zip ties, and nothing near a prop arc.

Validation will follow the same steps. Slow is fast.

As for the airframe: it’s small, brave, and a little out of breath carrying a Pi 5. I’ll keep v1 focused on proving the control/data pipeline and save the heroic autonomy for a sturdier v2.

Status: Damage contained; a safety routine I’ll actually follow is in place. Repairs first, then outside only.
