---
layout: default
title: "Technicomp Benchtop Linux"
permalink: /benchtop/
description: "Technicomp Benchtop Linux, the operating system for the technical workbench: an immutable, LTS-based GNOME desktop that rolls."
---
<div class="wrap">
  <header class="page-header product-header">
    <p class="kicker">Technicomp Benchtop Linux &middot; Alpha</p>
    <h1>The operating system for the technical workbench.</h1>
    <p class="lede">An immutable, transactional GNOME desktop for desktop and laptop workstations, built on openSUSE Tumbleweed and MicroOS.</p>
  </header>

  {% include benchtop.html compact=true %}
</div>

<div class="measure prose product-body" markdown="1">

## Rolling updates on an LTS foundation

Benchtop Linux is immutable. Each update installs as a new snapshot, and you can roll back to the previous one if an update causes a problem. The system rolls, but its core packages, including the kernel, track LTS releases, and GNOME stays one release behind current. Security fixes arrive promptly, and there are no major-version upgrades to manage. Applications come from Flatpak and command-line tools from Homebrew, which keeps the base system small.

The design aims for stability. A separate research project is testing whether it also improves security compared with conventional stable distributions, which backport fixes into older package versions.

## Tuned for the bench

Benchtop Linux prioritizes low interactive latency and workstation stability, so the desktop stays responsive even when the machine is busy. It is built for the threads of technical work: AI, virtualization, software development, system administration, security, reverse engineering, and content creation.

## Hardware

Benchtop Linux is optimized for widely used hardware, including Microsoft Surface devices, Apple MacBooks, Lenovo ThinkPads, ASUS and Razer laptops, and common peripherals. ARM will be a supported architecture in the future.

## Status

Benchtop Linux is in alpha. Installable disk images are released through the openSUSE Build Service.

- [Source and packaging on GitHub](https://github.com/TechnicompLabs)
- [Build Service project](https://build.opensuse.org/project/show/home:technicomp)
- [Design notes](https://github.com/pauldmartinphd/benchtop-notes)

</div>
