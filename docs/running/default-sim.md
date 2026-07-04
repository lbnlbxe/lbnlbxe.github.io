---
layout: page
title: Default FireSim Simulation
parent: Running
order: 1
permalink: /docs/running/default-sim/
---

## Default FireSim Simulation

The BXE FireSim node is set up to run a Single-Core Rocket RISC-V core with a scratchpad memory on FPGA. Here are the steps to run that example.

1\. Once logged into your node, change directories to the `firesim` and source FireSim to add it to your path.

```shell
cd $FIRESIM_ROOT
source sourceme-manager.sh --skip-ssh-setup
```

2\. Make sure your `$FIRESIM_ROOT/deploy/config_runtime.yaml` looks like the following:

```yaml
# Run-time configuration for the FireSim Simulation Manager
# See https://docs.fires.im/en/stable/Advanced-Usage/Manager/Manager-Configuration-Files.html for documentation of all of these params.

run_farm:
  base_recipe: run-farm-recipes/externally_provisioned.yaml
  recipe_arg_overrides:
    # REQUIRED: default platform used for run farm hosts. this is a class specifying
    # how to run simulations on a run farm host.
    default_platform: XilinxAlveoU250InstanceDeployManager

    # REQUIRED: default directory where simulations are run out of on the run farm hosts
    default_simulation_dir: /home/bxeuser/FIRESIM_RUNS_DIR

    # REQUIRED: default fpga db file that enumerates what fpgas are available on the machine (used by XilinxU* Deploy Managers)
    default_fpga_db: /opt/firesim-db.json

    # REQUIRED: List of unique hostnames/IP addresses, each with their
    # corresponding specification that describes the properties of the host.
    #
    # Ex:
    # run_farm_hosts_to_use:
    #     # use localhost which is described by "four_fpgas_spec" below.
    #     - localhost: four_fpgas_spec
    #     # supply IP address, which points to a machine that is described
    #     # by "four_fpgas_spec" below.
    #     - "111.111.1.111": four_fpgas_spec
    run_farm_hosts_to_use:
        - bxeuser@vizion.lbl.gov: eight_fpgas_spec

metasimulation:
  metasimulation_enabled: false
  # vcs or verilator. use vcs-debug or verilator-debug for waveform generation
  metasimulation_host_simulator: verilator
  # plusargs passed to the simulator for all metasimulations
  metasimulation_only_plusargs: "+fesvr-step-size=128 +max-cycles=100000000"
  # plusargs passed to the simulator ONLY FOR vcs metasimulations
  metasimulation_only_vcs_plusargs: "+vcs+initreg+0 +vcs+initmem+0"

# DOCREF START: target_config area
target_config:
    topology: no_net_config
    no_net_num_nodes: 1
    link_latency: 6405
    switching_latency: 10
    net_bandwidth: 200
    profile_interval: -1

    # This references a section from config_hwdb.yaml for fpga-accelerated simulation
    # or from config_build_recipes.yaml for metasimulation
    # In homogeneous configurations, use this to set the hardware config deployed
    # for all simulators
    default_hw_config: alveo_u250_firesim_rocket_singlecore_no_nic

    # Advanced: Specify any extra plusargs you would like to provide when
    # booting the simulator (in both FPGA-sim and metasim modes). This is
    # a string, with the contents formatted as if you were passing the plusargs
    # at command line, e.g. "+a=1 +b=2"
    plusarg_passthrough: ""
# DOCREF END: target_config area

tracing:
    enable: no

    # Trace output formats. Only enabled if "enable" is set to "yes" above
    # 0 = human readable; 1 = binary (compressed raw data); 2 = flamegraph (stack
    # unwinding -> Flame Graph)
    output_format: 0

    # Trigger selector.
    # 0 = no trigger; 1 = cycle count trigger; 2 = program counter trigger; 3 =
    # instruction trigger
    selector: 1
    start: 0
    end: -1

autocounter:
    read_rate: 0

workload:
    workload_name: br-base-uniform.json
    terminate_on_completion: no
    suffix_tag: null

host_debug:
    # When enabled (=yes), Zeros-out FPGA-attached DRAM before simulations
    # begin (takes 2-5 minutes).
    # In general, this is not required to produce deterministic simulations on
    # target machines running linux. Enable if you observe simulation non-determinism.
    zero_out_dram: no
    # If disable_synth_asserts: no, simulation will print assertion message and
    # terminate simulation if synthesized assertion fires.
    # If disable_synth_asserts: yes, simulation ignores assertion firing and
    # continues simulation.
    disable_synth_asserts: no

# DOCREF START: Synthesized Prints
synth_print:
    # Start and end cycles for outputting synthesized prints.
    # They are given in terms of the base clock and will be converted
    # for each clock domain.
    start: 0
    end: -1
    # When enabled (=yes), prefix print output with the target cycle at which the print was triggered
    cycle_prefix: yes
# DOCREF END: Synthesized Prints
```

