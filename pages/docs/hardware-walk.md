---
layout: wiki
title: Hardware Walkthrough
description: Learn how to get started with this building custom hardware
author: Berkeley eXtensible Environment Team
last_modified_at: {{ page.last_modified_at }}
category: Guide
author: Berkeley eXtensible Environment Team
breadcrumbs:
  - title: Documentation
    url: /docs/
  - title: Building Custom Designs
    url: /docs/building/
  - title: Hardware Walkthrough
    url: /docs/building/hardware-walkthrough/
prev_page:
  title: Building Custom Designs
  url: /docs/building/
next_page:
  title: Software Walkthrough
  url: /docs/building/software-walkthrough/
navbar: false
parent: Building
order: 31
permalink: /docs/building/hardware-walkthrough/
---

# Custom Hardware Walkthrough

This guide will show you how to change FireSim to build a dual-core Rocket RISC-V core. You can use this guide to modify Chipyard to build your custom architectures.

## Building a Dual-Core Rocket

In this example, we will be modifying FireSim to generate a dual-core RISC-V Rocket Core (`alveo_u250_firesim_rocket_dualcore_nic`) and booting it up on the attached FPGA. Generating bitstreams for the Alveo U250 can take a **few hours** to complete, so it's recommended to do your work in a [persistent session](home#opening-a-persistent-session-on-bxe-firesim-nodes).

### Modifying FireSim Configuration Files

FireSim has two `.yaml` files that describes the designs available to build, and well as where to build new designs.

Both of these files can be found in `$FIRESIM_ROOT/deploy`. Examples of unmodified FireSim configuration files can be found in `$FIRESIM_ROOT/deploy/sample-backup-configs`.

#### `$FIRESIM_ROOT/deploy/config_build.yaml`

This files configures the build environment for FireSim. This tells the tools where to build designs, what base shell to use, and what designs to build.

First, we'll tell FireSim where to build our designs. Edit the `build-farm` section to resemble the following:

```yaml
build_farm:
  base_recipe: build-farm-recipes/externally_provisioned.yaml
  recipe_arg_overrides:
    # REQUIRED: (replace this) default location of build directory on build host.
    default_build_dir: /home/bxeuser/FIRESIM_BUILD_DIR
    # REQUIRED: List of IP addresses (or "localhost"). Each can have an OPTIONAL
    # argument, called "override_build_dir", specifying to override the default
    # build directory.
    #
    # Ex:
    # build_farm_hosts:
    #     # use localhost and don't override the default build dir
    #     - localhost
    #     # use other IP address (don't override default build dir)
    #     - "111.111.1.111"
    #     # use other IP address (override default build dir for this build host)
    #     - "222.222.2.222":
    #         override_build_dir: /scratch/specific-build-host-build-dir
    build_farm_hosts:
        - localhost
```

This tells FireSim to place all of the build files in `/home/bxeuser/firesim/FIRESIM_BUILD_DIR` on the run farm hosts. You can change this to any location you'd like.

Next we need to tell FireSim which designs we would like to build. `builds_to_run` lists all of the recipes that we'd like FireSim to build from scratch. Since we only want FireSim to build a `alveo_u250_firesim_rocket_dualcore_nic` recipe, we make sure we add it and comment out the other recipes. Here's what the `builds_to_run` section should look like:

```yaml
builds_to_run:
   # Configs for BXE
   - alveo_u250_firesim_rocket_dualcore_nic
```

In summation, your `$FIRESIM_ROOT/deploy/config_build.yaml` should look like this:

