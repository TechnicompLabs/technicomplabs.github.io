---
title: "A Scientific Method for Measuring the Limits of Local LLM Inference Speed"
date: 2026-08-08
permalink: /posts/2026/08/measuring-local-llm-inference-limits/
excerpt: "How I measure the real speed limits of local LLM inference, using the machine I built for it: a 64-core server with 2 TB of RAM and four cheap datacenter GPUs. From memory bandwidth to a llama.cpp patch that took prefill from 105 to 119 tokens per second."
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

Most benchmark numbers for local language models fall apart the moment someone tries to reproduce them. Somebody flips a flag, watches the tokens-per-second number move, and posts a conclusion. The number came from a single run, with an unstated batch size and no real idea of what the hardware could do in the first place. I have done exactly this, and the results were worthless.

Over the last few weeks I worked out a way of measuring inference speed that gives me numbers I actually trust, and it turns out to be nothing more than the scientific method pointed at a GPU server. Find the hard physical limit first. Predict what the software should be able to reach against that limit. Then change one thing at a time and see whether the prediction holds. Keep the experiments that fail, because those are the ones that tell you where the real limit is.

This post is that method, told through the actual data from my machine.

## The machine

The machine is called Galactus, and I am a little proud of it, mostly because of what it cost.

Here is the problem it exists to solve. The interesting open models now are Mixture-of-Experts (MoE) models, and they are enormous. GLM-5.2, the model I lean on for most of this post, has 753 billion parameters and takes up 435 GiB even after I squeeze it down to four bits per weight. That does not come close to fitting on a single GPU. So the whole design is built around one idea: hold a huge model in ordinary system memory, put only the parts that run on every token onto GPUs, and do it without spending datacenter money.

The core is an AMD EPYC 7713, a 64-core Milan chip with eight memory channels. The channels are the reason I picked it. In this kind of workload the model streams its expert weights out of main memory on every single token, so memory bandwidth, not core count, is what sets the decode speed. Eight channels is how you get the bandwidth.

I filled it with 1 TB of DDR4, and it is 2 TB now. That sounds excessive until you price it out. DDR4 has been getting cheaper as everything moves to DDR5, so a terabyte of registered ECC memory ran me about $5,600, and it is what lets the machine load a 435 GiB model and still have room to work.

The part I am happiest about is the GPUs. I found four AMD Radeon Pro V620s for $400 each. The V620 is a datacenter card with 32 GB of memory on it, so four of them give me roughly 120 GiB of VRAM for $1,600. The obvious alternative would have been four RTX 3090s, which would have cost several times as much, given me less total VRAM, and, once I measured it, run this workload at basically the same speed: an estimated 6.3 tokens per second for the 3090s against the 6.01 I actually get. At about $12.50 per gigabyte of VRAM, those V620s are the best decision in the build.

The whole thing runs llama.cpp on the ROCm backend inside an LXC container on Proxmox. It came to about $9,050 all in.

## Start by measuring memory bandwidth

Since decode speed is set by memory bandwidth, the first thing I measure is not the model. It is how fast the machine can read its own memory.

The tool for that is STREAM, a tiny benchmark that pounds on memory with a few simple loops and reports the rate. There is one trap in reading its output. When a program writes to memory, the processor usually has to read the old contents of that cache line first, even though it is about to throw them away. That hidden read is real traffic, and STREAM does not count it, so STREAM understates the true bandwidth on its write-heavy loops. The correction is mechanical: multiply the Scale result by 1.5, and the Add and Triad results by 4/3. The Copy loop is a special case, because on this machine the compiler turned it into streaming writes that skip the hidden read. You can tell it did, because applying the correction to Copy would push the number above the theoretical maximum, which is impossible.

After the correction, Galactus reads memory at about 152 GB/s. The theoretical maximum for eight channels of DDR4-2933 is 187.7 GB/s, so I am getting 81% of what the hardware can physically do. That is a healthy number. If I had measured half the maximum, the real story would have been a bad memory configuration, and I would have gone into the BIOS instead of tuning software. The sweep tells me one more useful thing before I have run the model even once: bandwidth stops climbing at 16 threads, because a handful of active core clusters is already enough to saturate the path to memory. So decode has no use for all 64 cores.

## Predict decode speed from measured memory bandwidth