3\. Set up the simulation infrastructure. This will build and deploy all of the software for a FireSim simulation, as well as prepare the FPGA for simulation.

```shell
firesim infrasetup -a ${CY_DIR}/sims/firesim-staging/sample_config_hwdb.yaml -r ${CY_DIR}/sims/firesim-staging/sample_config_build_recipes.yaml
```

For a first run, this may take some time. You should expect the following output:

```shell
(/home/bxeuser/chipyard/.conda-env) bxeuser@firesim-xxx:~/chipyard/sims/firesim$ firesim infrasetup -a ${CY_DIR}/sims/firesim-staging/sample_config_hwdb.yaml -r ${CY_DIR}/sims/firesim-staging/sample_config_build_recipes.yaml          
FireSim Manager. Docs: https://docs.fires.im                                                                                                                                                                                                
Running: infrasetup                                                                                                                                                                                                                         
                                                                                                                                                                                                                                            
firesim_boom_singlecore_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                     
firesim_boom_singlecore_no_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                  
firesim_gemmini_printf_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                
firesim_gemmini_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                       
firesim_rocket_quadcore_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                     
firesim_rocket_quadcore_no_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                  
firesim_rocket_singlecore_no_nic_l2_lbp is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                        
firesim_rocket_singlecore_sha3_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                              
firesim_rocket_singlecore_sha3_no_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                           
firesim_rocket_singlecore_sha3_no_nic_l2_llc4mb_ddr3_printf is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                    
firesim_supernode_rocket_singlecore_nic_l2_lbp is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                 
vitis_firesim_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                         
vitis_firesim_gemmini_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                 
alveo_u250_firesim_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                    
alveo_u250_firesim_rocket_quadcore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                      
alveo_u250_firesim_boom_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                      
alveo_u250_firesim_rocket_singlecore_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                       
alveo_u250_firesim_gemmini_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                            
alveo_u200_firesim_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                    
alveo_u280_firesim_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                    
xilinx_vcu118_firesim_rocket_singlecore_4GB_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                             
nitefury_firesim_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                      
run_farm_hosts_dict defaultdict(<class 'list'>, {'wilson.lbl.gov': [(<runtools.run_farm.Inst object at 0x7f2dcac3c3a0>, None)]})                                                                                                            
set_server_hardware_config 0 None                                                                                                                                                                                                           
[wilson.lbl.gov] Checking if host instance is up...                                                                                                                                                                                         
xilinx_alveo_u250/                                                                                                                                                                                                                          
xilinx_alveo_u250/metadata                                                                                                                                                                                                                  
xilinx_alveo_u250/firesim.mcs                                                                                                                                                                                                               
xilinx_alveo_u250/firesim.bit                                                                                                                                                                                                               
Building FPGA software driver for xilinx_alveo_u250-firesim-FireSim-FireSimRocketConfig-BaseXilinxAlveoU250Config                                                                                                                           
[localhost] run: make PLATFORM=xilinx_alveo_u250 TARGET_PROJECT=firesim TARGET_PROJECT_MAKEFRAG=/home/bxeuser/chipyard/sims/firesim-staging/../../generators/firechip/chip/src/main/makefrag/firesim DESIGN=FireSim TARGET_CONFIG=FireSimRoc
ketConfig PLATFORM_CONFIG=BaseXilinxAlveoU250Config xilinx_alveo_u250                                                                                                                                                                       
[localhost] out: mkdir -p /home/bxeuser/chipyard/sims/firesim/sim/midas/src/main/scala/target-symlinks                                                                                                                                      
[localhost] out: # make the symlink dir                                                                                                                                                                                                     
[localhost] out: # symlink things                                                                                                                                                                                                           
[localhost] out: # remove any folders that don't match                                                                                                                                                                                      
[localhost] out: find /home/bxeuser/chipyard/sims/firesim/sim/midas/src/main/scala/target-symlinks/* -type d | grep -v -e "/home/bxeuser/chipyard/sims/firesim/sim/midas/src/main/scala/target-symlinks/bridgeinterfaces" -e "/home/bxeuser/
chipyard/sims/firesim/sim/midas/src/main/scala/target-symlinks/goldengateimplementations" | xargs rm -rf                                                                                                                                    
[localhost] out: source /home/bxeuser/chipyard/env.sh && \                                                                                                                                                                                  
[localhost] out:        make -C /home/bxeuser/chipyard/sims/firesim-staging \                                                                                                                                                               
[localhost] out:                SBT_PROJECT=firechip \
[localhost] out:                MODEL=FireSim \
[localhost] out:                MODEL_PACKAGE=firechip.chip \
[localhost] out:                VLOG_MODEL=FireSim \                                                                  
[localhost] out:                CONFIG=FireSimRocketConfig \
[localhost] out:                CONFIG_PACKAGE=firechip.chip \
[localhost] out:                GENERATOR_PACKAGE=chipyard \
[localhost] out:                EXTRA_CHISEL_OPTIONS=--emit-legacy-sfc \                                       
[localhost] out:                TB=unused \   
[localhost] out:                TOP=unused
[localhost] out: make[1]: Entering directory '/home/bxeuser/chipyard/sims/firesim-staging'
[localhost] out: Running with RISCV=/home/bxeuser/chipyard/.conda-env/riscv-tools
[localhost] out: make[1]: Nothing to be done for 'default'. 
[localhost] out: make[1]: Leaving directory '/home/bxeuser/chipyard/sims/firesim-staging'
[localhost] out: # firechip.chip.FireSim.FireSimRocketConfig must be same as Chipyard
[localhost] out: ln -sf /home/bxeuser/chipyard/sims/firesim-staging/generated-src/firechip.chip.FireSim.FireSimRocketConfig/firechip.chip.FireSim.FireSimRocketConfig.sfc.fir /home/bxeuser/chipyard/sims/firesim/sim/generated-src/xilinx_a
lveo_u250/xilinx_alveo_u250-firesim-FireSim-FireSimRocketConfig-BaseXilinxAlveoU250Config/firechip.chip.FireSim.FireSimRocketConfig.fir
[localhost] out: ln -sf /home/bxeuser/chipyard/sims/firesim-staging/generated-src/firechip.chip.FireSim.FireSimRocketConfig/firechip.chip.FireSim.FireSimRocketConfig.anno.json /home/bxeuser/chipyard/sims/firesim/sim/generated-src/xilinx
_alveo_u250/xilinx_alveo_u250-firesim-FireSim-FireSimRocketConfig-BaseXilinxAlveoU250Config/firechip.chip.FireSim.FireSimRocketConfig.anno.json
[localhost] out: # .d needed to run metasim CI tests                                                                  
[localhost] out: ln -sf /home/bxeuser/chipyard/sims/firesim-staging/generated-src/firechip.chip.FireSim.FireSimRocketConfig/firechip.chip.FireSim.FireSimRocketConfig.d /home/bxeuser/chipyard/sims/firesim/sim/generated-src/xilinx_alveo_u
250/xilinx_alveo_u250-firesim-FireSim-FireSimRocketConfig-BaseXilinxAlveoU250Config/firechip.chip.FireSim.FireSimRocketConfig.d

# ... More Console Output ..

[wilson.lbl.gov] Copying FPGA simulation infrastructure for slot: 0.
[wilson.lbl.gov] Unloading XDMA Driver Kernel Module.
[wilson.lbl.gov] Flash all FPGA Slots.
[wilson.lbl.gov] Copying FPGA flashing scripts for 0
[wilson.lbl.gov] Determine BDF for 0
[wilson.lbl.gov] Flashing FPGA Slot: 0 (01:00.0) with bitstream: /home/bxeuser/FIRESIM_RUNS_DIR/sim_slot_0/xilinx_alveo_u250/firesim.bit
Warning: run() received nonzero return code 1 while executing 'lsmod | grep -wq xdma'!
[wilson.lbl.gov] Loading XDMA Driver Kernel Module.
[wilson.lbl.gov] Change permissions on FPGA slot
[wilson.lbl.gov] Determine BDF for 0
[wilson.lbl.gov] Changing permissions on FPGA Slot: 0 (bdf:01:00.0)
The full log of this run is:
/home/bxeuser/chipyard/sims/firesim/deploy/logs/2024-10-31--22-18-09-infrasetup-ZW0RGPULZGU4NVFL.log
```

