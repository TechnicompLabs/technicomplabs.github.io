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

Technicomp Benchtop Linux is a workstation operating system for professionals and technically sophisticated users who want to use the machine rather than spend their time maintaining it. It is opinionated about the operating system and flexible about everything the user builds above it. It is intended for software development, local AI work, virtualization, system administration, security work, reverse engineering, and audio and video production.

## A stabilized workstation rolling release

The parts of an operating system do not all fail in the same way. A regression in the kernel or the desktop can take a working machine out of service for a day, while a compiler or library that is two years old quietly costs time on every project. Benchtop Linux is a stabilized workstation rolling release, and it handles those two risks separately.

Most of the system follows openSUSE Tumbleweed, so compilers, language runtimes, libraries, firmware, and graphics stay current. The kernel and the desktop, the components with a history of disruptive workstation regressions, move more conservatively. The kernel follows an LTS branch by default, with a current kernel available where hardware needs it. The desktop follows the previous upstream-supported GNOME release, which still receives upstream bug and security fixes. Benchtop Linux moves to the next major version when upstream support for the current one ends, by which time the new release has had its first round of fixes. For the desktop in particular, the informal version of this policy is to let other people be the beta testers.

## An immutable system, extended per user

The operating system uses the immutable, transactional model of openSUSE MicroOS. Each update is written to a new snapshot that the machine switches to at the next boot, and the previous snapshot remains available if something goes wrong.

The system package set is maintained as one tested image, and individual users do not modify it. Customization happens above that boundary. Applications are installed per user as Flatpaks, additional command-line tools and pinned toolchains come from Homebrew, project dependencies come from each language's own package manager, and anything that needs its own mutable system runs in a container or virtual machine. The base stays consistent and recoverable, and the user's own environment remains theirs to arrange.

## A complete system

Because users do not add system packages, the image has to be complete. If a supported workflow needs system-level plumbing, that plumbing ships with Benchtop Linux and is already configured. Users should not have to add repositories, patch kernels, install privileged daemons, write udev rules, or edit PAM configuration to make supported hardware or software work. Current hardware examples include ASUS hardware through asusctl, RGB control through OpenRGB, Microsoft Surface devices, and Apple computers with the T2 chip. The major programming-language toolchains are included as well, at current Tumbleweed versions.

## Latency and professional audio

Linux is commonly tuned for aggregate throughput. That suits servers and batch jobs, but it can leave a desktop sluggish when background work competes with the person at the keyboard. Benchtop Linux treats a workstation as a workstation: when the two goals conflict, low and predictable interactive latency takes priority over the last increment of throughput. Low-latency and real-time audio are intended workloads, and the scheduling, permissions, and PipeWire configuration they need are part of the system from the start.

## The Blueprint Environment

The desktop is the Blueprint Environment, an integrated desktop built on GNOME. It covers the desktop's interaction model, defaults, visual consistency, extensions, and conventions, and how they fit with the rest of the system. The Blueprint Shell is the part specific to GNOME Shell: its extensions, configuration, and behavior. Blueprint is not a fork of GNOME. It is maintained as part of the product so that the workstation behaves like one designed system, and it is another reason the desktop follows the previous supported GNOME release: a maintained desktop should not change unpredictably every six months.

{% include benchtop-handoff.html %}

</div>