Once I know the bandwidth, I can predict decode speed before running the model, and that is the step that makes this feel like measurement instead of guessing.

Decoding one token means reading the active expert weights out of memory and doing a bit of GPU work on top. So I model the time for one token as two parts: a fixed cost on the GPU, plus the time to stream the weights, which is simply the number of bytes divided by the bandwidth I measured.

For GLM-5.2 the fixed GPU cost is about 90 ms, and the model reads about 13.8 GB of expert weights per token. That works out to 90 ms plus 13.8 GB over 152 GB/s, or roughly 181 ms per token, which is 5.5 tokens per second. I measured 5.53. I ran the same prediction against two other configurations and got 6.2 and 3.9, where the machine gave me 6.01 and 3.87.

Landing three predictions within a few percent is the entire point of doing it this way. It tells me decode is limited by memory bandwidth and nothing else, and it tells me the ceiling. No amount of CPU tuning is going to move a number that is fixed by how fast DDR4 can be read. That knowledge saved me from a long list of experiments that could not have worked.

## Change one variable at a time

With the ceiling known, the testing itself is deliberately boring. I take a baseline, change exactly one thing, sweep it across a range, and look at the whole curve instead of just the best point. Then I move to the next thing, and I only keep a setting once I understand why it helped.

Thread count is a good example of why you sweep rather than assume. More threads do not help decode at all:

| Threads | GLM-5.2 decode (t/s) |
|--------:|---------------------:|
| 24 to 32 | 5.5 (peak) |
| 96 | 2.76 |
| 128 | 1.29 |

Decode is fastest at about half the cores and then falls off a cliff once the threads spill onto the hyperthreads. Prefill, the phase where the model reads your prompt, does the opposite and keeps getting faster with more threads. One flag, two opposite curves, which is the whole reason you cannot reuse a good decode setting for a prefill test.

### The morning I threw away a morning of results

Microbatch size gave me the biggest single swing I saw:

| n_ubatch | GLM-5.2 pp8192 (t/s) |
|---------:|---------------------:|
| 512 | 25.90 |
| 1024 | 41.68 |
| 2048 | 62.64 |
| 4096 | 84.62 |
| 8192 | 104.97 |

That is a factor of four from one number. It is also where the method quietly saved me from myself. llama-bench has a flag, `-p`, that sets the prompt length, and it also caps the microbatch at that same value without telling you. I had an old `-p 512` sitting in a script, so every prefill number I had collected that morning had really run at a microbatch of 512 rather than the 8192 I believed I was testing. A whole morning of numbers, wrong. Now I set the microbatch explicitly on every run and confirm it before I write anything down.

## Watch how the variables depend on each other

The reason one stray flag could ruin a whole morning is that these settings are not independent. Before I trust a result, I put the change into a rough dependency tree, because any given change tends to sit in one of a few relationships to the others.

- Some changes quietly break everything downstream of them. The `-p` flag capping the microbatch is the obvious one.
- Some only help in a particular regime. Offloading the expert math to the GPU actually loses ground at small microbatches and only wins at large ones, so you have to test it where it has a chance.
- Some touch only one phase. The patch I describe next speeds up prefill and leaves decode alone, so a decode slowdown afterward would mean a bug.
- Some look like the same idea but run down entirely different code paths. Pinning experts onto a GPU by hand and letting llama.cpp offload them automatically sound equivalent, and the hand-pinned version measured 17% slower for me.

## Chasing down a llama.cpp bottleneck

This is the result I am most pleased with, because I did not find it by trying flags. I found it by looking at what the scheduler was actually doing.

Prefill had flattened out around 105 tokens per second, and I could not tell whether that was the hardware or something I could fix. So rather than keep guessing, I asked llama.cpp to show me how it was splitting the work across the four GPUs:

```bash
GGML_SCHED_DEBUG=2 llama-bench -m GLM-5.2-... -ngl 99 -ot "exps=CPU" -fa 1 -v \
  -t 32 -b 512 -ub 512 -p 512 -n 0 -r 1 2>&1 | grep '## SPLIT' | sort | uniq -c
```

The answer was badly lopsided. Of 1,186 chunks of GPU work, 731 were going to a single card. Three of my four GPUs were basically idle during prefill while one did most of the job. The fix looks obvious: spread the work across all four cards.

