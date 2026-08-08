---
title: "A Scientific Method for Measuring the Limits of Local LLM Inference Speed"
date: 2026-08-08
permalink: /posts/2026/08/measuring-local-llm-inference-limits/
excerpt: "A reproducible method for measuring the speed limits of local LLM inference, applied to a 64-core EPYC server running 750-billion-parameter Mixture-of-Experts models on llama.cpp. It covers memory bandwidth measurement, a predictive model for decode speed, single-variable experiments, and a llama.cpp scheduler patch that raised prefill from 105 to 119 tokens per second."
categories:
  - performance
tags:
  - llama.cpp
  - moe
  - llm-inference
  - epyc
  - benchmarking
toc: true
toc_sticky: true
---

Benchmark numbers for local language model inference are often not reproducible. A contributor changes one flag, records a tokens-per-second value, and publishes a conclusion. The value came from a single run with an unstated microbatch size and no model of the hardware, so a second person cannot reproduce it.

This post applies the scientific method to inference speed. The procedure has four parts. Measure the physical limit of the hardware. State a hypothesis for what the software should achieve against that limit. Test the hypothesis by changing one variable at a time. Record every result, including the results that refute the hypothesis. The output is a set of measurements that another person can reproduce, and a physical explanation for each one.

I applied the procedure to one machine over approximately three weeks. This post uses that machine as the worked example.

The test system, referred to here as Galactus, is a single server: an AMD EPYC 7713 (64 cores, 128 threads, Zen 3), 2 TB of DDR4-2933 memory across 8 channels, and four AMD Radeon Pro V620 GPUs. It runs llama.cpp with the ROCm backend inside an LXC container on Proxmox. The workload is hybrid Mixture-of-Experts (MoE) inference: the routed experts remain in system RAM, and the dense path (attention, shared experts, and the KV cache) runs on the GPUs. The primary model is GLM-5.2, with 753 billion parameters and 435 GiB at 4-bit quantization. A second model, DeepSeek-V4-Flash, appears in the section on speculative decoding.

Over the investigation, GLM-5.2 prefill increased from 37.6 to 119.4 tokens per second, and decode increased from 5.2 to 7.1 tokens per second. Each limit has a measured explanation.

## Measure the memory bandwidth first

In hybrid MoE inference, the model reads the active expert weights from DRAM on every decode step. Decode speed is therefore limited by memory bandwidth. The first measurement is the memory bandwidth of the machine, taken before any model runs.

I measure bandwidth with STREAM, run as a sweep over thread counts. STREAM reports four kernels: Copy, Scale, Add, and Triad. The raw STREAM values undercount write traffic. When a program writes a cache line that it has not already loaded, the hardware first reads that line into cache before the write completes. This extra read is called read-for-ownership (RFO). STREAM does not count it.

To recover the true traffic, apply the RFO correction. Multiply Scale by 1.5, and multiply Add and Triad by 4/3. The Copy kernel needs no correction when the compiler emits non-temporal store instructions, because those stores bypass the cache and skip the read-for-ownership step.

The Copy case has a consistency check. If uncorrected Copy plus the RFO correction exceeds the theoretical peak, then Copy used non-temporal stores and needs no correction. On Galactus the corrected value would have exceeded the peak, which confirms the non-temporal path.

After the correction, all four kernels converge on about 152 GB/s. The theoretical peak for eight channels of DDR4-2933 is `8 × 2933 × 8` bytes, or 187.7 GB/s. The measured 152 GB/s is 81% of that peak. Bandwidth reaches its maximum at 16 threads and falls slightly at higher thread counts. It saturates at 16 threads because each Zen 3 core complex die reaches the I/O die over a single GMI2 link, so a few active dies already saturate the path to DRAM.

Two facts follow from this single number. Decode does not benefit from all 64 cores, because bandwidth saturates at 16 threads. And 81% of the theoretical peak indicates a correctly configured memory subsystem. A result near 50% would indicate a memory topology fault rather than a software fault.

## Predict decode speed from measured memory bandwidth

Use the measured bandwidth to predict decode speed. Model one decode step as two terms:

```
time_per_token = C + (bytes_read_per_token / bandwidth)
```