4\. Run your simulation!

```shell
firesim runworkload -a ${CY_DIR}/sims/firesim-staging/sample_config_hwdb.yaml -r ${CY_DIR}/sims/firesim-staging/sample_config_build_recipes.yaml
```

You should expect the following output:

```shell
FireSim Manager. Docs: https://docs.fires.im                                                                                                                                                                                                
Running: runworkload                                                                                                                                                                                                                        
                                                                                                                                                                                                                                            
firesim_boom_singlecore_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                     
firesim_boom_singlecore_no_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                  
firesim_gemmini_printf_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                
firesim_gemmini_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                       
firesim_rocket_quadcore_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                     
firesim_rocket_quadcore_no_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                  
firesim_rocket_singlecore_no_nic_l2_lbp is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                        
firesim_rocket_singlecore_sha3_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                              
firesim_rocket_singlecore_sha3_no_nic_l2_llc4mb_ddr3 is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                           
firesim_rocket_singlecore_sha3_no_nic_l2_llc4mb_ddr3_printf is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                    
firesim_supernode_rocket_singlecore_nic_l2_lbp is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                 
vitis_firesim_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                         
vitis_firesim_gemmini_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                 
alveo_u250_firesim_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                    
alveo_u250_firesim_rocket_quadcore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                      
alveo_u250_firesim_boom_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                      
alveo_u250_firesim_rocket_singlecore_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                       
alveo_u250_firesim_gemmini_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                            
alveo_u200_firesim_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                    
alveo_u280_firesim_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                    
xilinx_vcu118_firesim_rocket_singlecore_4GB_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                             
nitefury_firesim_rocket_singlecore_no_nic is overriding a deploy makefrag in your config_hwdb.yaml file. Make sure you understand why!                                                                                                      
run_farm_hosts_dict defaultdict(<class 'list'>, {'wilson.lbl.gov': [(<runtools.run_farm.Inst object at 0x7f0020d083a0>, None)]})                                                                                                            
set_server_hardware_config 0 None                                                                                                                                                                                                           
Creating the directory: /home/bxeuser/chipyard/sims/firesim/deploy/results-workload/2024-10-31--22-24-41-br-base-uniform/                                                                                                                   
xilinx_alveo_u250/                                                                                                                                                                                                                          
xilinx_alveo_u250/metadata                                                                                                                                                                                                                  
xilinx_alveo_u250/firesim.mcs                                                                                                                                                                                                               
xilinx_alveo_u250/firesim.bit                                                                                                                                                                                                               
xilinx_alveo_u250/                                                                                                                                                                                                                          
xilinx_alveo_u250/metadata                                                                                                                                                                                                                  
xilinx_alveo_u250/firesim.mcs                                                                                                                                                                                                               
xilinx_alveo_u250/firesim.bit                                                                                                                                                                                                               
[wilson.lbl.gov] Checking if host instance is up...                                                                                                                                                                                         
start_simulations_instance 1                                                                                                                                                                                                                
[wilson.lbl.gov] Starting FPGA simulation for slot: 0.                                                                                                                                                                                      
[wilson.lbl.gov] Determine BDF for 0
```

