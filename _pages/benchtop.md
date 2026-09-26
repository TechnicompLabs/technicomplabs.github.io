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

Technicomp Benchtop Linux is a complete workstation operating system for professionals and technically sophisticated users who want to sit down and work without first assembling, and then continually maintaining, the Linux installation itself. Its components do not all follow the same update policy, because they do not all fail in the same way. A regression in the kernel or the desktop can take a working machine out of service for a day; a compiler or library that is two years old quietly costs time on every project. Benchtop Linux treats those risks differently, maintains the system layer as a single coherent snapshot, and leaves the user's own environment flexible.

## A stabilized workstation rolling release

Fixed-release distributions trade currency for predictability: the whole system is frozen for months or years, and fixes are backported into old versions. Rolling distributions make the opposite trade. Benchtop Linux is a stabilized workstation rolling release, which means it applies stability selectively instead of to everything.

Most of the software stack follows openSUSE Tumbleweed and stays current. Compilers, language runtimes, system libraries, firmware, graphics components, and development tools are more useful new than old, and Benchtop Linux does not try to freeze them. The components with a demonstrated tendency to cause disruptive workstation regressions are handled more conservatively. The kernel is an LTS kernel by default, with a newer kernel provided where hardware enablement requires it. The desktop follows the previous upstream-supported GNOME release, and does not move to each new major version as soon as it ships. That release still receives upstream bug and security fixes, and Benchtop Linux moves to the next major version when upstream ends support for the current one, by which time the new release has been through its initial round of fixes.

The short form of the policy is conservative where regressions are expensive, current where staleness is expensive. The informal version, for the desktop in particular, is to let other people be the beta testers.

## An immutable, complete system

Benchtop Linux is built on the immutable, transactional model of openSUSE MicroOS. Updates are applied to a new snapshot rather than to the running system, and the machine boots into that snapshot once the update is complete. If an update causes a problem, the previous snapshot is still available to roll back to.

The system package set is treated as a finished product, not a construction kit, and users are not expected to modify it. The image contains the operating system, the desktop platform, drivers, firmware, privileged services, hardware integration, development toolchains, and the other system-level plumbing that its intended workflows need. Because users do not add system packages, the image has to be complete. Benchtop Linux does not minimize the installation image by leaving out useful infrastructure that every user would then install again. If a workflow is part of the intended workstation experience and requires system-level support, that support is already present and configured.

## Extending your account

The working rule is simple: do not modify the operating system; extend your account.

Graphical applications are installed as Flatpaks in the user's profile. Because applications are decoupled from the operating system's lifecycle, they stay current even while the desktop platform advances more cautiously. Additional command-line software, and alternate or pinned versions of toolchains, are installed through Homebrew, also per user. Linux environments that genuinely need their own mutable package sets belong in containers or virtual machines, where they cannot alter the host.

Customization is still extensive. Users can customize extensively through Flatpak, Homebrew, language ecosystems, containers, virtual machines, their home directories, and ordinary user configuration. The boundary is around the base operating system, which stays consistent, recoverable, and identical to what was tested. A user who prefers a minimal base installation and wants to assemble every part of the workstation by hand will probably be better served by a different distribution, and that is a reasonable preference.

## Development environments

Benchtop Linux ships the major programming-language environments commonly used on Linux. The language implementations follow the rolling Tumbleweed packages, and each environment includes the compiler or interpreter, the standard library, the usual development infrastructure, and the package-management tooling of its ecosystem.

Project dependencies belong to the ecosystem, not to the system RPM database. Python dependencies come from the Python ecosystem and normally live in a virtual environment; Rust dependencies belong to Cargo, JavaScript dependencies to npm, and Ruby libraries to RubyGems. The fact that a library is packaged as an RPM is not a reason to install it into the system image. When a project needs a different or pinned version of a language than the one in the current image, Homebrew or the ecosystem's own version manager provides it, so the system image does not need to carry parallel historical versions.

## The Blueprint Environment

The desktop in Benchtop Linux is the Blueprint Environment, an integrated desktop experience built on GNOME. It covers the interaction model, defaults, visual and behavioral consistency, extensions, configuration, and workflow conventions of the desktop, and how the desktop fits with the rest of the system. The Blueprint Shell is the part of that environment specific to GNOME Shell: the selected extensions, the Shell configuration, and its behavior.

Blueprint is not a fork of GNOME or a separately implemented desktop. It is a GNOME-based environment maintained as part of Benchtop Linux, with the goal that the workstation feels like one designed system, without the seams of an upstream desktop plus an assortment of unrelated customizations. That goal is also a reason for following the previous supported GNOME release. The desktop, including the extensions and behavior that define Blueprint, is a maintained product surface, and it should not change unpredictably every six months.

## Latency and professional audio

Linux is often tuned for maximum aggregate throughput. That is the right choice for a server or a batch-processing system, but it can be the wrong one for a machine with a person sitting in front of it. A system can post excellent throughput benchmarks and still feel sluggish, because interactive tasks wait behind background work when the CPU, memory, or storage is under pressure.

Benchtop Linux optimizes a workstation as a workstation. When the two goals conflict, it gives priority to low and predictable interactive latency over the last increment of aggregate throughput. The intent is not to give up throughput needlessly; responsiveness comes first, and performance is pursued within that constraint.

Real-time and low-latency audio are intended workloads. The system ships with the scheduling, resource limits, permissions, PipeWire configuration, and related plumbing that professional low-latency audio work requires, so that users do not have to convert a general-purpose desktop into an audio workstation after installation.

## Hardware integration

Benchtop Linux runs on x86 and ARM workstations. The principle for hardware support is the same as for the rest of the system: if making hardware work properly requires changing the operating system, through kernel patches, firmware, udev rules, privileged services, or permissions, that integration belongs in Benchtop Linux rather than in a setup guide that asks every user to reconstruct it. Current examples include ASUS hardware through asusctl, RGB device control through OpenRGB, Microsoft Surface devices, and Apple computers with the T2 security chip. The newer kernel option exists for the same reason, for hardware that needs support not yet present in the LTS kernel.

## Supported workflows

Benchtop Linux is prepared for the kinds of work listed below. The system image provides what each needs at the operating-system level, such as drivers, services, permissions, and toolchains. Most of the applications used in these fields are installed per user, as Flatpaks, through Homebrew, or inside containers, where they stay current and under the user's control.

<dl class="threads">
  <div><dt>AI</dt><dd>Local inference and model development</dd></div>
  <div><dt>Virtualization</dt><dd>KVM and QEMU virtual machines, and containers</dd></div>
  <div><dt>Software development</dt><dd>Compilers, interpreters, debuggers, and build tools</dd></div>
  <div><dt>System administration and DevOps</dt><dd>Remote access, configuration management, backup, and storage</dd></div>
  <div><dt>Security</dt><dd>Vulnerability assessment, network analysis, and digital forensics</dd></div>
  <div><dt>Reverse engineering</dt><dd>Disassembly, decompilation, and debugging</dd></div>
  <div><dt>Content creation</dt><dd>Low-latency audio, video, graphics, and publishing</dd></div>
</dl>

## Status

Benchtop Linux is in alpha. Installable images are released through the openSUSE Build Service.

- [Source and packaging on GitHub](https://github.com/TechnicompLabs)
- [Build Service project](https://build.opensuse.org/project/show/home:technicomp)
- [Design notes](https://github.com/pauldmartinphd/benchtop-notes)

</div>
