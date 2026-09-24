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

Benchtop Linux is immutable. Each update is installed as a new snapshot, and the previous snapshot can be restored if an update causes a problem. The system is a rolling release, but its core packages track long-term-support releases and its desktop is GNOME Oldstable. Security fixes arrive promptly, and there are no major-version upgrades to manage. Applications are installed through Flatpak and command-line tools through Homebrew, which keeps the base system small.

Benchtop Linux is designed for stability. Whether its release model also improves security relative to conventional stable distributions, which backport fixes into older package versions, is the subject of separate research.

## Tuned for the bench

Benchtop Linux is tuned for low interactive latency and stability, and its desktop remains responsive when the system is under heavy load. It supports the following threads of technical work without additional configuration: AI, virtualization, software development, system administration, security, reverse engineering, and content creation.

## Hardware

Benchtop Linux supports workstation-class x86 and ARM hardware and ships two kernels, a long-term-support kernel and a current kernel, both patched for hardware support. It is also optimized for Microsoft Surface devices, Apple MacBooks, Lenovo ThinkPads, and ASUS and Razer laptops, as well as common peripherals.

## Status

Benchtop Linux is in alpha. Installable disk images are released through the openSUSE Build Service.

- [Source and packaging on GitHub](https://github.com/TechnicompLabs)
- [Build Service project](https://build.opensuse.org/project/show/home:technicomp)
- [Design notes](https://github.com/pauldmartinphd/benchtop-notes)

</div>
