---
layout: default
title: Technicomp Labs
permalink: /
---
<div class="wrap">

  <section class="hero">
    <p class="kicker">A personal lab</p>
    <h1>Building, measuring, and restoring <span class="accent">computing systems.</span></h1>
    <p class="lede">I’m <a href="https://pauldmartin.phd">Paul D. Martin, Ph.D.</a>, and Technicomp Labs is my independent computing laboratory. This site is where I publish the open-source projects I release and my applied research, and write about restoring and upgrading vintage computers, particularly high-end systems, to their maximum performance. It is a personal, noncommercial project that follows my interests in computer history and vintage computing, in systems and virtualization, in open source infrastructure, and in technology education, and it is not client work.</p>
    <p class="lede-links"><a class="more" href="/blog/">Blog &rarr;</a> <a class="more" href="/projects/">Projects &rarr;</a> <a class="more" href="/about/">About me &rarr;</a></p>
  </section>

  <section class="section">
    <div class="section-head">
      <span class="num">01</span><h2>Benchtop Linux</h2>
      <a class="more" href="/benchtop/">Details &rarr;</a>
    </div>
    {% include benchtop.html %}
  </section>

  <section class="section">
    <div class="section-head">
      <span class="num">02</span><h2>Applied research</h2>
    </div>
    <article aria-labelledby="maya-update-title">
      <p class="kicker">Lab update · October 24, 2026</p>
      <h3 id="maya-update-title">Large local models at around 30 tokens per second.</h3>
      <p>I’m now running GLM-5.3-Flash Q4_K_XL and DeepSeek-V4.1-Flash Q8_K_XL at around 30 tokens per second with Project Maya on Galactus: four AMD Radeon Pro V620 GPUs, an EPYC 7713, and 2 TiB of RAM. The October 23 tests measured 31.6 tokens/s for GLM with default speculation and 29.3 tokens/s for DeepSeek with DSpark drafting enabled.</p>
      <p>The following morning’s DeepSeek dashboard adds a view of ongoing use: 90 requests and 86,075 output tokens at a reported aggregate 28.0 tokens/s. The last request generated 422 tokens at 31.6 tokens/s. These mixed requests provide a useful follow-up to the controlled measurements, with the configurations and measurement limits recorded in the notebook.</p>
      <figure>
        <a href="/assets/images/2026-10-24-maya-deepseek-dashboard.png"><img src="/assets/images/2026-10-24-maya-deepseek-dashboard.png" alt="Project Maya v1.1.24 serving DeepSeek-V4.1-Flash on four Radeon Pro V620 GPUs, with 31.6 tokens per second for the last request" width="1599" height="1185" style="height: auto;" loading="lazy"></a>
        <figcaption>DeepSeek on Galactus, October 24. The large number is the last request’s generation rate; the session aggregate is shown at the bottom. The dashboard is idle at capture time. Select the image to see it full size.</figcaption>
      </figure>
      <p><a class="more" href="https://github.com/pauldmartinphd/llm-performance-engineering-notebook#october-23-two-large-moe-models-at-roughly-30-tokenss">Read the measurements and source logs &rarr;</a></p>
    </article>
    <div class="split">
      <div>
        <p class="kicker">LLM Performance Engineering Notebook</p>
        <h3>Finding the real speed limit of local inference.</h3>
        <p>The notebook records my experiments on local inference, beginning with large Mixture-of-Experts models. I use hardware measurements and performance estimates to identify limits, then test configuration and code changes to understand where time goes. It includes a llama.cpp scheduler patch that raised prefill throughput 13.7% in the July comparison, per-model results, raw logs, and the hypotheses that testing did not support.</p>
        <p>The scheduler work now also lives in <a href="/projects/llamda-cpp/">llAMDa.cpp</a>, an AMD-focused llama.cpp fork for RDNA, Strix Halo, and Vega.</p>
        <p><a class="more" href="https://github.com/pauldmartinphd/llm-performance-engineering-notebook">Read the notebook &rarr;</a> <a class="more" href="/projects/llamda-cpp/">llAMDa.cpp &rarr;</a></p>
      </div>
      <img src="/assets/images/galactus-build.jpg" alt="Galactus, the lab's inference server: four GPUs and an EPYC processor in a compact chassis">
    </div>
  </section>

  <section class="section">
    <div class="section-head">
      <span class="num">03</span><h2>Hardware and history</h2>
    </div>
    <div class="cards">
      <a class="card" href="/lab/">
        <img src="/assets/images/lab-bench-window.jpg" alt="Electronics workbench with parts organizers, an Apple IIe, and an open-frame test bench">
        <div class="card-body">
          <p class="kicker">The Lab</p>
          <h3>Bench, rack, and instruments</h3>
          <p>The bench, instruments, and servers I use to measure performance, diagnose hardware, and restore older machines.</p>
        </div>
      </a>
      <a class="card" href="/collection/">
        <img src="/assets/images/collection-compact-macs.jpg" alt="Shelves of compact Macintosh computers">
        <div class="card-body">
          <p class="kicker">The Collection</p>
          <h3>Every era at warp speed</h3>
          <p>The pinnacle of computing from every decade, restored and upgraded to the limits of its era.</p>
        </div>
      </a>
    </div>
  </section>

  {% if site.posts.size > 0 %}
  <section class="section">
    <div class="section-head">
      <span class="num">04</span><h2>Blog</h2>
      <a class="more" href="/blog/">All posts &rarr;</a>
    </div>
    {% include post-list.html limit=5 %}
  </section>
  {% endif %}

</div>