This will be replaced with a live status page:

```shell
FireSim Simulation Status @ 2024-10-31 22:25:06.882595
--------------------------------------------------------------------------------
This workload's output is located in:
/home/bxeuser/chipyard/sims/firesim/deploy/results-workload/2024-10-31--22-24-41-br-base-uniform/
This run's log is located in:
/home/bxeuser/chipyard/sims/firesim/deploy/logs/2024-10-31--22-24-41-runworkload-UHXADI37URRZQWOL.log
This status will update every 10s.
--------------------------------------------------------------------------------
Instances
--------------------------------------------------------------------------------
Hostname/IP: wilson.lbl.gov | Terminated: False
--------------------------------------------------------------------------------
Simulated Switches
--------------------------------------------------------------------------------
--------------------------------------------------------------------------------
Simulated Pipes
--------------------------------------------------------------------------------
--------------------------------------------------------------------------------
Simulated Nodes/Jobs
--------------------------------------------------------------------------------
Hostname/IP: wilson.lbl.gov | Job: br-base-uniform0 | Sim running: True 
--------------------------------------------------------------------------------
Summary
--------------------------------------------------------------------------------
1/1 instances are still running.
1/1 simulations are still running.
--------------------------------------------------------------------------------
```

Once you see the live status page, open another terminal window on your BXE FireSim node and attach to running console of the simulated system using `screen`:

