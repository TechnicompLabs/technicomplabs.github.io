---
title: "Projects"
permalink: /projects/
layout: single
author_profile: true
toc: true
toc_sticky: true
excerpt: "Software and hardware projects from Technicomp Labs, from a purpose-built Linux distribution to open performance research."
---

The lab's output beyond the writing: software, patches, and builds. Everything
here is independent work, released openly where licensing allows.

## Technicomp Benchtop Linux

**Launching soon.** A custom Linux distribution designed as an integrated
benchtop environment for technical computing, systems analysis, and engineering
work — built and tuned for the workflows this lab actually runs: firmware
analysis, reverse engineering, source code review, and embedded systems
development.

Benchtop Linux also grows out of research into vulnerability divergence and
patch management across Linux distributions. It is an applied answer to a
question raised by that work: how a stable distribution can deliver
enterprise-grade reliability without the incomplete backports and version
drift that let a package's real vulnerability exposure diverge from its
upstream version.

## LLM Performance Engineering Notebook

An open lab notebook of LLM performance engineering: experiments and results
in finding and raising the inference speed limits of large Mixture-of-Experts
models, measured on the lab's own servers. It documents the methodology —
measure the hard physical limit first, predict from a model, then change one
variable at a time — along with per-model results, raw logs, and a table of
hypotheses that didn't survive measurement.

**[github.com/pauldmartinphd/llm-performance-engineering-notebook](https://github.com/pauldmartinphd/llm-performance-engineering-notebook)**

## llama.cpp MoE prefill patch

Three small edits to the llama.cpp scheduler that raised Mixture-of-Experts
prefill throughput 13.7% on the lab's reference machine: spread offloaded
expert matmuls across all GPUs instead of serializing them onto one, and skip
a redundant GPU-to-host read during prefill. The method applies to any
CPU-MoE offload setup, and the patch is being submitted upstream.

**[Patch and write-up](https://github.com/pauldmartinphd/llm-performance-engineering-notebook/tree/main/patches)**

## Galactus

The machine behind the inference measurements: an AMD EPYC 7713 with 2 TB of
8-channel DDR4 and four AMD Radeon Pro V620 GPUs (128 GB VRAM), built to run
frontier-class open-weight models locally for about $9,000. The design bets
everything on memory bandwidth, and the measurements in the notebook above
show why that bet pays.

<figure>
  <img src="/assets/images/galactus-build.jpg" alt="Galactus: LLM inference server build with four AMD Radeon Pro V620 GPUs and an EPYC 7713 in a Jonsbo N5 chassis">
  <figcaption>Galactus mid-build: four Radeon Pro V620s with custom fan shrouds in a Jonsbo N5 chassis.</figcaption>
</figure>
