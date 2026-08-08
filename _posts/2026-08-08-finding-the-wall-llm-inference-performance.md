---
title: "Finding the Wall: How to Actually Test LLM Inference Performance"
date: 2026-08-08
permalink: /posts/2026/08/finding-the-wall/
excerpt: "A repeatable method for finding a machine's real inference limits — demonstrated on a 64-core EPYC server running 750B-parameter MoE models, from raw memory bandwidth to a llama.cpp scheduler patch that took prefill from 105 to 119 tokens/second."
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

Most "how fast is my local LLM" numbers are vibes. Someone changes a flag, the tokens/second wiggles, and a conclusion gets posted. Then someone else can't reproduce it, because the number never came from a model of the machine — it came from a single run with an unstated microbatch and a background compile eating three cores.

This is the method I actually use, demonstrated end to end on one machine. The rule underneath it: **establish the physical ceiling, predict what the software should reach, then change one variable at a time and explain every number — and keep the hypotheses that got refuted, because they save more time than the ones that held.**

The machine is **Galactus**: an AMD EPYC 7713 (64 cores / 128 threads, Zen 3), 2 TB of DDR4-2933 across 8 channels, and 4 × AMD Radeon Pro V620, running llama.cpp and ROCm in a container on Proxmox. The workload is hybrid Mixture-of-Experts inference — routed experts held in system RAM, the dense path (attention, shared experts, KV cache) on the GPUs. The headline model is **GLM-5.2** (753 B parameters, 435 GiB at Q4), with **DeepSeek-V4-Flash** along for the speculative-decode section.

By the end, GLM-5.2 prefill went from 37.6 to 119.4 tokens/second and decode from 5.2 to 7.1 — and, more importantly, both ceilings are explained rather than stumbled into.

## Step 1: Start at the wall, not the model

For CPU-resident MoE, decode speed is set by one thing: how fast the active experts can be read out of DRAM every token. So the first measurement isn't a model benchmark at all. It's memory bandwidth.

