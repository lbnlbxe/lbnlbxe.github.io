---
layout: home
title: Home
nav_order: 1
permalink: /
---

<div style="display: flex; align-items: center;">
  <img src="{{ 'assets/logos/project-logo.png' | relative_url }}" alt="BXE Logo" style="height: 100px">&nbsp;&nbsp;<h2>Berkeley eXtensible Environment (BXE)</h2>
</div>
<div style="display: flex; align-items: center;">
  <p><em>LBNL-hosted, FPGA-based Computer Architecture Simulation Environment</em></p>
</div>

[Github]({{ site.github_repo }}){:target="_blank" .btn .btn-purple .mr-2}
[Learn More]({{ '/about' | relative_url }}){: .btn .btn-blue .mr-2}
[Documentation]({{ '/docs' | relative_url }}){: .btn .btn-green .mr-2}

{: .new-title }
> BXE v3.0.1 Released!
>
> Check out the [What's New]({{ '/docs/whats-new' | relative_url }}) page to learn about new features of BXE.

### About This Project

*Build on the FireSim environment to perform extensive architecture simulation utilizing LBNL's FPGA cluster.*

The work presented here is based on the hard work done by the <a href="https://fires.im/" target="_blank">🔥 FireSim</a> and <a href="https://chipyard.readthedocs.io/en/main/" target="_blank">🏗️ Chipyard</a> development teams at <a href="https://bar.eecs.berkeley.edu/" target="_blank">🐻 UC Berkeley Architecture Research</a>. Many thanks for helping us bring this platform to life.

The UCB-BAR team provides documentation and tutorials on using FireSim and Chipyard. Most of the documentation provided here is based on those documents. Below is list of useful links for getting started with understanding how FireSim and Chipyard operate:

<div class="card-grid">
  <a href="https://docs.fires.im/" class="site-card" target="_blank">🔥<div class="card-title">FireSim</div><p>Fast and Effortless FPGA-accelerated Hardware Simulation with On-Prem and Cloud Flexibility</p></a>
<a href="https://chipyard.readthedocs.io/en/main/" class="site-card" target="_blank">🏗️<div class="card-title">Chipyard</div><p>Framework for designing and evaluating full-system hardware using agile teams</p></a>
<a href="https://firemarshal.readthedocs.io/en/latest/Tutorials/quickstart.html" class="site-card" target="_blank">🧑‍🚒<div class="card-title">FireMarshal</div><p>Workload generation tool for RISC-V based systems for FireSim FPGA-accelerated simulation</p></a>
</div>

{: .highlight-title}
> Getting Started
>
> Follow our [documentation]({{ '/docs' | relative_url }}) to learn how to connect and run your own simulations on BXE!