```yaml
# Build-time build design / AGFI configuration for the FireSim Simulation Manager
# See https://docs.fires.im/en/stable/Advanced-Usage/Manager/Manager-Configuration-Files.html for documentation of all of these params.

# this refers to build farms defined in config_build_farm.yaml
# this refers to build farms defined in config_build_farm.yaml
build_farm:
  base_recipe: build-farm-recipes/externally_provisioned.yaml
  recipe_arg_overrides:
    # REQUIRED: (replace this) default location of build directory on build host.
    default_build_dir: /home/bxeuser/FIRESIM_BUILD_DIR
      # REQUIRED: List of IP addresses (or "localhost"). Each can have an OPTIONAL  
      # argument, called "override_build_dir", specifying to override the default  
      # build directory.  
      #  
      # Ex:  
      # build_farm_hosts:  
      #         # use localhost and don't override the default build dir  
      #         - localhost  
      #         # use other IP address (don't override default build dir)  
      #         - "111.111.1.111"  
      #         # use other IP address (override default build dir for this build host)  
      #         - "222.222.2.222":  
      #                 override_build_dir: /scratch/specific-build-host-build-dir  
      build_farm_hosts:
          - localhost

builds_to_run:
    # this section references builds defined in config_build_recipes.yaml
    # if you add a build here, it will be built when you run buildbitstream

    # Configs for BXE
    - alveo_u250_firesim_rocket_dualcore_nic

    # Unnetworked designs use a three-domain configuration
    # Tiles: 1000 MHz
    #    <Rational Crossing>
    # Uncore: 500 MHz
    #    <Async Crossing>
    # DRAM : 1000 MHz
    # - firesim_rocket_quadcore_no_nic_l2_llc4mb_ddr3
    # - firesim_boom_singlecore_no_nic_l2_llc4mb_ddr3

    # All NIC-based designs use the legacy FireSim frequency selection, with the
    # tiles and uncore running at 3.2 GHz to sustain 200Gb theoretical NIC BW
    # - firesim_supernode_rocket_singlecore_nic_l2_lbp
    # - firesim_rocket_quadcore_nic_l2_llc4mb_ddr3
    # - firesim_boom_singlecore_nic_l2_llc4mb_ddr3

    # Configs for tutorials
    # - firesim_rocket_singlecore_no_nic_l2_lbp
    # - firesim_rocket_singlecore_sha3_nic_l2_llc4mb_ddr3
    # - firesim_rocket_singlecore_sha3_no_nic_l2_llc4mb_ddr3
    # - firesim_rocket_singlecore_sha3_no_nic_l2_llc4mb_ddr3_printf
    # - firesim_gemmini_rocket_singlecore_no_nic
    # - firesim_gemmini_printf_rocket_singlecore_no_nic

    # Configs for Vitis/XRT
    # - vitis_firesim_rocket_singlecore_no_nic

    # Config for RHSResearch Nitefury II
    # - nitefury_firesim_rocket_singlecore_no_nic

    # Configs for Xilinx Alveo U250/U280
    # - alveo_u250_firesim_rocket_singlecore_no_nic
    # - alveo_u250_firesim_gemmini_rocket_singlecore_no_nic
    # - alveo_u200_firesim_rocket_singlecore_no_nic
    # - alveo_u280_firesim_rocket_singlecore_no_nic

    # Config for Xilinx VCU118
    # - xilinx_vcu118_firesim_rocket_singlecore_4GB_no_nic

agfis_to_share:
    - firesim_rocket_quadcore_nic_l2_llc4mb_ddr3
    - firesim_rocket_quadcore_no_nic_l2_llc4mb_ddr3
    - firesim_boom_singlecore_no_nic_l2_llc4mb_ddr3
    - firesim_boom_singlecore_nic_l2_llc4mb_ddr3

    - firesim_supernode_rocket_singlecore_nic_l2_lbp

    # Configs for tutorials
    # - firesim_rocket_singlecore_no_nic_l2_lbp
    # - firesim_rocket_singlecore_sha3_nic_l2_llc4mb_ddr3
    # - firesim_rocket_singlecore_sha3_no_nic_l2_llc4mb_ddr3
    # - firesim_rocket_singlecore_sha3_no_nic_l2_llc4mb_ddr3_printf

share_with_accounts:
    # To share with a specific user:
    somebodysname: 123456789012
    # To share publicly:
    # public: public
```

#### `$FIRESIM_ROOT/deploy/config_build_recipes.yaml`

Next we need add our `alveo_u250_firesim_rocket_dualcore_nic` to the list of FireSim hardware recipes. The recipe is what FireSim uses to tell Chipyard what to design to build, as well as any FPGA specific parameters. The top of the file has schema for all of the parameters available.

Add the following to the end of `$FIRESIM_ROOT/deploy/config_build_recipes.yaml`:

```yaml
# BXE FireSim Dual-Core Rocket Recipe
alveo_u250_firesim_rocket_dualcore_nic:
    PLATFORM: xilinx_alveo_u250
    TARGET_PROJECT: firesim
    DESIGN: FireSim
    TARGET_CONFIG: WithNIC_FireSimDualRocketConfig
    PLATFORM_CONFIG: BaseXilinxAlveoU250Config
    deploy_quintuplet: null
    platform_config_args:
        fpga_frequency: 60
        build_strategy: TIMING
    post_build_hook: null
    metasim_customruntimeconfig: null
    bit_builder_recipe: bit-builder-recipes/xilinx_alveo_u250.yaml
```

We have now completed configuring FireSim. The `TARGET_CONFIG` section is the specific design in Chipyard we'd like to build. However, this design doesn't exist in Chipyard...

### Creating a Design with Chipyard Mixins

Chipyard allows us to use their library of standard architecture and hardware components to build designs. These are called _Mixins_, and the list of components is out of scope for this tutorial. For now, we'll just use these to design a dual-core Rocket core.

Let's add our design to the list of designs available to FireSim. Open `$CHIPYARD_ROOT/generators/firechip/chip/src/main/scala/TargetConfigs.scala` and add the following to the end of the file:

```scala
// BXE FireSim Dual-Core Rocket
class FireSimDualRocketConfig extends Config(
  new WithDefaultFireSimBridges ++
  new WithDefaultMemModel ++
  new WithFireSimConfigTweaks ++
  new freechips.rocketchip.subsystem.WithNBigCores(2) ++
  new chipyard.config.AbstractConfig)
```