`C` is a fixed cost on the GPU side. Measure it once from a known configuration. `bytes_read_per_token` is the size of the active expert set at the quantization of the model. For GLM-5.2, `C` is about 90 ms, and the model reads about 13.8 GB per token:

```
90 ms + (13.8 GB / 152 GB/s) = 90 ms + 91 ms = 181 ms
181 ms per token is about 5.5 tokens per second
```

The measured value was 5.53 tokens per second. I applied the same model to two other configurations: a setup that fills VRAM with resident experts, and a CPU-only configuration with no GPU offload. The model predicted 6.2 and 3.9 tokens per second. The measurements were 6.01 and 3.87. Agreement across three configurations to within a few percent confirms that decode is bandwidth-bound on this machine. The model also gives an upper bound on what any decode change can achieve, before that change is tested. No CPU-side change can move a limit set by DRAM bandwidth.

## Change one variable at a time

With the limit known, the experimental loop has three steps:

1. Record a baseline with the current configuration, measured correctly.
2. Change one variable and sweep it across a range. Read the full curve, not only the peak value.
3. Move to the next variable. Keep a setting only after the reason for its effect is understood.

The thread sweep shows why a sweep is necessary. Decode does not improve with more threads:

| Threads | GLM-5.2 decode (t/s) |
|--------:|---------------------:|
| 24 to 32 | 5.5 (peak) |
| 96 | 2.76 |
| 128 | 1.29 |

Decode peaks near half the physical core count and falls sharply once threads land on SMT sibling cores. Prefill follows a different curve. It continues to rise with thread count. The two phases have different optimal thread counts for the same flag, so a decode-optimal setting must not be reused for a prefill test.

### A default that invalidated a set of results

The microbatch sweep produced a large effect:

| n_ubatch | GLM-5.2 pp8192 (t/s) |
|---------:|---------------------:|
| 512 | 25.90 |
| 1024 | 41.68 |
| 2048 | 62.64 |
| 4096 | 84.62 |
| 8192 | 104.97 |

The prefill rate changes by a factor of four across this single parameter. The measurement contains a failure mode. In `llama-bench`, the `-p` flag sets the prompt length and also clamps `n_ubatch` to that length. A `-p 512` value inherited from an older script had silently clamped every prefill test that morning to a microbatch of 512, not the 8192 I intended. Those results were invalid. The rule that follows: set `-ub` explicitly on every run, and confirm the effective microbatch before recording a prefill number.

## Control for dependencies between variables

Changes are not independent. Before recording a result, locate the variable in the dependency structure. There are four common cases.

- One setting can invalidate every result that depends on it. The `-p` clamp on `n_ubatch` is an example. It silently changed the microbatch for every downstream test.
- A change can help in one regime only. `op_offload` reduces prefill speed at small microbatch, because the per-microbatch streaming cost dominates, and it increases prefill speed at a microbatch of 8192. Test a change in the regime where it can help.
- A change can affect one phase only. The scheduler patch below raises prefill and leaves decode unchanged. A decode regression after that patch would indicate a defect.
- Two paths can share an idea and differ in performance. Pinning experts resident on a GPU with `-ot` placement uses a different code path from `op_offload`. On GLM-5.2 the resident-placement path measured 17% slower at a microbatch of 2048.

## A scheduler patch found by inspecting the mechanism

Prefill reached a plateau of 104.97 tokens per second at a microbatch of 8192. To decide whether this was the hardware limit or an artifact, I inspected the behavior of the scheduler rather than testing more flags. The relevant signal is the distribution of graph splits across the GPUs:

```bash
GGML_SCHED_DEBUG=2 llama-bench -m GLM-5.2-... -ngl 99 -ot "exps=CPU" -fa 1 -v \
  -t 32 -b 512 -ub 512 -p 512 -n 0 -r 1 2>&1 | grep '## SPLIT' | sort | uniq -c
```

The output showed 731 of 1,186 GPU offload splits assigned to a single card (ROCm0). Three of the four GPUs were idle during prefill.

The hypothesis: distribute the offloaded expert matmuls across all four cards.