```shell
screen -r fsim0
```

If you opened the console fast enough, you will see all of the boot messages of your simulated system, until your prompted for a login:

```shell
[truncated Linux boot output]
[    0.257579] Run /init as init process
[    0.291768] icenet: loading out-of-tree module taints kernel.
[    0.295288] disk [iceblk] of loaded; 590064 sectors, 1 tags, 16 max request length
Mounting /dev/iceblk as root device
[    0.302551] EXT4-fs (iceblk): mounted filesystem ce904243-7e0d-4b87-a526-50c1e4ea0967 without journal. Quota mode: disabled.
Loaded platform drivers, booting from disk:
[    0.352061] EXT4-fs (iceblk): re-mounted ce904243-7e0d-4b87-a526-50c1e4ea0967. Quota mode: disabl
ed.
running /etc/init.d/S01syslogd
Starting syslogd: OK
running /etc/init.d/S02klogd
Starting klogd: OK
running /etc/init.d/S02sysctl
Running sysctl: OK
running /etc/init.d/S10mdev
Starting mdev: OK
running /etc/init.d/S40network
Starting network: OK
running /etc/init.d/S99run
launching firemarshal workload run/command
firemarshal workload run/command done

Welcome to Buildroot
buildroot login:
```

Now you can log in as `root` (no password) and browse around the system:

```shell
Welcome to Buildroot
buildroot login: root
# uname -a
Linux buildroot 6.6.0-00004-g67bc4513761f #2 SMP Mon Oct 28 17:49:31 PDT 2024 riscv64 GNU/Linux
# cat /proc/cpuinfo
processor       : 0
hart            : 0
isa             : rv64imafdcb_zicntr_zicsr_zifencei_zihpm_zba_zbb_zbs
mmu             : sv39
uarch           : sifive,rocket0
mvendorid       : 0x0
marchid         : 0x1
mimpid          : 0x20181004

#
```

Once you're done, run `poweroff -f` on the simulated system:

```shell
# poweroff -f
[   12.456000] reboot: Power down
Power off
time elapsed: 468.8 s, simulation speed = 88.50 MHz
*** PASSED *** after 41492621244 cycles
Runs 41492621244 cycles
[PASS] FireSim Test
SEED: 1526690334
Script done, file is uartlog

[screen is terminating]
```

Returning to the manager session, you'll notice that the loop as exited:

```shell
FireSim Simulation Status @ 2024-10-31 22:27:34.515872
--------------------------------------------------------------------------------
This workload's output is located in:
/home/bxeuser/chipyard/sims/firesim/deploy/results-workload/2024-10-31--22-24-41-br-base-uniform/
This run's log is located in:
/home/bxeuser/chipyard/sims/firesim/deploy/logs/2024-10-31--22-24-41-runworkload-UHXADI37URRZQWOL.log
This status will update every 10s.
--------------------------------------------------------------------------------
Instances
--------------------------------------------------------------------------------
Hostname/IP: wilson.lbl.gov | Terminated: False
--------------------------------------------------------------------------------
Simulated Switches
--------------------------------------------------------------------------------
--------------------------------------------------------------------------------
Simulated Pipes
--------------------------------------------------------------------------------
--------------------------------------------------------------------------------
Simulated Nodes/Jobs
--------------------------------------------------------------------------------
Hostname/IP: wilson.lbl.gov | Job: br-base-uniform0 | Sim running: False
--------------------------------------------------------------------------------
Summary
--------------------------------------------------------------------------------
1/1 instances are still running.
0/1 simulations are still running.
--------------------------------------------------------------------------------
FireSim Simulation Exited Successfully. See results in:
/home/bxeuser/chipyard/sims/firesim/deploy/results-workload/2024-10-31--22-24-41-br-base-uniform/
The full log of this run is:
/home/bxeuser/chipyard/sims/firesim/deploy/logs/2024-10-31--22-24-41-runworkload-UHXADI37URRZQWOL.log
```

