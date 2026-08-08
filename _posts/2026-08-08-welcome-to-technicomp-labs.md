---
title: "Welcome to Technicomp Labs"
date: 2026-08-08
permalink: /posts/2026/08/welcome/
excerpt: "What this blog is for, and what's coming first."
categories:
  - meta
tags:
  - announcements
toc: false
---

This is the first post at Technicomp Labs.

The plan is straightforward: write up systems-performance and applied-ML work in
full — the measurements, the hardware, and the reasoning that ties them together.
No rules of thumb standing in for numbers, and the dead ends left in, because a
refuted hypothesis usually teaches as much as a confirmed one.

**Coming first:** a performance-testing methodology, demonstrated end to end on a
real machine — a 64-core EPYC server with 2 TB of DDR4 and four GPUs, running
large Mixture-of-Experts models on llama.cpp. It starts from raw memory-bandwidth
numbers, builds a model of what the hardware *should* do, and then walks the
iterative test loop that took one model's prefill from 37 to 119 tokens/second —
including a llama.cpp scheduler patch found along the way.

More soon.
