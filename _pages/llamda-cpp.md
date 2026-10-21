---
title: "llAMDa.cpp"
kicker: "Project"
permalink: /projects/llamda-cpp/
subtitle: "An AMD-focused llama.cpp fork for RDNA, Strix Halo, and Vega."
---

[llAMDa.cpp](https://github.com/pauldmartinphd/llAMDa.cpp) is a fork of llama.cpp that I maintain to bring useful AMD performance work into one repository, without requiring a collection of custom launchers. I started with a V620-specific build to run newer large mixture-of-experts (MoE) models on Galactus, my EPYC inference server with four Radeon Pro V620 GPUs. I expanded that work to include older Vega cards, other RDNA GPUs, and Strix Halo, with a common branch for shared changes and separate branches for architecture-specific development.

The fork grew out of my [LLM Performance Engineering Notebook](https://github.com/pauldmartinphd/llm-performance-engineering-notebook). Measuring where inference time went led me to change the scheduler, and supporting newer models led me to integrate work from other llama.cpp forks. I now maintain those changes together, preserving the original authorship and documenting which configurations I have tested.

**[Source code](https://github.com/pauldmartinphd/llAMDa.cpp) · [Build instructions](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/docs/amd-main.md) · [Source credits](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/CREDITS-AMD.md)**

## Why a fork

A Vega card, an RDNA2 workstation card, and a Strix Halo system with unified memory impose different constraints on the kernels and memory transfers. A kernel change that helps one architecture may not help another, and a transfer optimization for unified memory may not apply to discrete GPUs. I therefore keep the device and workload restrictions with each change rather than enabling every optimization on every AMD GPU.

I wanted those changes available together, working through the ordinary build and runtime options, with their authorship and evidence recorded. I also wanted a place to carry the scheduler changes that came out of my own measurements on Galactus.

## Branches

I use `amd-main` as the default branch and build the three architecture development branches on that common base.

| Branch | Purpose |
|---|---|
| [`amd-main`](https://github.com/pauldmartinphd/llAMDa.cpp/tree/amd-main) | The default branch. It follows upstream llama.cpp and carries the common AMD changes, Unsloth's DeepSeek-V4.1/DSpark implementation, and my MoE prefill patch. |
| [`rdna-dev`](https://github.com/pauldmartinphd/llAMDa.cpp/tree/rdna-dev) | RDNA development, including the V620 work that started the fork. |
| [`halo-dev`](https://github.com/pauldmartinphd/llAMDa.cpp/tree/halo-dev) | Strix Halo development. |
| [`vega-dev`](https://github.com/pauldmartinphd/llAMDa.cpp/tree/vega-dev) | Vega development, including the gfx900 and gfx906 targets. |

All four branches include the prefill changes described below.

## Patches and source credits

I build on upstream llama.cpp and retain the authorship and original commit references for the patches I import. The [credits file](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/CREDITS-AMD.md) identifies the source revisions and adaptations.

| Change | Source |
|---|---|
| Normalization kernels compiled with loops only for grids that exceed launch limits | mxxm-t, [mx-llama.cpp](https://github.com/mxxm-t/mx-llama.cpp) |
| Direct handling of small readbacks on unified-memory Vulkan systems | Laurent Zuijdwijk, [halo-box/strix-llama.cpp](https://github.com/halo-box/strix-llama.cpp) |
| Wider RDNA3 decode for the MXFP4 and Q8_0 quantization formats | Edwin Brown, [llama.cpp-rdna2](https://github.com/edwinbrowwn/llama.cpp-rdna2) |
| DeepSeek-V4.1-Flash-1017 and DSpark speculative decoding support | Daniel Han and Unsloth, [llama.cpp PR 30817](https://github.com/ggml-org/llama.cpp/pull/30817) |
| Multi-GPU MoE prefill distribution and routing-index readback bypass | My [scheduler patch](https://github.com/pauldmartinphd/llm-performance-engineering-notebook/blob/main/patches/prefill/README.md) |

Upstream llama.cpp already supplies gfx900 dispatch, RDNA3.5 matrix-multiply tuning, and a BF16 fallback for hardware without native BF16 support. I inherit those changes from upstream.

I initially imported several forks together, then reverted that integration and selected individual patches. A benchmark of an entire fork does not tell me whether a particular kernel or dispatch table caused the improvement. I also excluded changes that depended on incompatible implementations or violated the documented alignment requirements. I explain those decisions in the [patch audit](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/docs/amd-main.md), alongside the source authors' tests and measurements.

## The prefill patch

While testing GLM-5.2 on Galactus with routed experts in system RAM, I found that llama.cpp concentrated offloaded expert computation on one GPU during prefill. A synchronous routing-index readback also serialized the work. I changed the scheduler to distribute eligible expert work across the GPUs and bypass that readback for sufficiently large batches.

In the July GLM-5.2 experiment on Galactus, which then had 1 TB of RAM, the combined patch raised prompt-processing throughput on an 8,192-token prompt from 104.97 to 119.36 tokens/s, a gain of 13.7%. Distribution alone moved it only from 104.97 to 105.71 tokens/s, so distributing the work alone did not produce an appreciable gain. Token generation stayed at about 5.5 tokens/s; I changed the prefill path, not the decode policy.

The port in the fork applies the policy to eligible operations on batches of at least 32 tokens. The bypass additionally requires at least eight routing selections per model expert, and it copies the full expert tensor instead of reading back the selected IDs. Under skewed routing, that can transfer weights that go unused. Setting `GGML_MOE_PREFILL=0` disables both changes, which makes controlled comparisons straightforward. This distributes streamed prefill computation. Expert caching and tensor parallelism remain separate mechanisms.

## Testing the integration

I built the integration on a Mac using the CPU backend. During the AMD patch review, I ran 12 selected regression tests and 145 normalization backend cases, all of which passed. After adding the prefill patch to the common branch, I built all four branches and tested the actual selection and expert-copy code with a harness covering four-device selection, eligible subsets, fallback behavior, batch-size thresholds, numerical equivalence under skewed routing, and the disable switch. Those checks also passed.

These tests check CPU behavior and scheduler logic, but they do not execute the AMD kernels or HIP transfers. I have not compiled the adapted normalization change for HIP locally or benchmarked the current integration on Galactus. The **13.7% prefill gain comes from my July GLM-5.2 scheduler experiment**, before this integration, and the source authors' kernel measurements describe their own hardware and revisions.

To measure the current port, I need to compare the default behavior with `GGML_MOE_PREFILL=0` on Galactus while holding the model, placement, prompt, and batch sizes constant. The architecture-specific branches also need tests on their corresponding GPUs.

## Related measurements in the notebook

I also tested newer Flash models with the earlier V620 build. These runs predate the current integration:

- **[GLM-5.3-Flash](https://github.com/pauldmartinphd/llm-performance-engineering-notebook/blob/main/results/lab-notebook/15-glm53-flash-placement.md):** automatic placement generated 19.1 to 19.4 tokens/s, while forcing the routed experts into system RAM gave about 13.7 to 14.4 tokens/s. I did not record which tensors the automatic placement chose, so I cannot say which mechanism produced the difference.
- **[DeepSeek-V4.1-Flash](https://github.com/pauldmartinphd/llm-performance-engineering-notebook/blob/main/results/lab-notebook/16-deepseek41-dspark.md):** a target-only run generated 11.2 tokens/s. The DSpark draft model then failed to load when a GPU could not allocate another 2,464 MiB, so I have no speculative-decoding result for that model.

I discuss the remaining bandwidth question in a [separate note](https://github.com/pauldmartinphd/llm-performance-engineering-notebook/blob/main/results/lab-notebook/17-bandwidth-and-placement.md). Dividing STREAM bandwidth by active expert bytes gives an optimistic rate for that weight traffic, but inference also selects experts, decodes quantized weights, performs the arithmetic, and completes the rest of the model. I need operation and transfer timings to determine how much each contributes to token time.

## Building, license, and credits

I provide build commands in the [README](https://github.com/pauldmartinphd/llAMDa.cpp) and [AMD build notes](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/docs/amd-main.md) for HIP targets such as gfx900, gfx906, gfx1030, gfx1100, and gfx1151, and for Vulkan on Strix Halo. The installed ROCm toolchain must support the selected target; the fork cannot supply missing driver or toolchain support for older cards. I retain llama.cpp's MIT license, history, and license notices. It is an independent fork, not an official AMD or llama.cpp project.

- [llAMDa.cpp repository](https://github.com/pauldmartinphd/llAMDa.cpp)
- [Source credits](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/CREDITS-AMD.md)
- [Patch audit and build notes](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/docs/amd-main.md)
- [Multi-GPU MoE prefill notes](https://github.com/pauldmartinphd/llAMDa.cpp/blob/amd-main/docs/moe-prefill-offload.md)
- [Development notes in the lab notebook](https://github.com/pauldmartinphd/llm-performance-engineering-notebook/blob/main/results/lab-notebook/14-llamda-amd-fork.md)
