---
layout: page
title: What's New
parent: Documentation
permalink: /docs/whats-new
nav_order: 1
---

## What's New

{:toc}

### [v3.0.5](https://github.com/lbnlbxe/bxe/releases/tag/v3.0.4){:target="_blank"} - 2026 August 27

- Added option to specific checkout/tag of Chipyard and FireSim
- Defaults to latest release if no option passed

_Full Changelog_: [v3.0.4...v3.0.5](https://github.com/lbnlbxe/bxe/compare/v3.0.4...v3.0.5){:target="_blank"}

### [v3.0.4](https://github.com/lbnlbxe/bxe/releases/tag/v3.0.4){:target="_blank"} - 2026 August 18

- Fixed bug in runner driver deploy scripts

_Full Changelog_: [v3.0.3...v3.0.4](https://github.com/lbnlbxe/bxe/compare/v3.0.3...v3.0.4){:target="_blank"}

### [v3.0.3](https://github.com/lbnlbxe/bxe/releases/tag/v3.0.3){:target="_blank"} - 2026 April 23

- Add manager/runner mode and harden fstab setup in setupBXE.sh

_Full Changelog_: [v3.0.2...v3.0.3](https://github.com/lbnlbxe/bxe/compare/v3.0.2...v3.0.3){:target="_blank"}

### [v3.0.2](https://github.com/lbnlbxe/bxe/releases/tag/v3.0.2){:target="_blank"} - 2026 April 02

- Fixed FireMarshal to use `guestmount`
- Added `managers/add-bxe-user.sh` script to manage BXE Manager users and their groups

_Full Changelog_: [v3.0.1...v3.0.2](https://github.com/lbnlbxe/bxe/compare/v3.0.1...v3.0.2){:target="_blank"}

### [v3.0.1](https://github.com/lbnlbxe/bxe/releases/tag/v3.0.1){:target="_blank"} - 2026 February 19

- Updates to setupBXE.sh to account for AMD/Xilinx Tools prerequisites.

_Full Changelog_: [v3.0.0...v3.0.1](https://github.com/lbnlbxe/bxe/compare/v3.0.0...v3.0.1){:target="_blank"}

### [v3.0.0](https://github.com/lbnlbxe/bxe/releases/tag/v3.0.0){:target="_blank"} - 2026 January 28

- **Public Release**: Public release of [BXE source code](https://github.com/lbnlbxe/bxe){:target="_blank"}
- Minor Bugfixes

_Full Changelog_: [v2.3.0...v3.0.0](https://github.com/lbnlbxe/bxe/compare/v2.3.0...v3.0.0){:target="_blank"}

### v2.3.0 - 2025 November 15

- **Multi-tenancy**: multiple users can have their own checkout of Chipyard/FireSim within the same VM
- **Install Script**: Pulls the latest version of Chipyard/FireSim and sets up them up for BXE
  - Allows for multiple Chipyard/FireSim environments
  - Allows for FireSim only environments, where you can use the FireSim simulator for custom Verilog without Chipyard (i.e. [MoSAIC](https://github.com/lbnlcomputerarch/mosaicfiresim){:target="_blank"})
- **FireAxe Capable**: Allows us to deploy large FireSim simulations (too large to fit on a single FPGA) partitioned across multiple FPGAs
  - [FireAxe Documentation](https://docs.fires.im/en/latest/Advanced-Usage/FireAxe-Partitioning-onto-Multiple-FPGAs/index.html){:target="_blank"}
  - Still untested, needs backend work to wire up FPGAs
