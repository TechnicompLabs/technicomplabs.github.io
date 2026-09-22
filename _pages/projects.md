---
title: "Built in the lab"
permalink: /projects/
kicker: "Projects"
subtitle: "Software, patches, and builds from the lab, released openly where licensing allows."
---

{% include benchtop.html %}

## About the distribution

Benchtop Linux keeps openSUSE Aeon's foundation (read-only btrfs snapshots, UEFI with systemd-boot, and Ignition first-boot configuration) and departs from it in two deliberate ways: a verbatim upstream kernel.org LTS kernel, and GNOME held one release behind current, both delivered through the openSUSE Build Service. The kernel is patched to support widely used hardware, including Microsoft Surface and Apple MacBooks, and ARM will be a supported architecture in the future.

It ships tuned defaults rather than stock ones: kernel and network sysctls, BFQ and Kyber I/O scheduling, transparent-huge-page and multi-generational-LRU policy, and realtime-audio limits, all layered as drop-ins that sort after openSUSE's vendor defaults and stay overridable. The goal is a desktop that stays responsive under heavy load.

Benchtop Linux also grows out of research into vulnerability divergence and patch management across Linux distributions. It is an applied answer to a question raised by that work: how a stable distribution can deliver enterprise-grade reliability without the incomplete backports and version drift that let a package's real vulnerability exposure diverge from its upstream version.

Benchtop Linux is in alpha: installable disk images are released through the openSUSE Build Service, with the graphical installer and branding packages as the next packaging tasks. A dedicated site at benchtoplinux.org is in preparation.

## LLM Performance Engineering Notebook

An open lab notebook of LLM performance engineering: experiments and results in finding and raising the inference speed limits of large Mixture-of-Experts models, measured on the lab's own servers. It documents the methodology (measure the hard physical limit first, predict from a model, then change one variable at a time) along with per-model results, raw logs, and a table of hypotheses that didn't survive measurement.

The notebook's first concrete result is a patch to the llama.cpp scheduler: three small edits that raised Mixture-of-Experts prefill throughput 13.7% on the reference machine, by spreading offloaded expert matmuls across all GPUs instead of serializing them onto one and skipping a redundant GPU-to-host read during prefill. The method applies to any CPU-MoE offload setup, and the patch is being submitted upstream.

The machine behind the measurements is Galactus: an AMD EPYC 7713 with 2 TB of 8-channel DDR4 and four AMD Radeon Pro V620 GPUs (128 GB VRAM), built to run frontier-class open-weight models locally. The design bets everything on memory bandwidth, and the measurements in the notebook show why that bet pays.

<figure>
  <img src="/assets/images/galactus-build.jpg" alt="Galactus: LLM inference server build with four AMD Radeon Pro V620 GPUs and an EPYC 7713 in a Jonsbo N5 chassis">
  <figcaption>Galactus mid-build: four Radeon Pro V620s with custom fan shrouds in a Jonsbo N5 chassis.</figcaption>
</figure>

- [Notebook, results, and raw logs](https://github.com/pauldmartinphd/llm-performance-engineering-notebook)
- [The prefill patch](https://github.com/pauldmartinphd/llm-performance-engineering-notebook/tree/main/patches)
