---
layout: wiki
title: First Time Install
description: This guide explains how to use the <code>/opt/bxe/installBXE.sh</code> script to set up BXE in your environment for the first time.
author: Berkeley eXtensible Environment Team
category: Guide
author: Berkeley eXtensible Environment Team
breadcrumbs:
  - title: Documentation
    url: /docs/
  - title: Environment
    url: /docs/environment/
  - title: First Time Install
    url: /docs/environment/first-time
prev_page:
  title: BXE Environment
  url: /docs/environment/building
next_page:
  title: Default FireSim Simulation
  url: /docs/running/default-sim/
navbar: false
parent: Environment
order: 32
permalink: /docs/environment/first-time/
---

## Install Chipyard with FireSim

The `/opt/bxe/installBXE.sh` script automates the installation of Chipyard and FireSim for the BXE environment. We will be installing the Chipyard with Firesim integration. Run the following command:

```bash
/opt/bxe/installBXE.sh chipyard
```

This will install Chipyard to `$HOME/chipyard` (or prompt for an alternative if it already exists). You should see the following output:

```bash
$ /opt/bxe/installBXE.sh chipyard
==> Installation Path: /home/$USER/chipyard
==> Installing BXE configuration...
  ✓ BXE configuration installed
==> Cloning Chipyard repository...                                                                            
Cloning into '/home/$USER/chipyard'...                                                                        
remote: Enumerating objects: 47372, done.                                                                     
remote: Counting objects: 100% (9646/9646), done.                                                             
remote: Compressing objects: 100% (4146/4146), done.                                                          
remote: Total 47372 (delta 5727), reused 5534 (delta 5485), pack-reused 37726 (from 4)                        
Receiving objects: 100% (47372/47372), 63.64 MiB | 10.87 MiB/s, done.                                         
Resolving deltas: 100% (26226/26226), done.                                                                   
  ✓ Chipyard cloned at: /home/$USER/chipyard                                                                  
  ✓ Chipyard commit: ee46501f                                                                                 
==> Building Chipyard natively...
### ...                                         
```

The install process takes about 10 to 15 minutes. You should the see the following once the installation is complete:

```bash
 ========== BEGINNING STEP 11: Cleaning up repository ==========                                              
~/chipyard/generators/constellation ~/chipyard                                                                
Cleared directory 'espresso'                                                                                  
Submodule 'espresso' (https://github.com/chipsalliance/espresso.git) unregistered for path 'espresso'         
~/chipyard                                                                                                    
~/chipyard/tools/cde ~/chipyard                                                                               
~/chipyard                                                                                                    
~/chipyard/generators/cva6/src/main/resources/cva6/vsrc ~/chipyard                                            
Cleared directory 'cva6'                                                                                      
Submodule 'src/main/resources/vsrc/cva6' (https://github.com/openhwgroup/cva6.git) unregistered for path 'cva6
'                                                                                                             
~/chipyard                                                                                                    
Setup complete!                                                                                               
  ✓ FireSim location: /home/$USER/chipyard/sims/firesim                                                       
  ✓ FireSim commit: b084672c2                                                                                 
==> Installing BXE FireSim configurations...                                                                  
  ✓ BXE FireSim configurations installed                                                                      
==> Installing profile configuration...                                                                       
  ✓ Profile configured
==> Checking SSH key...
  SSH key not found, regenerating...
Generating public/private ed25519 key pair.
Your identification has been saved in /home/$USER/.ssh/firesim.pem
Your public key has been saved in /home/$USER/.ssh/firesim.pem.pub
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx firesim.pem
The key's randomart image is:
+--[ED25519 256]--+
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
+----[SHA256]-----+
==> FireSim Public Key:
ssh-ed25519 XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX firesim.pem

Please send the public key above to the admin to ensure it exists on the FireSim runner machines.

========================================
BXE Install Complete!
========================================
```

As the installer suggests, send that SSH key to the BXE team so that you can access the FPGA hardware to run your simulations.

## Initializing FireSim Config Files

Initialize the FireSim manager to work with the AMD/Xilinx Alveo U250. Perform the following steps:

```bash
source $FIRESIM_ROOT/sourceme-manager.sh --skip-ssh-setup
firesim managerinit --platform xilinx_alveo_u250
```

This will set up `config_build_recipes.yaml`, `config_build.yaml`, `config_hwdb.yaml`, and `config_runtime.yaml`.

---

You're now ready to run FireSim simulations and build your own FireSim designs. Happy Building!
