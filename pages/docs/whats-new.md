---
layout: wiki
title: What's New
description: New Features of BXE
author: Berkeley eXtensible Environment Team
category: Home
navbar: true
parent: Documentation
permalink: /docs/whats-new
order: 2
---

## v2.3.0 - 2025 November 15

- **Multi-tenancy**: multiple users can have their own checkout of Chipyard/FireSim within the same VM
- **Install Script**: Pulls the latest version of Chipyard/FireSim and sets up them up for BXE
  - Allows for multiple Chipyard/FireSim environments
  - Allows for FireSim only environments, where you can use the FireSim simulator for custom Verilog without Chipyard (i.e. [MoSAIC](https://github.com/lbnlcomputerarch/mosaicfiresim){:target="_blank"})
- **FireAxe Capable**: Allows us to deploy large FireSim simulations (too large to fit on a single FPGA) partitioned across multiple FPGAs
  - [FireAxe Documentation](https://docs.fires.im/en/latest/Advanced-Usage/FireAxe-Partitioning-onto-Multiple-FPGAs/index.html){:target="_blank"}
  - Still untested, needs backend work to wire up FPGAs
