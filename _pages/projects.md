---
title: "Built in the lab"
permalink: /projects/
kicker: "Projects"
subtitle: "Software, patches, and builds from the lab, released openly where licensing allows."
---

## Technicomp Benchtop Linux

Technicomp Benchtop Linux is a stabilized workstation rolling release derived from openSUSE Tumbleweed and MicroOS. The kernel and the desktop, the components most likely to cause disruptive regressions, move conservatively, while the rest of the system stays current. The operating system is an immutable, complete image that users extend through their own accounts rather than modify. It is currently in alpha.

[About Benchtop Linux &rarr;](/benchtop/)

## LLM Performance Engineering Notebook

An open lab notebook on the inference speed limits of large Mixture-of-Experts models, measured on the lab's own servers. It documents the method (measure the hardware limit first, predict performance from a model, and then change one variable at a time), per-model results, raw logs, and the hypotheses that testing did not support.

Its first concrete result is a patch to the llama.cpp scheduler that raised prefill throughput 13.7% on the reference machine. The patch spreads offloaded expert computations across all GPUs instead of sending them to one, and removes an unnecessary GPU-to-host transfer during prefill. The approach applies to any configuration that keeps expert weights in system memory, and the patch is being submitted upstream.

The measurements run on Galactus, an AMD EPYC 7713 system with 2 TB of eight-channel DDR4 memory and four AMD Radeon Pro V620 GPUs (128 GB of VRAM), built to run large open-weight models locally. The design prioritizes memory bandwidth, which limits token generation for these models.

<figure>
  <img src="/assets/images/galactus-build.jpg" alt="Galactus: LLM inference server build with four AMD Radeon Pro V620 GPUs and an EPYC 7713 in a Jonsbo N5 chassis">
  <figcaption>Galactus mid-build: four Radeon Pro V620s with custom fan shrouds in a Jonsbo N5 chassis.</figcaption>
</figure>

- [Notebook, results, and raw logs](https://github.com/pauldmartinphd/llm-performance-engineering-notebook)
- [The prefill patch](https://github.com/pauldmartinphd/llm-performance-engineering-notebook/tree/main/patches)
