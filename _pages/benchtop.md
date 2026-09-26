---
layout: default
title: "Technicomp Benchtop Linux"
permalink: /benchtop/
description: "Technicomp Benchtop Linux is a stabilized workstation rolling release derived from openSUSE Tumbleweed and MicroOS: a complete, immutable workstation operating system for technical work."
---
<div class="wrap">
  <header class="page-header product-header">
    <p class="kicker">Technicomp Benchtop Linux &middot; Alpha</p>
    <h1>The operating system for the technical workbench.</h1>
  </header>

  {% include benchtop.html compact=true %}
</div>

<div class="measure prose product-body" markdown="1">

## What Benchtop Linux is

Technicomp Benchtop Linux is a stabilized workstation rolling release derived from openSUSE Tumbleweed and MicroOS. It is intended for people who use Linux as a professional workstation and want the operating system to arrive complete, remain predictable over time, and still provide a current development environment.

Benchtop is opinionated about the operating system and flexible above it. The system image contains and maintains the desktop, hardware integration, development toolchains, privileged services, and other machine-level plumbing. Applications, additional tools, project environments, and user configuration live above that boundary.

## A stabilized rolling release

Not every part of an operating system benefits from the same update policy. Benchtop follows Tumbleweed for most of the software stack, keeping compilers, language runtimes, system libraries, firmware, graphics components, and development tools current.

The kernel and desktop move more cautiously because regressions there are disproportionately disruptive to a working machine. Benchtop uses an LTS kernel by default, with a current kernel available when newer hardware requires it. The Blueprint Environment follows the previous upstream-supported GNOME release, continuing to receive upstream fixes while avoiding the earliest part of each major desktop transition.

Benchtop therefore does not try to make every component equally conservative. It keeps the parts that benefit from currency moving with Tumbleweed while giving the kernel and desktop a longer stabilization window.

> For the desktop in particular, the informal version is to let other people be the beta testers.

## An immutable system, extended per user

Benchtop uses the immutable, transactional model provided by MicroOS. System updates are applied as new snapshots rather than as a sequence of changes to the running root filesystem, and an earlier snapshot remains available for rollback.

The system package set is not intended to be customized by the user. Graphical applications are installed as per-user Flatpaks. Homebrew provides additional command-line software and alternate or pinned toolchains in the user's profile. Project dependencies are managed by their native language ecosystems, and workloads that need an independently mutable Linux userspace belong in containers or virtual machines.

The operating system stays consistent while the user's environment remains flexible.

## A complete system

An immutable workstation only works well if the image already contains the system-level pieces its users need. Benchtop therefore ships the plumbing for its intended workflows rather than expecting every user to reconstruct it afterward.

If support requires a kernel patch, firmware, a system service, device permissions, a udev rule, or similar privileged integration, it belongs in the system image. Current examples include ASUS laptop support through asusctl, OpenRGB integration, Microsoft Surface hardware, and T2-based Macs.

The same principle applies to development. Benchtop includes the major programming-language toolchains at the versions supplied by Tumbleweed. The distro provides the compiler or interpreter, standard library, and normal development tooling; third-party project libraries remain with Cargo, npm, pip, RubyGems, and the other native language ecosystems rather than becoming part of the system package set.

## The Blueprint Environment

Benchtop's desktop is the Blueprint Environment, a GNOME-based desktop whose behavior and presentation are maintained as part of the operating system.

Blueprint combines GNOME with a maintained set of extensions, defaults, and system configuration that define how the Benchtop desktop behaves. The objective is consistency across machines and releases: controls remain where users expect them, common actions behave predictably, and upgrading the operating system does not repeatedly reorganize the desktop.

The Blueprint Shell is the GNOME Shell-specific portion of that environment: its extensions, configuration, and behavior. Blueprint is not a fork of GNOME; it is Benchtop's integrated GNOME configuration, maintained as part of the operating system.

## Responsive under load

Benchtop treats interactive responsiveness as a primary performance requirement.

Linux systems are often optimized for aggregate throughput. That is a sensible priority for servers and batch workloads, but it can be the wrong tradeoff for an interactive desktop. A machine can finish background work somewhat faster while becoming noticeably sluggish when CPU, memory, or storage activity competes with the person using it.

Benchtop gives priority to low and predictable interactive latency when that conflicts with extracting the last increment of throughput. Scheduling, memory management, storage behavior, power management, and related system policy are chosen with desktop responsiveness in mind.

The same approach extends to professional audio. Low-latency and real-time audio are intended workstation workloads, with the necessary scheduling policy, resource limits, permissions, and PipeWire integration configured at the system level.

{% include benchtop-handoff.html %}

</div>
