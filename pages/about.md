---
layout: default
title: About
navbar: true
order: 1
permalink: /about/
---

# About Our Research

## BXE FireSim VM Node

<img src="https://www.xilinx.com/content/dam/xilinx/imgs/kits/u200-hero-a.jpg" align="right" width="250px" alt="Alveo U250">

- vCPUs: 8-core x86-64 socket
- Memory: 128 GiB
- OS: [Xubuntu 20.04.6](https://xubuntu.org/release/20-04/)
- FPGA: AMD/Xilinx [Alveo U250](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html)
  - AMD/Xilinx [Vitis 2023.1](https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/vitis/2023-1.html)
- FireSim: [main](https://github.com/firesim/firesim/tree/c301eb5)


## BXE FireSim Cloud Architecture

```mermaid
flowchart LR
    User(("👤 User"))
    BXE>"bxe.lbl.gov\n🛂 Login Node"]
    f1("firesim-001\n👷🏗️ Manager/Build Node")
    f2("firesim-002\n👷🏗️ Manager/Build Node")
    f3("firesim-xxx\n👷🏗️ Manager/Build Node")
    w{"wilson.lbl.gov\n🏃 Run Farm"}
    v{"vizion.lbl.gov\n🏃 Run Farm"}
    r{"rhodey.lbl.gov\n🏃 Run Farm"}
    fpgaw[["<i>eight_fpgas_spec</i>\n🧮 U250"]]
    fpgav[["<i>eight_fpgas_spec</i>\n🧮 U250"]]
    fpgar[["<i>four_fpgas_spec</i>\n🧮 U250"]]

    subgraph wilson
      direction TB
      w --o fpgaw
    end

    subgraph vizion
      direction TB
      v --o fpgav
    end

    subgraph rhodey
      direction TB
      r --o fpgar
    end

    User -- "USERNAME@bxe.lbl.gov" --> BXE
    BXE -- "bxeuser@firesim-xxx" --> f1 & f2 & f3
    f1 & f2 & f3 --> wilson
    f1 & f2 & f3 --> vizion
    f1 & f2 & f3 --> rhodey
```

When you want to access BXE FireSim, you need to request an account for the BXE Login Node. Once you are given an account, you are assigned a specific VM (`firesim-XXX`). Each Virtual Machine is set up with [FireSim](https://docs.fires.im/en/main/) per the instructions found for the [Xilinx Alveo U250 XDMA-based Getting Started Guide](https://docs.fires.im/en/main/Getting-Started-Guides/On-Premises-FPGA-Getting-Started/Xilinx-Alveo-U250-FPGAs.html) for FireSim.
