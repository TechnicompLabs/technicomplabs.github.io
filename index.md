---
layout: default
title: Technicomp Labs
permalink: /
---
<div class="wrap">

  <section class="hero">
    <p class="kicker">A working lab</p>
    <h1>Fast systems and <span class="accent">old machines.</span></h1>
    <p class="lede">Performance engineering, applied machine learning, and vintage computer restoration, measured on real hardware in the workshop of <a href="https://pauldmartin.phd">Paul D. Martin, Ph.D.</a></p>
  </section>

  <section class="section">
    <div class="section-head">
      <span class="num">01</span><h2>Benchtop Linux</h2>
      <a class="more" href="/projects/">All projects &rarr;</a>
    </div>
    {% include benchtop.html %}
  </section>

  <section class="section">
    <div class="section-head">
      <span class="num">02</span><h2>Research</h2>
    </div>
    <div class="split">
      <div>
        <p class="kicker">LLM Performance Engineering Notebook</p>
        <h3>Finding the real speed limit of local inference.</h3>
        <p>An open lab notebook on large Mixture-of-Experts models: measure the hard physical limit first, predict from a model, then change one variable at a time. It includes a llama.cpp scheduler patch that raised prefill throughput 13.7%, per-model results, raw logs, and the hypotheses that didn't survive measurement.</p>
        <p><a class="more" href="https://github.com/pauldmartinphd/llm-performance-engineering-notebook">Read the notebook &rarr;</a></p>
      </div>
      <img src="/assets/images/galactus-build.jpg" alt="Galactus, the lab's inference server: four GPUs and an EPYC processor in a compact chassis">
    </div>
  </section>

  <section class="section">
    <div class="section-head">
      <span class="num">03</span><h2>The Workshop</h2>
    </div>
    <div class="cards">
      <a class="card" href="/lab/">
        <img src="/assets/images/lab-bench-window.jpg" alt="Electronics workbench with parts organizers, an Apple IIe, and an open-frame test bench">
        <div class="card-body">
          <p class="kicker">The Lab</p>
          <h3>Bench, rack, and instruments</h3>
          <p>The repair bench, test equipment, and machines behind the performance work and hardware restoration.</p>
        </div>
      </a>
      <a class="card" href="/collection/">
        <img src="/assets/images/collection-compact-macs.jpg" alt="Shelves of compact Macintosh computers">
        <div class="card-body">
          <p class="kicker">The Collection</p>
          <h3>Four decades of machines</h3>
          <p>Vintage computers and game consoles, repaired and kept in working order rather than on display.</p>
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
