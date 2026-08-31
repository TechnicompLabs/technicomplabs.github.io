---
title: "The Lab"
permalink: /lab/
layout: single
author_profile: true
toc: false
excerpt: "The workshop behind the posts: test benches, instrumentation, and the machines used for performance work and hardware restoration."
header:
  og_image: /assets/images/lab-bench-window.jpg
---

The lab is a purpose-built basement workshop that supports both sides of what
Technicomp Labs does: modern systems-performance work and vintage hardware
restoration.

<figure>
  <img src="/assets/images/lab-overview.jpg" alt="Workshop overview: workbench with pegboard bin storage, parts organizers, and an open-frame hardware test bench">
  <figcaption>The workshop: main bench, pegboard small-parts storage, and the test-bench corner.</figcaption>
</figure>

## Electronics and repair bench

The main bench is set up for board-level work — a programmable bench power
supply, soldering and hot-air rework stations, an oscilloscope, and wall-mounted
parts organizers stocked with the small components that vintage machines
consume: capacitors, voltage regulators, fuses, and connector hardware. An
open-frame test bench beside it holds whatever motherboard is currently under
diagnosis, so boards can be brought up and probed outside a case.

<figure>
  <img src="/assets/images/lab-bench-window.jpg" alt="Electronics workbench with parts organizers, an Apple IIe awaiting repair, and an open-frame hardware test bench">
  <figcaption>The repair corner: an Apple IIe on the bench, parts organizers in the window, and the open-frame test bench on the right.</figcaption>
</figure>

## Compute

Modern compute lives in a mobile rack: a virtualization host with hot-swap
storage on a UPS, plus the custom-built machines used for the performance
experiments written up here — including Galactus, the EPYC inference server.
The ongoing measurements from these machines are published in the
[LLM Performance Engineering Notebook](https://github.com/pauldmartinphd/llm-performance-engineering-notebook).

<figure>
  <img src="/assets/images/galactus-build.jpg" alt="Galactus: LLM inference server build with four AMD Radeon Pro V620 GPUs and an EPYC 7713 in a Jonsbo N5 chassis">
  <figcaption>Galactus — EPYC 7713, 2 TB DDR4, and four Radeon Pro V620s in a Jonsbo N5 chassis.</figcaption>
</figure>

## Staging and burn-in

A second bench stages machines that are between arrival and restoration —
network switches and a KVM for bringing several systems up at once, with
recently finished machines shelved below.

<figure>
  <img src="/assets/images/lab-staging-bench.jpg" alt="Staging workbench with a Power Macintosh G3 all-in-one, laptops, rack-mount network switches, and Power Mac G5 towers below">
  <figcaption>The staging bench: a Power Macintosh G3 all-in-one under test, with Power Mac G5s queued underneath.</figcaption>
</figure>