And that's it! We're ready to build our design!

### Generating a Bitstream

With the FireSim build environment and Chipyard design configured, we're ready to launch the build. With FireSim loaded into our environment (`source sourceme-manager.sh --skip-ssh-setup`), we can launch a build.

```shell
cd $FIRESIM_ROOT
# source sourceme-manager.sh --skip-ssh-setup
firesim buildbitstream
```

As stated before, this may take **MANY HOURS** to complete. Grab yourself a cup of your favorite beverage and let the machine build. You should see the following successful build message when done:

```
[localhost] out: 
xilinx_alveo_u250/
xilinx_alveo_u250/metadata
xilinx_alveo_u250/firesim.bit
xilinx_alveo_u250/firesim.mcs
FireSim FPGA Build Completed
Your bitstream has been created!
Add

alveo_u250_firesim_rocket_dualcore_nic:
    bitstream_tar: file:///home/bxeuser/firesim/deploy/results-build/2024-07-17--17-33-00-alveo_u250_firesim_rocket_dualcore_nic/cl_xilinx_alveo_u250-firesim-FireSim-WithNIC-FireSimDualRocketConfig-BaseXilinxAlveoU250Config/firesim.tar.gz
    deploy_quintuplet_override: null
    custom_runtime_config: null

to your config_hwdb.yaml to use this hardware configuration.
Build complete! Xilinx Alveo xilinx_alveo_u250 bitstream ready. See /home/bxeuser/firesim/deploy/built-hwdb-entries/alveo_u250_firesim_rocket_dualcore_nic.
The full log of this run is:
/home/bxeuser/firesim/deploy/logs/2024-07-17--17-33-00-buildbitstream-3P5LQGXVOH0DNATS.log
```

# Running a Custom Design
Now that the build is complete, we need to tell FireSim that this design is available to be run. We'll add `alveo_u250_firesim_rocket_dualcore_nic` to FireSim's hardware database, then configure the simulation to use the new design.

## `$FIRESIM_ROOT/deploy/config_hwdb.yaml`
Using the path generated in the build step, we add an entry to the hardware database. Add the following to the end of the file:

```yaml
alveo_u250_firesim_rocket_dualcore_nic:
    bitstream_tar: file:///home/bxeuser/firesim/deploy/results-build/2024-07-17--17-33-00-alveo_u250_firesim_rocket_dualcore_nic/cl_xilinx_alveo_u250-firesim-FireSim-WithNIC-FireSimDualRocketConfig-BaseXilinxAlveoU250Config/firesim.tar.gz
    deploy_quintuplet_override: null
    custom_runtime_config: null
```

## `$FIRESIM_ROOT/deploy/config_runtime.yaml`
Now we're ready to deploy this design in the simulation. We modify this file to tell FireSim what the target design we're going to simulate. In the `target_config` section, change the `default_hw_config` to point to our new design, `alveo_u250_firesim_rocket_dualcore_nic`. It should resemble this:

```yaml
target_config:
    topology: no_net_config
    no_net_num_nodes: 1
    link_latency: 6405
    switching_latency: 10
    net_bandwidth: 200
    profile_interval: -1
    default_hw_config: alveo_u250_firesim_rocket_dualcore_nic
    plusarg_passthrough: ""
```

## Running the Simulation
We can now run the design and boot Linux on this design. Following the tutorial from before:

```shell
cd $FIRESIM_ROOT
firesim infrasetup -a ${CY_DIR}/sims/firesim-staging/sample_config_hwdb.yaml -r ${CY_DIR}/sims/firesim-staging/sample_config_build_recipes.yaml
firesim runworkload -a ${CY_DIR}/sims/firesim-staging/sample_config_hwdb.yaml -r ${CY_DIR}/sims/firesim-staging/sample_config_build_recipes.yaml
```

In a separate window, log into the runner machine and run:

```shell
screen -r fsim0
```

Wait for the console to boot, then verify multiple cores in the booted simulation:

```shell
# cat /proc/cpuinfo 
processor       : 0
hart            : 1
isa             : rv64imafdckph
mmu             : sv39
uarch           : sifive,rocket0
mvendorid       : 0x0
marchid         : 0x1
mimpid          : 0x20181004

processor       : 1
hart            : 0
isa             : rv64imafdckph
mmu             : sv39
uarch           : sifive,rocket0
mvendorid       : 0x0
marchid         : 0x1
mimpid          : 0x20181004
```

Congratulations! You have now simulated a dual-core RISC-V Rocket Core and booted Linux on an FPGA! 🍾 🍾 🍾

## Building BlackBox Designs

Chipyard is capable of wrapping custom Verilog designs into a [BlackBox](https://chipyard.readthedocs.io/en/main/Customization/Incorporating-Verilog-Blocks.html). This allows you to use your custom Verilog designs as accelerators attached to the core, or as the core itself.

FireSim has an example using a single-core [CVA6 RISC-V Verilog](https://chipyard.readthedocs.io/en/main/Generators/CVA6.html) implemenation.
