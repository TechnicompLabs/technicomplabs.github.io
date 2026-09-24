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
    <p class="lede">An immutable, transactional GNOME desktop for desktop and laptop workstations, built on openSUSE Slowroll.</p>
  </header>

  {% include benchtop.html compact=true %}
</div>

<div class="measure prose product-body" markdown="1">

## The LTS distro that rolls

Benchtop Linux keeps openSUSE Aeon's foundation (read-only btrfs snapshots, UEFI with systemd-boot, and Ignition first-boot configuration) and departs from it in two deliberate ways: a verbatim upstream kernel.org LTS kernel, and GNOME held one release behind current, both delivered through the openSUSE Build Service. Core packages stay on LTS releases while the rest of the system rolls, so fixes arrive promptly without every upgrade being a new major version. Graphical applications come from Flatpak and command-line tooling from Homebrew, which keeps the base small.

## Tuned for the bench

The system ships tuned defaults rather than stock ones: kernel and network sysctls, BFQ and Kyber I/O scheduling, transparent-huge-page and multi-generational-LRU policy, and realtime-audio limits, all layered as drop-ins that sort after openSUSE's vendor defaults and stay overridable. The goal is a desktop that stays responsive under heavy load. It comes ready for AI, virtualization, software development, system administration, security, audio, and content-creation work.

## Hardware

The kernel is patched to support widely used hardware, including Microsoft Surface devices and Apple MacBooks, and userspace support is included for common peripherals. ARM will be a supported architecture in the future.

## Why it exists

Benchtop Linux grows out of research into vulnerability divergence and patch management across Linux distributions. It is an applied answer to a question raised by that work: how a stable distribution can deliver enterprise-grade reliability without the incomplete backports and version drift that let a package's real vulnerability exposure diverge from its upstream version.

## Status

Benchtop Linux is in alpha. Installable disk images are released through the openSUSE Build Service.

- [Source and packaging on GitHub](https://github.com/TechnicompLabs)
- [Build Service project](https://build.opensuse.org/project/show/home:technicomp)
- [Design notes](https://github.com/pauldmartinphd/benchtop-notes)

</div>
