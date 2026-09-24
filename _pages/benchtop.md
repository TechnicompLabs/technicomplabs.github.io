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

Benchtop Linux is immutable. Each update is installed as a new snapshot, and the previous snapshot can be restored if an update causes a problem. Even though the system uses a rolling release model, its core packages track long-term-support releases and its desktop is GNOME Oldstable. Security fixes arrive promptly, and there are no major-version upgrades to manage. Applications are installed through Flatpak and command-line tools through Homebrew, which keeps the base system small.

## Tuned for the bench

Benchtop Linux is tuned for low interactive latency and stability, so that its desktop remains responsive when the system is under heavy load.

## Threads of technical work

Benchtop Linux is configured for each of the following threads of technical work without additional setup.

<dl class="threads">
  <div><dt>AI</dt><dd>Local model inference and development.</dd></div>
  <div><dt>Virtualization</dt><dd>Virtual machines and containers built on KVM and QEMU.</dd></div>
  <div><dt>Software development</dt><dd>Editors, debuggers, and build tooling, with command-line tools available through Homebrew.</dd></div>
  <div><dt>System administration</dt><dd>Remote access, backup, and storage tools for managing multiple machines.</dd></div>
  <div><dt>Security</dt><dd>Network analysis, digital forensics, and password auditing.</dd></div>
  <div><dt>Reverse engineering</dt><dd>Disassembly, decompilation, debugging, and binary inspection.</dd></div>
  <div><dt>Content creation</dt><dd>Audio, video, graphics, and publishing applications.</dd></div>
</dl>

## Hardware

Benchtop Linux runs on workstation-class x86 and ARM hardware. It ships two kernels, LTS and Current, and both include patches that extend support for laptops, tablets, convertibles, and common peripherals.

## Status

Benchtop Linux is in alpha. Installable disk images are released through the openSUSE Build Service.

- [Source and packaging on GitHub](https://github.com/TechnicompLabs)
- [Build Service project](https://build.opensuse.org/project/show/home:technicomp)
- [Design notes](https://github.com/pauldmartinphd/benchtop-notes)

</div>
