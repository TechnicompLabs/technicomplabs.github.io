---
layout: default
title: "Technicomp Benchtop Linux"
permalink: /benchtop/
description: "Technicomp Benchtop Linux, the operating system for the technical workbench: an immutable GNOME desktop with an LTS core."
---
<div class="wrap">
  <header class="page-header product-header">
    <p class="kicker">Technicomp Benchtop Linux &middot; Alpha</p>
    <h1>The operating system for the technical workbench.</h1>
    <p class="lede">An immutable GNOME desktop for workstations, built on openSUSE Tumbleweed and MicroOS.</p>
  </header>

  {% include benchtop.html compact=true %}
</div>

<div class="measure prose product-body" markdown="1">

## Rolling updates on an LTS foundation

Even though the system uses a rolling release model, its core packages track LTS releases and its desktop is GNOME Oldstable. Security fixes arrive promptly, with no major-version upgrades. Every update is a snapshot that can be rolled back.

Flatpak provides applications and Homebrew provides command-line tools. Both are managed per user.

## Tuned for the bench

Benchtop Linux is tuned for low interactive latency and stability, so that its desktop remains responsive under heavy load.

## Threads of technical work

<dl class="threads">
  <div><dt>AI</dt><dd>Local inference and model development</dd></div>
  <div><dt>Virtualization</dt><dd>KVM and QEMU virtual machines and containers</dd></div>
  <div><dt>Software development</dt><dd>Editors, debuggers, and build tools</dd></div>
  <div><dt>System administration and DevOps</dt><dd>Remote access, configuration management, backup, and storage tools</dd></div>
  <div><dt>Security</dt><dd>Vulnerability assessment, network analysis, and digital forensics</dd></div>
  <div><dt>Reverse engineering</dt><dd>Disassembly, decompilation, and debugging</dd></div>
  <div><dt>Content creation</dt><dd>Audio, video, graphics, and publishing tools</dd></div>
</dl>

## Hardware

Benchtop Linux runs on x86 and ARM workstations. Its LTS and Current kernels include patches for laptops, tablets, convertibles, and common peripherals.

## Status

Benchtop Linux is in alpha. Installable images are released through the openSUSE Build Service.

- [Source and packaging on GitHub](https://github.com/TechnicompLabs)
- [Build Service project](https://build.opensuse.org/project/show/home:technicomp)
- [Design notes](https://github.com/pauldmartinphd/benchtop-notes)

</div>
