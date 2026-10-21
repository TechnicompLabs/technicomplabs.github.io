---
title: "llAMDa.cpp"
kicker: "Project"
permalink: /projects/llamda-cpp/
subtitle: "An AMD-focused llama.cpp fork for RDNA, Strix Halo, and Vega."
---

[llAMDa.cpp](https://github.com/pauldmartinphd/llAMDa.cpp) is a fork of llama.cpp that I maintain to bring useful AMD performance work into one repository, without requiring a collection of custom launchers. It began as a V620-specific build for running newer large Mixture-of-Experts models on Galactus, my EPYC inference server with four Radeon Pro V620 GPUs. I then widened it to cover the other AMD hardware I care about: older Vega cards, RDNA2 cards such as the V620, and Strix Halo.

The fork grew out of the [LLM Performance Engineering Notebook](https://github.com/pauldmartinphd/llm-performance-engineering-notebook), where I measure local inference and test changes to llama.cpp itself. This page describes what the fork contains, what it takes from other people's work, and what I have and have not validated.

## Why a fork

AMD GPUs differ more from one another than the product names suggest. A Vega card, an RDNA2 workstation card, and a Strix Halo system with unified memory have different execution and memory constraints, so a change that helps one of them needs the right device and workload before it becomes a default for the others. Several AMD-focused forks of llama.cpp already exist, each tuned to its owner's hardware, and many of their changes are small and worth keeping.

I wanted those changes available together, working through the ordinary build and runtime options, with their authorship and evidence recorded. I also wanted a place to carry the scheduler changes that came out of my own measurements on Galactus.

## Branches

The common changes live on a default branch, and architecture-specific work builds on that base in separate branches.

| Branch | Purpose |
|---|---|
| [`amd-main`](https://github.com/pauldmartinphd/llAMDa.cpp/tree/amd-main) | The default branch. It follows upstream llama.cpp and carries the common AMD changes, Unsloth's DeepSeek-V4.1/DSpark implementation, and my MoE prefill patch. |
| [`rdna-dev`](https://github.com/pauldmartinphd/llAMDa.cpp/tree/rdna-dev) | RDNA development, including the V620 work that started the fork. |
| [`halo-dev`](https://github.com/pauldmartinphd/llAMDa.cpp/tree/halo-dev) | Strix Halo development. |
| [`vega-dev`](https://github.com/pauldmartinphd/llAMDa.cpp/tree/vega-dev) | Vega development, including the gfx900 and gfx906 targets. |

All four branches include the prefill changes described below.

## What the fork carries, and from whom

Most of the code in the fork is other people's work, and I keep their authorship and original commit references. The credits file in the repository lists each source.

| Change | Source |
|---|---|
| Normalization kernels compiled with loops only for grids that exceed launch limits | mxxm-t, [mx-llama.cpp](https://github.com/mxxm-t/mx-llama.cpp) |
| Direct handling of small readbacks on unified-memory Vulkan systems | Laurent Zuijdwijk, [halo-box/strix-llama.cpp](https://github.com/halo-box/strix-llama.cpp) |
| Wider RDNA3 decode for the MXFP4 and Q8_0 quantization formats | Edwin Brown, [llama.cpp-rdna2](https://github.com/edwinbrowwn/llama.cpp-rdna2) |
| DeepSeek-V4.1-Flash-1017 and DSpark speculative decoding support | Daniel Han and Unsloth, [llama.cpp PR 30817](https://github.com/ggml-org/llama.cpp/pull/30817) |
| Multi-GPU MoE prefill distribution | My own patch, from the notebook |

Upstream llama.cpp already supplies gfx900 dispatch, RDNA3.5 matrix-multiply tuning, and a BF16 fallback for hardware without native BF16 support. I did not add those and do not present them as part of this fork's contribution.

The selection is deliberately narrow. My first import combined several forks at once, and I reverted it, because a whole-fork benchmark does not show whether an isolated table or kernel is the part that helps. I also declined changes that depended on a different upstream implementation, and one Strix Halo change whose own author notes that it violates an alignment requirement of the cooperative-matrix path. The goal is a small, reviewable set of changes, each with evidence for keeping it. The audit documents what was retained, what was removed, and why.

## The prefill patch

The patch addresses a problem I found on Galactus. When a model's routed experts sit in system RAM, llama.cpp concentrated the offloaded expert computation on one GPU during prefill, and a routing-index readback forced the work to wait on a synchronization. The patch has two parts: it distributes eligible expert work across the available GPUs, and it bypasses the readback for sufficiently large batches.

In the July GLM-5.2 experiment on Galactus, which then had 1 TB of RAM, the combined patch raised prompt-processing throughput on an 8,192-token prompt from 104.97 to 119.36 tokens/s, a gain of 13.7%. Distribution alone moved it only from 104.97 to 105.71 tokens/s, so both parts were needed. Token generation stayed at about 5.5 tokens/s. The patch addresses prefill and was not expected to change decode.

The port in the fork applies the policy to eligible operations on batches of at least 32 tokens. The bypass additionally requires at least eight routing selections per model expert, and it copies the full expert tensor instead of reading back the selected IDs. Under skewed routing, that can transfer weights that go unused. Setting `GGML_MOE_PREFILL=0` disables both changes, which makes controlled comparisons straightforward. The patch does not make experts persist in VRAM, does not enable tensor parallelism, and does not promise a decode speedup.

## What I have and have not validated

The fork has been built and tested on a Mac without AMD hardware, so its validation is limited to what a CPU build can show.

- The Release CPU build passes, along with the 12 regression tests and 145 normalization backend cases I selected.
- A test harness exercises the actual prefill selection and expert-copy code, including four-device selection, eligible subsets, fallback behavior, the batch-size thresholds, numerical equivalence under skewed routing, and the disable switch. All of those checks pass.
- None of this exercises HIP transfers or GPU kernels. The adapted normalization change has not been compiled for HIP locally, and the per-architecture changes still need testing on matching hardware.
- I have not benchmarked this revision on Galactus. The 13.7% figure comes from the earlier V620 build, and it does not establish a gain for the newer models. The other patches' performance figures are the source authors' own measurements on their hardware, not measurements of this combined branch.

The next step is a controlled A/B comparison on Galactus, using `GGML_MOE_PREFILL=0` against the default, with the source revision, model files, and launch settings recorded alongside the results.

## Related measurements in the notebook

The notebook now also holds measurements from the earlier V620 build, with the caveats stated in the entries.

- **GLM-5.3-Flash:** automatic placement generated 19.1 to 19.4 tokens/s, while forcing the routed experts into system RAM gave about 13.7 to 14.4 tokens/s. I did not record which tensors the automatic placement chose, so I cannot say which mechanism produced the difference.
- **DeepSeek-V4.1-Flash:** a target-only run generated 11.2 tokens/s. The DSpark draft model then failed to load when a GPU could not allocate another 2,464 MiB, so I have no speculative-decoding result for that model.
- **STREAM bandwidth is not a decode prediction.** A memory-bandwidth figure divided by active expert bytes gives an optimistic bound, not an expected token rate, because inference also selects and decodes experts and finishes the rest of the model before each token completes.

## Building, license, and credits

The README and `docs/amd-main.md` in the repository give build commands for HIP targets such as gfx900, gfx906, gfx1030, gfx1100, and gfx1151, and for Vulkan on Strix Halo. The fork uses llama.cpp's MIT license, and its history and license notices remain intact. It is an independent fork, not an official AMD or llama.cpp project.

- [llAMDa.cpp repository](https://github.com/pauldmartinphd/llAMDa.cpp)
- [Source credits](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/CREDITS-AMD.md)
- [Patch audit and build notes](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/docs/amd-main.md)
- [Multi-GPU MoE prefill notes](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/docs/moe-prefill-offload.md)
- [Development notes in the lab notebook](https://github.com/pauldmartinphd/llm-performance-engineering-notebook/blob/main/results/lab-notebook/14-llamda-amd-fork.md)
