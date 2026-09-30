---
title: "Built in the lab"
permalink: /projects/
kicker: "Projects"
subtitle: "Software, patches, and builds from the lab, released openly where licensing allows."
---

## Technicomp Benchtop Linux

I’m building Technicomp Benchtop Linux as a stabilized workstation rolling release derived from openSUSE Aeon and built on Tumbleweed. Its kernel and desktop move more cautiously because regressions there can interrupt work, while the Tumbleweed userspace keeps development tools current. The immutable system image includes the Blueprint Environment and workstation tuning that prioritizes low interactive latency. Benchtop is currently in alpha.

[About Benchtop Linux &rarr;](/benchtop/)

## LLM Performance Engineering Notebook

The notebook records my experiments on local inference, beginning with large Mixture-of-Experts models on Galactus. The investigation starts with measured hardware limits and a model of time per token, then tests changes to the configuration and code. Per-model results, raw logs, and the hypotheses that testing did not support make the reasoning behind each conclusion available alongside the measurements.

One result is a patch to the llama.cpp scheduler that raised prefill throughput 13.7% on Galactus in the July comparison, with 1 TB of RAM. It distributes offloaded expert computation across the GPUs and bypasses a routing-index readback that serialized the work during prefill. Distribution alone produced no meaningful throughput gain; both changes were needed for the measured improvement. The patch addresses CPU-resident expert offload, but the result is specific to the tested configuration. The source and measurements are public; I have not submitted an upstream PR.

Galactus now has an AMD EPYC 7713, 2 TB of eight-channel DDR4 memory, and four AMD Radeon Pro V620 GPUs (128 GB of VRAM). I built it to run large open-weight models locally, prioritizing memory bandwidth because streaming CPU-resident expert weights accounts for much of the token-generation time. The notebook records the memory population and build used for each comparison.

<figure>
  <img src="/assets/images/galactus-build.jpg" alt="Galactus: LLM inference server build with four AMD Radeon Pro V620 GPUs and an EPYC 7713 in a Jonsbo N5 chassis">
  <figcaption>Galactus mid-build: four Radeon Pro V620s with custom fan shrouds in a Jonsbo N5 chassis.</figcaption>
</figure>

- [Notebook, results, and raw logs](https://github.com/pauldmartinphd/llm-performance-engineering-notebook)
- [The prefill patch](https://github.com/pauldmartinphd/llm-performance-engineering-notebook/tree/main/patches)
