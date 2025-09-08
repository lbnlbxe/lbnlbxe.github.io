---
layout: wiki
title: Software Walkthrough
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
  - title: Software Walkthrough
    url: /docs/building/software-walkthrough/
prev_page:
  title: Hardware Walkthrough
  url: /docs/building/hardware-walkthrough/
navbar: false
parent: Building
order: 32
permalink: /docs/building/software-walkthrough/
---

# Custom Software Walkthrough

In order to run software on your custom hardware design on BXE FireSim, you need to build the workload first. **FireMarshal** is responsible building the binaries and disk images for your workload. FireMarshal generates two kinds of workloads: _Linux-based Workloads_ and _Bare-Metal Workloads_.

If you're interested in _Bare-Metal Workloads_, see the configuration contained within `$FIREMARSHAL_ROOT/test/bare.yaml` and it's associated files in `$FIREMARSHAL_ROOT/test/bare`. The FireMarshal Documentation for [Bare-Metal Workloads](https://firemarshal.readthedocs.io/en/latest/Tutorials/custom.html#bare-metal-workload) describes the process.

In this document, we'll be focusing building **Linux-based Workloads**.

## Getting Started

Before we begin, we'll need to add the FireSim environment to our path. We will be doing all of this workload work in the FireMarshal directory, so make sure you're navigated there:

```shell
cd $FIRESIM_ROOT
source sourceme-manager.sh --skip-ssh-setup
cd $FIREMARHSAL_ROOT
```

You can skip the source step if `firesim` is already in your `$PATH`.

### Bundled Linux Distributions

FireMarshal comes prepackaged with RISC-V Linux distributions:

- Ubuntu 20.04.5 LTS / Focal Fossa (FireMarshal Workload: `ubuntu-base.json`) - **_RECOMMENDED_**
  - Username: `ubuntu`
  - Password: `bxeubuntu`
- [Buildroot](https://firemarshal.readthedocs.io/en/latest/distros/Buildroot.html) (FireMarshal Workload: `br-base.json`)
  - Username: `root`
  - Password: N/A
- ~~[Fedora Rawhide](https://firemarshal.readthedocs.io/en/latest/distros/Fedora.html) - Minimal (FireMarshal Workload: `fedora-base.json`)~~

These are your _base workloads_. When you build your software workload, you will be building on either one of these base images.

If you'd like to use a different OS as your base image, take a look at JSON for these base workloads in `$FIREMARSHAL_ROOT/boards/firechip/base-workloads` and the associated distro files directory in `$FIREMARSHAL_ROOT/boards/firechip/distros`.

### Uniform vs. Non-Uniform Workloads

Since FireSim can handle a number of simulations at once time, there are two different modes to describe your workload:

- **Uniform Workload**: A single type of job that is run on as many simulations as specified by the user.
- **Non-uniform Workload**: Several different jobs, where there must be exactly as many jobs as there are running simulated nodes.

Given that BXE FireSim is currently limited to one simulation/FPGA per node, we will be limiting discussion in this document to **Uniform Workloads**.

### Example Workloads

FireMarshal comes bundled with some prepackaged workload examples that can help you build your own workload. You can find these example workloads in `$FIREMARSHAL_ROOT/example-workloads`. These examples include:

- `example-fed`
  - A Fedora-based workload bundled with non-uniform jobs `qsort` and `pySort` (out of scope for Single-FPGA simulations)
- `gdb`
  - A Buildroot-based workload bundled with a `gdb` job
- `parallelJobs`
  - A Buildroot-based workload for running different jobs on a many FPGA system (out of scope for Single-FPGA simulations)

These are excellent templates to use for building your custom workload.

### Anatomy of a Workload

FireMarshal workloads have the following file layout:

```plaintext
WORKLOAD.json
WORKLOAD
│ - guest-init.sh
│ - host-init.sh
│ - [JOBS].sh
└───overlay
    │ - [FILES TO COPY TO /]
```

This is the anatomy of `WORKLOAD.json`:

```json
{
  "name" : "WORKLOAD",
  "base" : "BASE.json",
  "overlay" : "overlay",
  "host-init" : "host-init.sh",
  "guest-init" : "guest-init.sh",
  "jobs" : [
    {
      "name" : "job0",
      "outputs" : [ "/root/run_result.csv" ],
      "run" : "job0.sh"
    },
    {
      "name" : "job1",
      "outputs" : [ "/root/run_result.csv" ],
      "run" : "job1.sh"
    }
  ]
}
```

| Key                        | Value                                                                                                                                                                                                                                                                              |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`                     | The name of your workload.                                                                                                                                                                                                                                                         |
| `base`                     | The FireMarhsal Workload JSON that your workload will use to build it's image. This can be the aforementioned base workloads (`ubuntu-base.json`, `br-base.json`), or any other FireMarhsal Worklaod JSON that you have previously built.                                          |
| `overlay`                  | The directory holding the files that will be copied into your image at `/`. This is the place to put any files you'd like copied into the image, with the directory structure being where the files will end up in the image.                                                      |
| `host-init`                | Script that will be run prior to the image build. If you have any staticly linked programs, this would be the place to kick off `make`.                                                                                                                                            |
| `guest-init`               | Script that will be run after the image boots. This script allows you to pull/build any dependencies for your workload to run.                                                                                                                                                     |
| `jobs`<br/>_(Nonuniform ONLY)_ | List of all jobs to run (i.e. `job0.sh`, `job1.sh`) on your image and where the results of these runs can be found (the list described in `outputs`) that will be copied after simulation is complete. <br/><strong><i class="fas fa-info-circle me-2"></i>NOTE</strong>: This is only used for **Nonuniform Workloads** only. |

#### Comprehensive Configuration Options

Above is a summarized list of configuration options available in FireMarshal. For a more comprehensive list of configuration options, visit FireMarshal's [Workload Specification](https://firemarshal.readthedocs.io/en/latest/workloadConfig.html) page.

## Building an Example Workload

In this walkthrough, we'll show you how to build how to build a Buildroot-based workload. Using `example-fed.json` as template, we'll create a uniform workload for BXE FireSim. Let's create the aforementioned directory structure to store our new workload:

```shell
# Make sure you're in $FIREMARSHAL_ROOT with FireSim in the environment
mkdir bxe-workloads && cd bxe-workloads
mkdir -p example-br/overlay
cp ../example-workloads/example-fed/host-init.sh example-br/.
cp -r ../example-workloads/example-fed/overlay example-br/.
touch example-br/guest-init.sh
touch example-br.json
```

Let's start by filling out `example-br/guest-init.sh`:

```sh
#!/bin/bash
# set -x

# This is an example of the sort of thing you might want to do in an init script.
# Note that this script will be run exactly once on your image in qemu.

# Note: you will see a bunch of fedora boot messages and possibly even a login
# prompt while building as this script runs. Don't worry about the login promt,
# your script is running in the background.

# In this case, we will use fedora's package manager to install something (the
# full-featured 'time' command to replace the shell builtin). We also use pip
# to install a python package used by one of the benchmarks. You can also
# download stuff, compile things that don't support cross-compilation, and/or
# configure your system in this script.

# Note that we call poweroff at the end. This is recomended because this script
# will be run automatically during the build process. If you leave it off, the
# build script will wait for you to interact with the booted image and shut
# down before it continues (which might be useful when debugging a workload).


poweroff
```

Then we build our FireMarshal workload JSON, `example-br.json`:

```JSON
{
  "name" : "example-br",
  "base" : "br-base.json",
  "overlay" : "overlay",
  "host-init" : "host-init.sh",
  "guest-init" : "guest-init.sh"
}
```

Now, we're ready to start building the workload.

```shell
# Make sure you're in $FIREMARSHAL_ROOT with FireSim in the environment
cd ..
./marshal build bxe-workloads/example-br.json
```

From `example-br.json`, FireMarshal will first build the base workload (`br-base.json`), run `host-init.sh` to build static binaries, copy the files and directories in `overlay` to `/` on the image, and finally run `guest-init.sh`.

Optionally, you can add the `-v` option to FireMarshal to see a more verbose output:

```shell
./marshal -v build bxe-workloads/example-br.json
```

Once complete, we can launch the image to see if everything is installed correctly.

```shell
./marshal launch bxe-workloads/example-br.json
```

FireMarshal will boot the image in QEMU, giving you an interactive console.

```plaintext
...
launching firemarshal workload run/command
firemarshal workload run/command done

Welcome to Buildroot
buildroot login:
```

You can log in with the following credentials:

- Username: `root`

Once your done, poweroff the simulation

```shell
# poweroff -f
```

If you find any issue with your build and you want to clean your image, you can run the following:

```shell
./marshal clean bxe-workloads/example-br.json
```

## Installing a Workload to FireSim

Once you're sure the workload is operating as you intend in the software QEMU simulator, you have to inform FireSim as to where to find your new disk images. You install it as follows:

```shell
./marshal install bxe-workloads/example-br.json
```

You should see the following:

```plaintext
To check on progress, either call marshal with '-v' or see the live output at:
/home/bxeuser/firesim/target-design/chipyard/software/firemarshal/logs/example-br-install-2023-05-16--23-43-05-8Q7MKK5VB3BMFXVZ.log
Workload installed to FireSim at /home/bxeuser/firesim/deploy/workloads/example-br.json
Log available at: /home/bxeuser/firesim/target-design/chipyard/software/firemarshal/logs/example-br-install-2023-05-16--23-43-05-8Q7MKK5VB3BMFXVZ.log
```

You're now ready to launch this software workload on FireSim.

## Running a Workload on FireSim

Update your `$FIRESIM_ROOT/deploy/config_runtime.yaml` to point to the newly installed workload.

```JSON
workload:
    workload_name: example-br.json
    terminate_on_completion: no
    suffix_tag: null
```

Once complete, run your FireSim Simulation and enjoy the workload running on the FPGA!