I run [STREAM](https://www.cs.virginia.edu/stream/) as a thread sweep, then apply the **read-for-ownership (RFO) correction**. STREAM undercounts write traffic, because an ordinary store first has to read the cache line it's about to overwrite — traffic STREAM never counts. The fix is to scale the write-heavy kernels: Scale ×1.5, Add and Triad ×4/3. Copy needs no correction *if* the compiler turned it into non-temporal stores.

There's a built-in sanity check: uncorrected-Copy plus the RFO correction must not exceed the theoretical ceiling. On Galactus it would have — proof that Copy used non-temporal stores and shouldn't be corrected. With that resolved, all four kernels converge:

**~152 GB/s**, which is 81% of the 187.7 GB/s theoretical peak for 8-channel DDR4-2933.

Two things already fall out of this one number. Bandwidth *saturates at 16 threads* and declines past it — so decode has no reason to want all 64 cores. And 81% of theoretical is a healthy platform; if it had come back at 50%, the story would've been "fix your memory topology," not "tune llama.cpp."

## Step 2: Predict decode before you measure it

Now I use the wall to predict the model, with a two-term model of a decode step:

```
time_per_token ≈ C + (bytes_read_per_token ÷ bandwidth)
```

`C` is a fixed GPU-side cost you measure once. `bytes_read_per_token` is the active-expert footprint at your quant. For GLM-5.2, `C ≈ 90 ms` and the experts read ≈ 13.8 GB/token, so:

```
90 ms + (13.8 GB ÷ 152 GB/s) ≈ 90 + 91 = 181 ms  →  ~5.5 t/s
```

Measured: **5.53 t/s**. I ran the same model against two more configurations (a VRAM-filled "fitter" setup and a CPU-only denominator); it predicted **6.2 and 3.9**, and measured **6.01 and 3.87**. When the model and the machine agree to within a few percent across three configs, you're not guessing anymore — you know decode is bandwidth-bound, and you know roughly what any change *can* buy before you run it. No amount of CPU tuning will move a wall made of DRAM.

## Step 3: The iterative loop — one variable at a time

With the ceiling known, the loop is simple to state and easy to botch:

1. **Baseline**, measured correctly.
2. **Change one variable**, and **sweep it** — read the shape, not just the peak.
3. **Next variable**, carrying forward the previous winner *only once you know why it won.*

The thread sweep shows why you sweep instead of guessing "more is better":

| Threads | GLM-5.2 decode (t/s) |
|--------:|---------------------:|
| 24–32   | ~5.5 (peak)          |
| 96      | 2.76                 |
| 128     | 1.29                 |

Decode peaks around half the physical cores and *collapses* once you schedule onto SMT siblings. Prefill, meanwhile, keeps climbing with threads — a different curve for the same knob, which is exactly why you don't reuse a decode-optimal setting for a prefill test.

### The war story: how one default poisoned a day of results

Then the microbatch sweep, which is where I got humbled:

| n_ubatch | GLM-5.2 pp8192 (t/s) |
|---------:|---------------------:|
| 512      | 25.90                |
| 1024     | 41.68                |
| 2048     | 62.64                |
| 4096     | 84.62                |
| 8192     | 104.97               |

That's a **4× swing** on a single knob. And here's the trap: `llama-bench`'s `-p` flag *silently clamps* `n_ubatch` to the prompt length. Every "op_offload" prefill number I'd taken that morning had a `-p 512` inherited from an old script — so they'd all secretly run at ub 512, not the ub 8192 I thought I was testing. A day of numbers, quietly invalidated by one default. Set `-ub` explicitly, every single time, and never trust a prefill number until you've confirmed the effective microbatch.

## Step 4: The dependency tree of changes

The reason that microbatch bug was so costly is that changes aren't independent — they sit in a tree, and one setting can silently confound everything downstream. Before I trust a result, I place the variable in that tree:

- **Some changes poison everything upstream of them.** `-p` clamping `n_ubatch` is the poster child.
- **Some changes only help in one regime.** `op_offload` is a net *loss* at small microbatch (the per-microbatch streaming cost dominates) and a big win at ub 8192. Test a change where it's actually able to win.
- **Some changes touch only one phase.** The scheduler patch below moves prefill and leaves decode untouched — so if decode had regressed, that'd be a bug, not a tradeoff.
- **Some paths look equivalent and aren't.** Pinning experts resident on a GPU via `-ot` placement is a *different code path* from `op_offload`, and on GLM-5.2 it measured 17% slower. "Same idea" is not "same performance."

Draw the tree, and a later measurement can't be quietly sabotaged by an earlier flag.

## Step 5: The capstone — finding a llama.cpp patch by mechanism

This is the whole loop in one finding, and it's my favorite because a *negative* result is what cracked it.

**Baseline:** prefill plateaued at 104.97 t/s (ub 8192). Fast, but I didn't know if that was the wall or an artifact. So instead of poking flags, I looked at the *mechanism* — the scheduler's split histogram:

```bash
GGML_SCHED_DEBUG=2 llama-bench -m GLM-5.2-... -ngl 99 -ot "exps=CPU" -fa 1 -v \
  -t 32 -b 512 -ub 512 -p 512 -n 0 -r 1 2>&1 | grep '## SPLIT' | sort | uniq -c
```

Result: **731 of 1,186 GPU offload splits landed on a single card** (ROCm0). Three of four GPUs were sitting idle during prefill. There's the artifact.

**Hypothesis:** distribute the offloaded expert matmuls across all four cards.

**Pre-registered null:** I predicted, in writing, that distribution *alone* would do nothing — because the expert-weight copies were already asynchronous, and the thing actually serializing them was a per-split `synchronize` used to read the routing IDs. If that sync is the real bottleneck, spreading the work to four cards just gives you four streams that each still wait their turn. I built distribution-only first specifically to *try to refute my own hypothesis*: it came back **105.71 vs 104.97** — no change, exactly as predicted. The histogram equalized perfectly while throughput didn't budge, which is the tell that you've moved a mechanism without moving the bottleneck.

**The real fix:** at prefill-sized batches, essentially every expert is used by *some* token, so reading the IDs to find "which experts are active" saves no bandwidth — it only imposes the serializing sync. Skip the read at large batch, mark all experts used, and the copies can issue immediately and overlap compute across cards. Three small edits to `ggml/src/ggml-backend.cpp`:

| Build | pp8192 | pp16384 | pp32768 |
|---|---:|---:|---:|
| Stock (ub 8192) | 104.97 | — | — |
| Distribution only | 105.71 | — | — |
| **Distribution + ID-read bypass** | **119.36** | 86.11 | 55.76 |

**+13.7%**, histogram equalized to 285/300/294/292 across the cards. The full patch and reproduction steps are [in the repo](https://github.com/pauldmartinphd/llm-performance-engineering-notebook). The point for this post isn't the 13.7% — it's *how* it was found: predict, measure the mechanism, and let a refuted hypothesis point at the real cause.

## Step 6: Decode gains live in speculation

Prefill was scheduling; decode is physics. The two-term model already told me decode was pinned to the DRAM wall, and no CPU knob — threads, affinity, polling, hugepages, repacked kernels — moved it, because none of them change bytes or bandwidth. The only lever that reads *fewer* bytes per accepted token is **speculative decoding**.

- **GLM-5.2** gained multi-token-prediction support upstream (`--spec-type draft-mtp`). Sweeping draft depth: n=1 → 6.8, **n=2 → 7.1**, n=3 → 6.9 t/s. Locked at n=2: **+31%**, and past the 7 t/s reading-speed bar for the first time.
- **DeepSeek-V4-Flash-0731** with DSpark (`--spec-type draft-dspark`, a block-5 drafter in VRAM) went further: baseline ~10.1 → **14.7 t/s at n=3**, about **+45%** — the fastest decode this machine has produced on any model.

The depth curves have a shape worth internalizing. Both peak at n=2–3 and *regress* by n=5: on a top-k-of-many MoE, each drafted token activates a nearly disjoint set of experts, so deep speculation pays more expert-read bytes than its acceptance rate earns back. Same verify-tax the model predicts; you can see it in the numbers.

## Step 7: Record everything — including the "no"s

Every run gets logged with its *full* configuration — date, exact flags, build, metric, value, source line. A tokens/second figure without its flags and build isn't a result; it's an anecdote.

And I log the dead ends deliberately. Over this investigation, all of these were measured and **refuted** for this workload: NUMA imbalance, container overhead, threadpool polling, strict CPU affinity, `CPU_REPACK`, transparent hugepages, `-sm row`, pipeline parallelism, ZenDNN, and HIP managed memory. That list is the single most useful thing the whole effort produced, because every "no" is a road the next person doesn't have to drive down.

## The takeaway

The specific numbers here are Galactus's. The method isn't:

1. **Measure the physical ceiling** (STREAM + RFO).
2. **Predict** the workload from it (the two-term model).
3. **Sweep one variable at a time**, reading shapes.
4. **Place each change in the dependency tree** so nothing is confounded.
5. **Debug by mechanism**, and trust refuted hypotheses.
6. **Record results and the "no"s.**

That's the difference between "I changed a flag and it got faster" and "I know what this machine can do, and why." The terse, copy-pasteable version of this procedure — and every benchmark row behind these numbers — lives in the [llm-performance-engineering-notebook repo](https://github.com/pauldmartinphd/llm-performance-engineering-notebook).