If you look at the workload output directory, you'll see the following:

```shell
(/home/bxeuser/chipyard/.conda-env) bxeuser@firesim-xxx:~/chipyard/sims/firesim$ ls -la deploy/results-workload/2024-10-31--22-24-41-br-base-uniform/*/*
-rw-rw-r-- 1 bxeuser bxeuser   460 Oct 31 15:24 deploy/results-workload/2024-10-31--22-24-41-br-base-uniform/br-base-uniform0/HW_CFG_SUMMARY
-rw-rw-r-- 1 bxeuser bxeuser   352 Oct 31 15:24 deploy/results-workload/2024-10-31--22-24-41-br-base-uniform/br-base-uniform0/memory_stats0.csv
-rw-rw-r-- 1 bxeuser bxeuser   101 Jul 29 14:19 deploy/results-workload/2024-10-31--22-24-41-br-base-uniform/br-base-uniform0/os-release
-rw-rw-r-- 1 bxeuser bxeuser   694 Oct 31 15:24 deploy/results-workload/2024-10-31--22-24-41-br-base-uniform/br-base-uniform0/sim-run.sh
-rw-rw-r-- 1 bxeuser bxeuser 15637 Oct 31 15:27 deploy/results-workload/2024-10-31--22-24-41-br-base-uniform/br-base-uniform0/uartlog
```

Where `uartlog` is the full console output of the simulation.

