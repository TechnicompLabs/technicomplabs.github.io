---
layout: default
title: Technicomp Labs
permalink: /
---
<div class="wrap">

  <section class="hero">
    <p class="kicker">A personal lab</p>
    <h1>Building, measuring, and restoring <span class="accent">computing systems.</span></h1>
    <p class="lede">I’m <a href="https://pauldmartin.phd">Paul D. Martin, Ph.D.</a>, and Technicomp Labs is my independent computing laboratory. This site is where I publish the open-source projects I release and write about restoring and upgrading vintage computers, particularly high-end systems, to their maximum performance. It is a personal, noncommercial project that follows my interests in computer history and retro gaming, in systems and virtualization, in open source infrastructure, and in technology education, and it is not client work.</p>
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
      <span class="num">02</span><h2>Measurement and tuning</h2>
    </div>
    <div class="split">
      <div>
        <p class="kicker">LLM Performance Engineering Notebook</p>
        <h3>Finding the real speed limit of local inference.</h3>
        <p>The notebook records my experiments on local inference, beginning with large Mixture-of-Experts models. I use hardware measurements and performance estimates to identify limits, then test configuration and code changes to understand where time goes. It includes a llama.cpp scheduler patch that raised prefill throughput 13.7% in the July comparison, per-model results, raw logs, and the hypotheses that testing did not support.</p>
        <p><a class="more" href="https://github.com/pauldmartinphd/llm-performance-engineering-notebook">Read the notebook &rarr;</a></p>
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