Before I wrote any code, though, I wrote down a prediction, and my prediction was that spreading the work would do nothing. The copies of the expert weights already happened in the background. What actually held things up was a small synchronization step, where each chunk of work stopped to read which experts a batch had selected. If that pause was the real bottleneck, then handing the work to four cards would just give me four cards that each waited their turn. So I built the spread-the-work version first, specifically to try to prove myself wrong. It came out at 105.71 against the 104.97 baseline. The four GPUs were now perfectly balanced, and the speed had not moved at all. That is the clearest signal you can get that you fixed the wrong thing: the mechanism changed, and the number did not.

The real culprit was that synchronization. At the batch sizes prefill runs at, nearly every expert gets used by some token anyway, so stopping to look up which experts are active buys nothing and only forces the cards to wait on each other. So I skipped the lookup at large batch sizes and let the copies go. Three small edits later:

| Build | pp8192 | pp16384 | pp32768 |
|---|---:|---:|---:|
| Stock | 104.97 | n/a | n/a |
| Spread the work only | 105.71 | n/a | n/a |
| Spread the work and skip the lookup | 119.36 | 86.11 | 55.76 |

Prefill went to 119.36 tokens per second, and now all four cards do equal work. The [patch and the exact steps to reproduce it](https://github.com/pauldmartinphd/llm-performance-engineering-notebook) are in the repository. The number is nice, but the shape of the thing is the real lesson. I predicted a result, ran the experiment that could have proven me wrong, and the prediction that failed to move the number is what pointed me at the actual fix.

## Make decode faster by reading fewer bytes

My two-term model said decode was pinned against the memory wall, and it was right. I tried the whole CPU-side menu, thread counts, core pinning, polling, hugepages, and none of it moved decode, because none of it changes how many bytes get read per token. The only way to make decode genuinely faster is to read fewer bytes, and the way to do that is speculative decoding: let a small, fast model guess a few tokens ahead, then check its guesses in a single batch.

For GLM-5.2 this took decode from 5.4 to 7.1 tokens per second, a 31% gain, and it finally pushed the model past the point where it writes faster than I read. On DeepSeek-V4-Flash, the same trick with a stronger drafter reached 14.7 tokens per second, a 45% gain and the fastest this machine has ever decoded anything.

There is a limit to how hard you can lean on it. Both models speed up as I let the drafter guess two or three tokens ahead, and then they slow down again if I push to five. On a Mixture-of-Experts model, each guessed token tends to wake up a different set of experts, so guessing too far ahead reads more memory than the extra accepted tokens are worth.

## What this does not show

I would rather be honest about the edges than oversell the middle. These numbers come from one machine, and the speculation tests mostly used a single prompt with greedy decoding. I did not manage to capture the draft acceptance rates for the DeepSeek runs, because my logging broke that session, and I recorded that as a finding rather than pretend it did not happen. The bandwidth and GLM numbers are from the machine's earlier 1 TB configuration, and it has 2 TB now, so I still owe myself a fresh bandwidth measurement on the new memory. If you run this method on your own hardware, expect different constants and work them out yourself.

## Write down everything, especially the failures

Every run goes into a log with its full configuration: the date, the exact flags, the build, the measurement, and where it came from. A tokens-per-second number is meaningless without the flags and the build that produced it.

I also keep a list of the things that did not work. On Galactus that list includes NUMA tuning, container overhead, thread polling, core pinning, weight repacking, hugepages, a couple of GPU split modes, pipeline parallelism, and two different vendor math libraries. That list of dead ends is honestly the most useful thing the whole project produced, because every entry is an experiment you now get to skip.

## The method, in six steps

1. Measure the hardware's real limit, using STREAM with the RFO correction.
2. Predict what the software should get from that limit.
3. Change one variable at a time and read the whole curve.
4. Keep track of how the variables depend on each other.
5. When a number stops moving, stop guessing and look at what the code is actually doing.
6. Write down every result, including the ones that failed.

The numbers in this post belong to Galactus. The method belongs to anyone: measure first, predict, and let the experiments that fail tell you where the real limit is. If you want the raw data, the patch, and the full lab notebook, it is all in the [repository](https://github.com/pauldmartinphd/llm-performance-engineering-notebook).
