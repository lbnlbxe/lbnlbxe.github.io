---
layout: default
title: Home
---

<div class="bg-dark lbnl-gradient text-white py-5">
    <div class="container py-5">
        <div class="row justify-content-center">
            <div class="col-lg-8 text-center">
                <h1 class="display-5 fw-normal"><strong>B</strong>erkeley e<strong>X</strong>tensible <strong>E</strong>nvironment (BXE)</h1>
                <p class="lead mb-4">LBNL-hosted, FPGA-based Computer Architecture Simulation Environment</p>
                <div class="d-grid gap-2 d-md-flex justify-content-md-center">
                    <a href="{{ '/about' | relative_url }}" class="btn btn-secondary btn-lg">Learn More</a>
                    <a href="{{ '/docs' | relative_url }}" class="btn btn-outline-secondary btn-lg">Documentation</a>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="container my-5">
    <section id="about">
        <div class="lbnl-accent">
            <h2>About This Project</h2>
            <p><em>Build on the FireSim environment to perform extensive architecture simulation utilizing LBNL's FPGA cluster.</em></p>
        </div>
        
        <p>The work presented here is based on the hard work done by the <a href="https://fires.im/" target="_blank"><i class="fas fa-fire-flame-curved" style="color: #e25822;"></i> FireSim</a> and <a href="https://chipyard.readthedocs.io/en/main/" target="_blank">🏗️ Chipyard</a> development teams at <a href="https://bar.eecs.berkeley.edu/" target="_blank">🐻 UC Berkeley Architecturre Research</a>. Many thanks for helping us bring this platform to life.
        <br/><br/>
        The UCB-BAR team provides docuemntation and tutorials on using FireSim and Chipyard. Most of the documentation provided here is based on those documents. Below is list of useful links for getting started with understanding how FireSim and Chipyard operate:</p>
        
        <div class="row mt-4">
            <div class="col-md-6 mb-3">
                <div class="card lbnl-card h-100">
                    <div class="card-body">
                        <h4 class="card-title">
                        <a href="https://docs.fires.im/" target="_blank" style="text-decoration: none; color: var(--bs-light); --letter-spacing: -1px; font: -apple-system,BlinkMacSystemFont,&quot;Segoe UI&quot;,Roboto,Helvetica,Arial,sans-serif,&quot;Apple Color Emoji&quot;,&quot;Segoe UI Emoji&quot;,&quot;Segoe UI Symbol&quot;; font-feature-settings: &quot;kern&quot; 1; font-kerning: normal;"><i class="fas fa-fire-flame-curved" style="color: #e25822;"></i> FireSim</a></h4>
                        <p class="card-text"><ul>
                            <li><a href="https://docs.fires.im/en/main/FireSim-Basics.html" target="_blank">FireSim Basics</a></li>
                            <li><a href="https://docs.fires.im/en/main/Local-FPGA-Initial-Setup.html" target="_blank">Local FPGA System Setup</a></li>
                            <li>Defining Custom Workloads
                            <ul>
                                <li><a href="https://firemarshal.readthedocs.io/en/latest/" target="_blank">FireMarshal</a></li>
                                <li>FireMarshal - <a href="https://firemarshal.readthedocs.io/en/latest/Tutorials/quickstart.html" target="_blank">Quick Start Tutorial</a></li>
                                <li>FireMarshal - <a href="https://firemarshal.readthedocs.io/en/latest/Tutorials/custom.html" target="_blank">Custom Workloads</a></li>
                            </ul></li>
                        </ul></p>
                    </div>
                </div>
            </div>
            <div class="col-md-6 mb-3">
                <div class="card lbnl-card h-100">
                    <div class="card-body">
                        <h4 class="card-title"><a href="https://chipyard.readthedocs.io/en/main/" target="_blank"><img src="https://chipyard.readthedocs.io/en/main/_images/chipyard-logo.svg" height="40" class="me-2" alt="Chipyard"></a></h4>
                        <p class="card-text"><ul>
                            <li><a href="https://chipyard.readthedocs.io/en/main/Chipyard-Basics/Chipyard-Components.html">Chipyard Components</a></li>
                            <li><a href="https://chipyard.readthedocs.io/en/main/Chipyard-Basics/Development-Ecosystem.html">Development Ecosystem</a></li>
                            <li><a href="https://chipyard.readthedocs.io/en/main/Chipyard-Basics/Configs-Parameters-Mixins.html">Configs, Parameters, Mixins</a>
                            <ul>
                                <li><a href="https://chipyard.readthedocs.io/en/main/Customization/index.html">Customization</a></li>
                            </ul></li>
                        </ul></p>
                    </div>
                </div>
            </div>
        </div>
    </section>
    
    <div class="alert alert-info mt-4" role="alert">
        <strong>Getting Started:</strong> Follow our <a href="{{ '/docs' | relative_url }}">documentation</a> to learn how to connect and run your own simulations on BXE!
    </div>
    
</div>
