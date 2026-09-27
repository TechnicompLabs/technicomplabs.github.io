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

Technicomp Benchtop Linux is a stabilized workstation rolling release derived from [openSUSE Aeon](https://aeondesktop.org/), built on openSUSE Tumbleweed and the transactional infrastructure shared with MicroOS. It builds on Aeon's image and installation work, adding a broader workstation toolset, the Blueprint Environment, and its own kernel and desktop release policies.

Benchtop is opinionated about the operating system and flexible above it. The system image contains and maintains the desktop, hardware integration, development toolchains, privileged services, and other machine-level plumbing. Applications, additional tools, project environments, and user configuration live above that boundary.

## The work it supports

Benchtop supplies the drivers, services, toolchains, and desktop integration for a broad range of technical work. Users choose their applications and maintain their own project environments.

| Work | Scope |
|---|---|
| Software development | Compilers, language environments, build tools, version control, debugging, and performance analysis. |
| AI | Local inference and model development, with GPU and compute integration. |
| Data science | Scientific computing, statistical analysis, data processing, and database access. |
| Virtualization | KVM/QEMU virtual machines, containers, and isolated Linux environments. |
| System administration and DevOps | Remote access, configuration management, infrastructure automation, networking, storage, backup, and recovery. |
| Security | Vulnerability assessment, network analysis, host auditing, and digital forensics. |
| Reverse engineering | Disassembly, decompilation, binary inspection, and debugging. |
| Electronics and embedded development | Serial consoles, microcontroller programming, firmware work, on-chip debugging, and logic analyzers. |
| Content creation | Professional audio, video, graphics, and publishing, with low-latency audio and hardware video acceleration. |
| Gaming | Graphics, controller support, and performance management. |

The [package patterns](https://github.com/TechnicompLabs/benchtop-patterns) describe the system-level tools included for these workflows.

## A stabilized rolling release

Not every part of an operating system benefits from the same update policy. Benchtop follows Tumbleweed for most of the software stack, keeping compilers, language runtimes, system libraries, firmware, graphics components, and development tools current.

The kernel and desktop move more cautiously because regressions there are disproportionately disruptive to a working machine. Benchtop uses an LTS kernel by default, with a current kernel available when newer hardware requires it. The Blueprint Environment follows the previous upstream-supported GNOME release, continuing to receive upstream fixes while avoiding the earliest part of each major desktop transition.

Benchtop therefore does not try to make every component equally conservative. It keeps the parts that benefit from currency moving with Tumbleweed while giving the kernel and desktop a longer stabilization window.

> For the desktop in particular, the informal version is to let other people be the beta testers.

## An immutable system, extended per user

Benchtop uses the immutable, transactional model provided by MicroOS. System updates are applied as new snapshots rather than as a sequence of changes to the running root filesystem, and an earlier snapshot remains available for rollback.

The system package set is not intended to be customized by the user. GNOME Software manages graphical applications, installing Flatpaks from Flathub into the user's own installation and AppImages from AppImageHub through an AppImage backend. AppImages are installed under `~/Applications/`, with desktop launchers generated for them. Homebrew provides additional command-line software and alternate or pinned toolchains in the user's profile. Project dependencies are managed by their native language ecosystems, and workloads that need an independently mutable Linux userspace belong in containers or virtual machines.

The operating system stays consistent while the user's environment remains flexible.

## The Blueprint Environment

Benchtop's desktop is the Blueprint Environment, a GNOME-based desktop whose behavior and presentation are maintained as part of the operating system.

Blueprint combines GNOME with a maintained set of extensions, defaults, and system configuration that define how the Benchtop desktop behaves. The objective is consistency across machines and releases: controls remain where users expect them, common actions behave predictably, and upgrading the operating system does not repeatedly reorganize the desktop.

The Blueprint Shell is the GNOME Shell-specific portion of that environment: its extensions, configuration, and behavior. Blueprint is not a fork of GNOME; it is Benchtop's integrated GNOME configuration, maintained as part of the operating system.

## Responsive under load

Benchtop treats interactive responsiveness as a primary performance requirement. Linux systems are often optimized for aggregate throughput, which is a sensible priority for servers and batch workloads but can be the wrong tradeoff for an interactive desktop. A machine can finish background work somewhat faster while becoming noticeably sluggish when CPU, memory, or storage activity competes with the person using it.

Benchtop gives priority to low and predictable interactive latency when that conflicts with extracting the last increment of throughput. Scheduling, memory management, storage behavior, power management, and related system policy are chosen with desktop responsiveness in mind.

The same approach extends to professional audio. Low-latency and real-time audio are intended workstation workloads, with the necessary scheduling policy, resource limits, permissions, and PipeWire integration configured at the system level.

## Hardware

If making hardware work properly requires a kernel patch, firmware, a system service, device permissions, a udev rule, or similar privileged integration, that integration belongs in the system image. Current examples include ASUS laptop support through asusctl, OpenRGB integration, Microsoft Surface hardware, and T2-based Macs. Benchtop runs on x86 and ARM workstations.

The supported baseline for x86 workstations is:

| Component | Baseline |
|---|---|
| CPU | x86-64-v3, 4 cores / 8 threads or better |
| Memory | 16 GB minimum; 32 GB or more recommended |
| Storage | 1 TB SSD minimum |
| Display | 1920×1080 minimum |
| Graphics | Hardware acceleration suitable for the supported GNOME/Wayland stack |

This is the hardware Benchtop is designed and tested for, not a promise that every workload fits within it. Large builds, several virtual machines at once, large datasets, and local models may need substantially more memory, storage, or GPU capacity. The storage figure refers to the capacity of the drive, not the size of the installation. The image itself follows Tumbleweed's x86-64 baseline and will boot on older processors, so less capable systems may work, but they are outside the design and test target. The x86-64-v3 level applies only to x86; ARM systems are supported against their own baseline.

{% include benchtop-handoff.html %}

</div>