```shell
Script started on 2024-10-31 15:24:54-07:00 [TERM="screen" TTY="/dev/pts/1" COLUMNS="80" LINES="24"]
+domain found: 0x0000
+bus found: 0x01
+device found: 0x00
+function found: 0x0
+bar found: 0x0
Using: 0000:01:00.0, BAR ID: 0, PCI Vendor ID: 0x10ee, PCI Device ID: 0x903f
Opening /sys/bus/pci/devices/0000:01:00.0/vendor
Opening /sys/bus/pci/devices/0000:01:00.0/device
examining xdma/.
examining xdma/..
examining xdma/xdma7_control
examining xdma/xdma7_c2h_2
examining xdma/xdma7_events_10
examining xdma/xdma7_user
examining xdma/xdma7_events_5
examining xdma/xdma7_c2h_0
examining xdma/xdma7_events_3
examining xdma/xdma7_xvc
examining xdma/xdma7_events_1
examining xdma/xdma7_h2c_3
examining xdma/xdma7_events_15
examining xdma/xdma7_h2c_1
examining xdma/xdma7_events_13
examining xdma/xdma7_events_8
examining xdma/xdma7_c2h_3
examining xdma/xdma7_events_11
examining xdma/xdma7_events_6
examining xdma/xdma7_c2h_1
examining xdma/xdma7_events_4
examining xdma/xdma7_events_2
examining xdma/xdma7_events_0
examining xdma/xdma7_h2c_2
examining xdma/xdma7_events_14
examining xdma/xdma7_events_9
examining xdma/xdma7_h2c_0
Using xdma write queue: /dev/xdma7_h2c_0
Using xdma read queue: /dev/xdma7_c2h_0
widget_registry_t::add_widget(StreamEngine)
cpu2fpga: 0, fpga2cpu: 1
UART0 is here (stdin/stdout).
TraceRV 0: Tracing disabled, since +tracefile was not provided.
command line for program 0. argc=26:
+permissive +macaddr0=00:12:6D:00:00:02 +blkdev0=br-base-uniform0-br-base.img +niclog0=niclog0 +blkdev-log0=blkdev-log0 +trace-select=1 +trace-start=0 +trace-end=-1 +trace-output-format=0 +dwarf-file-name=br-base-uniform0-br-base-bin-dwarf +autocounter-readrate=0 +autocounter-filename-base=AUTOCOUNTERFILE +print-start=0 +print-end=-1 +linklatency0=6405 +netbw0=200 +shmemportname0=default +domain=0x0000 +bus=0x01 +device=0x00 +function=0x0 +bar=0x0 +pci-vendor=0x10ee +pci-device=0x903f +permissive-off br-base-uniform0-br-base-bin 
FireSim fingerprint: 0x46697265
TracerV: Trigger enabled from 0 to 18446744073709551615 cycles
Commencing simulation.
tsibridge_t::tick skipping tick
tsibridge_t::tick skipping tick
tsibridge_t::tick skipping tick
tsibridge_t::tick skipping tick
tsibridge_t::tick skipping tick
tsibridge_t::tick skipping tick
tsibridge_t::tick skipping tick
tsibridge_t::tick skipping tick

OpenSBI v1.2
   ____                    _____ ____ _____
  / __ \                  / ____|  _ \_   _|
 | |  | |_ __   ___ _ __ | (___ | |_) || |
 | |  | | '_ \ / _ \ '_ \ \___ \|  _ < | |
 | |__| | |_) |  __/ | | |____) | |_) || |_
  \____/| .__/ \___|_| |_|_____/|___^@/_____|
        | |
        |_|

Platform Name             : ucb-bar,chipyard
Platform Features         : medeleg
Platform HART Count       : 1
Platform IPI Device       : aclint-mswi
Platform Timer Device     : aclint-mtimer @ 1000000Hz
Platform Console Device   : sifive_uart
Platform HSM Device       : ---
Platform PMU Device       : ---
Platform Reboot Device    : htif
Platform Shutdown Device  : htif
Platform Suspend Device   : ---
Firmware Base             : 0x80000000
Firmware Size             : 360 KB
Firmware RW Offset        : 0x40000
Runtime SBI Version       : 1.0

Domain0 Name              : root
Domain0 Boot HART         : 0
Domain0 HARTs             : 0*
Domain0 Region00          : 0x0000000002000000-0x000000000200ffff M: (I,R,W) S/U: ()
Domain0 Region01          : 0x0000000080040000-0x000000008005ffff M: (R,W) S/U: ()
Domain0 Region02          : 0x0000000080000000-0x000000008003ffff M: (R,X) S/U: ()
Domain0 Region03          : 0x0000000000000000-0xffffffffffffffff M: (R,W,X) S/U: (R,W,X)
Domain0 Next Address      : 0x0000000080200000
Domain0 Next Arg1         : 0x0000000081600000
Domain0 Next Mode         : S-mode
Domain0 SysReset          : yes
Domain0 SysSuspend        : yes

Boot HART ID              : 0
Boot HART Domain          : root
Boot HART Priv Version    : v1.12
Boot HART Base ISA        : rv64imafdcx
Boot HART ISA Extensions  : none
Boot HART PMP Count       : 8
Boot HART PMP Granularity : 4
Boot HART PMP Address Bits: 33
Boot HART MHPM Count      : 0
Boot HART MIDELEG         : 0x0000000000000222
Boot HART MEDELEG         : 0x000000000000b109
[    0.000000] Linux version 6.6.0-00004-g67bc4513761f (bxeuser@firesim-c301eb5) (riscv64-unknown-linux-gnu-gcc (gc891d8dc23e) 13.2.0, GNU ld (GNU Binutils) 2.42) #2 SMP Mon Oct 28 17:49:31 PDT 2024
[    0.000000] Machine model: ucb-bar,chipyard
[    0.000000] Forcing kernel command line to: console=ttyS0 console=ttySIF0,3686400 earlycon
[    0.000000] SBI specification v1.0 detected
[    0.000000] SBI implementation ID=0x1 Version=0x10002
[    0.000000] SBI TIME extension detected
[    0.000000] SBI IPI extension detected
[    0.000000] SBI RFENCE extension detected
[    0.000000] SBI SRST extension detected
[    0.000000] earlycon: sifive0 at MMIO 0x0000000010020000 (options '')
```

Congratulations! You have now simulated a RISC-V Core and booted Linux on an FPGA! 🍾 🍾 🍾

You can log into the Buildroot with the following credentials:

> Username: `root`
>
> Password: [No Password]

Make sure to end the simulation by shutting down the simulation with the following command:

```bash
poweroff -f
```