I recorded a prediction before testing it. Distribution alone would not increase throughput. The expert-weight copies already ran asynchronously. The serialization came from a per-split synchronize that reads the expert-selection IDs. If that synchronize is the limit, then spreading the work across four cards gives four streams that each still wait on the synchronize. I built the distribution-only version first, to test the prediction against a possible refutation. It measured 105.71 against the 104.97 baseline. The split histogram equalized across the four cards while throughput held constant. An equalized histogram with unchanged throughput confirms that the mechanism moved and the limit did not.

The fix targets the synchronize. At a prefill-sized batch, almost every expert is selected by some token in the batch, so reading the IDs to identify the active experts recovers no bandwidth. The read only imposes the serializing synchronize. At a large batch, skip the read, mark all experts active, and let the copies issue immediately so they overlap compute on the other cards. The change is three edits to `ggml/src/ggml-backend.cpp`.

| Build | pp8192 | pp16384 | pp32768 |
|---|---:|---:|---:|
| Stock (ub 8192) | 104.97 | n/a | n/a |
| Distribution only | 105.71 | n/a | n/a |
| Distribution and ID-read bypass | 119.36 | 86.11 | 55.76 |

The patched build reached 119.36 tokens per second, a 13.7% increase, with the splits distributed across the four cards (285, 300, 294, 292). The full patch and the reproduction steps are in the [repository](https://github.com/pauldmartinphd/llm-performance-engineering-notebook). The null result located the fix. It confirmed that the synchronize, not the card assignment, was the limit.

## Reduce decode bytes with speculative decoding

The two-term model already showed that decode is bandwidth-bound. No CPU-side change moved it: not thread count, CPU affinity, threadpool polling, transparent hugepages, or repacked kernels. None of these change the bytes read or the bandwidth. The one method that reduces bytes read per accepted token is speculative decoding.

GLM-5.2 supports multi-token prediction (`--spec-type draft-mtp`). A sweep over draft depth gave 6.8 tokens per second at depth 1, 7.1 at depth 2, and 6.9 at depth 3. Depth 2 is the production setting: 7.1 tokens per second, a 31% increase over the 5.4 baseline.

DeepSeek-V4-Flash-0731 with the DSpark drafter (`--spec-type draft-dspark`, a block-5 drafter resident in VRAM) reached 14.7 tokens per second at depth 3, a 45% increase over its 10.1 baseline. This is the highest decode rate the machine produced on any model.

Both depth curves peak at depth 2 or 3 and fall at depth 5. On a top-k-of-many MoE model, each drafted token activates a nearly disjoint set of experts. Deep speculation reads more expert bytes than its acceptance rate recovers, so positions 4 and 5 cost more than they return.

## Limitations

The numbers in this post come from one machine. Most speculative-decoding tests used a single prompt (a technical-prose question) with greedy decoding. The acceptance rates for the DSpark runs were not captured, because the log-capture instrumentation failed during that session. That failure is recorded in the lab notebook. The 152 GB/s bandwidth figure and the GLM-5.2 results come from an earlier 1 TB memory configuration. The machine now has 2 TB, and the bandwidth re-measurement on the new modules is still open. A reader who repeats this method on other hardware should expect different constants and should re-derive them.

## Record every result, including the failures

Record each run with its full configuration: the date, the exact flags, the build identifier, the metric, the value, and the source. A tokens-per-second value without its flags and build is not a result.

Record the failed hypotheses as well. Over this investigation the following were measured and refuted for this workload: NUMA imbalance, container overhead, threadpool polling, strict CPU affinity, CPU_REPACK, transparent hugepages, the `-sm row` split mode, pipeline parallelism, ZenDNN, and HIP managed memory. This list is the most reusable output of the work, because each refuted item is a path that another person does not need to test.

## Summary of the method

1. Measure the physical limit with STREAM and the RFO correction.
2. Predict the workload from that limit with the two-term model.
3. Sweep one variable at a time and read the full curve.
4. Place each change in the dependency structure so no result is confounded.
5. Inspect the mechanism when a number reaches a plateau, and test each prediction against a possible refutation.
6. Record every result and every refuted hypothesis.

The specific numbers here are properties of this machine. The method applies to any comparable system. The procedure above, and every benchmark row behind these numbers, is in the [llm-performance-engineering-notebook repository](https://github.com/pauldmartinphd/llm-performance-engineering-notebook).
