---
layout: default
title: Technicomp Labs
permalink: /
---
<div class="wrap">

  <section class="hero">
    <p class="kicker">A working lab</p>
    <h1>Building, measuring, and restoring <span class="accent">computing systems.</span></h1>
    <p class="lede">Technicomp Labs is the independent computing laboratory of <a href="https://pauldmartin.phd">Paul D. Martin, Ph.D.</a> The lab builds and modifies current systems, measures their limits, and restores computers and game consoles from earlier eras. Current work includes Benchtop Linux, performance research on local AI systems, hardware and firmware projects, and a working collection spanning five decades of personal computing.</p>
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
      <span class="num">02</span><h2>Performance research</h2>
    </div>
    <div class="split">
      <div>
        <p class="kicker">LLM Performance Engineering Notebook</p>
        <h3>Finding the real speed limit of local inference.</h3>
        <p>An open lab notebook on the inference speed of large Mixture-of-Experts models. Each investigation starts by measuring the hardware limit, predicts performance from a model of that limit, and then changes one variable at a time. The notebook includes a llama.cpp scheduler patch that raised prefill throughput 13.7%, per-model results, raw logs, and the hypotheses that testing did not support.</p>
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
          <p>The bench, test equipment, and servers behind the performance research, restorations, and upgrades.</p>
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
