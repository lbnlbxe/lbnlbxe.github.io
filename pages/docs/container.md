---
layout: wiki
title: Container Walkthrough
description: Build your designs with our BXE container from the comfort of your own cluster
author: Berkeley eXtensible Environment Team
category: Guide
author: Berkeley eXtensible Environment Team
breadcrumbs:
  - title: Documentation
    url: /docs/
  - title: Building Custom Designs
    url: /docs/building/
  - title: Container Walkthrough
    url: /docs/building/container/
prev_page:
  title: Software Walkthrough
  url: /docs/building/software-walkthrough/
navbar: false
parent: Building
order: 33
permalink: /docs/building/container/
---

# Container

We provide a Docker image setup with the Chipyard and FireMarshal environment that is used within BXE FireSim to build designs. This guide will show you how to download the Docker image, and build custom designs with Chipyard's metasimulation environment.

## Prerequisites

### Installing Docker

<img src="https://www.docker.com/wp-content/uploads/2022/03/vertical-logo-monochromatic.png" align="right" width="250px" alt="Docker Logo">

The material in this document requires the use of [Docker](https://www.docker.com/). Either one of the following options will work:

- Access to a server with Docker installed

  - Make sure your user account has been added to the `docker` group

- Use [Docker Desktop](https://docs.docker.com/get-docker/) on your local machine

<div class="row mt-4">
  <div class="col-md-6 mb-4">
    <div class="card text-white bg-info mb-3">
      <div class="card-header"><i class="fas fa-triangle-exclamation"></i> Apple Silicon</div>
        <div class="card-body">
          <h5 class="card-title">Apple Silicon</h5>
          <p class="card-text">If you use Apple Silicon (<code>aarch64</code>-based CPU), builds and simulations are prohibitively slow.
          <br/>
          <code>x86-64</code> processors are HIGHLY recommended.</p>
        </div>
      </div>
    </div>
  <div class="col-md-6 mb-4">
  </div>
</div>

### Pulling the Docker image

Docker images contain all of the files and binaries needed to run a particular task. You can think of Docker images as virtual machine snapshots. Our container registry has a collection of these images. Much like Git, these _tags_ are images that contain different files for different projects. We'll pull the `latest` tag, as shown below:

```bash
docker pull socks.lbl.gov:4567/cag/bxe/firesim
```

### Running a Docker container

Once you've pulled a Docker image, you can run within a container. You can think of containers as the virtual machine that's running the image you pulled. When a container is initialized and run, it's given a unique hash and a unique string name. When you want to access the container, you refer to its unique hash/string. You can also give your container a unique name, as demonstrated below:

> :exclamation: **NOTE:** `--privileged` is only required when using FireMarshal.

- Running a Docker container interactively
  - This runs the Docker container and attaches the console of your container

  ```bash
  docker run [--privileged] [--name CUSTOM_CONTAINER_NAME] -it socks.lbl.gov:4567/cag/bxe/firesim
  ```

- Running a Docker container and attaching later
  - You can run a Docker container in the background and attach to it
  
  ```bash
  docker run [--privileged] [--name CUSTOM_CONTAINER_NAME] socks.lbl.gov:4567/cag/bxe/firesim
  docker attach [HASHID/STRINGID/CUSTOM_CONTAINER_NAME]
  ```

#### Mounting Host Directories in Docker containers

You can mount directories from your host machine within your container with the `-v [HOSTDIR]:[CONTAINERDIR]` flag. In this example, we want to mount `/tmp/mpoint` from our host machine to `/app` in the container.

```bash
docker run \
    [--privileged] [--name CUSTOM_CONTAINER_NAME] \
    -it \
    -v /tmp/mpoint:/app \
    socks.lbl.gov:4567/cag/bxe/firesim
```

Any changes made in `/app` in the Docker container are reflected in `/tmp/mpoint` on the host. You can mount multiple directories with multiple `-v [HOSTDIR]:[CONTAINERDIR]` flags with the different mounts.``

## Initializing the Chipyard and FireMarshal Environment

Once you've run and attached to your Docker container, you should see the following output on your console:

```bash
(base) bxeuser@[HASHID]:~/firesim$
```

Run the following commands to set up the Chipyard and FireMarshal environment:

```bash
conda activate firesim
source sourceme-manager.sh --skip-ssh-setup
```

You should see the following output on your console:

```bash
(base) bxeuser@[HASHID]:~/firesim$ conda activate firesim
(firesim) bxeuser@[HASHID]:~/firesim$ source sourceme-manager.sh --skip-ssh-setup
(/home/bxeuser/firesim/.conda-env) bxeuser@[HASHID]:~/firesim$
```

You are now ready to build RTL with Chipyard and build software with FireMarshal.

## File and Directory Structure

### FireSim

Environment Variable: `$FIRESIM_ROOT`

Directory Path: `~/firesim`

Files/Directories of Note:

- `deploy`: This is where the FireSim configuration YAML is located, specifically `config_build_recipes.yaml`, `config_build.yaml`, `config_hwdb.yaml`, and `config_runtime.yaml`.

### Chipyard

Environment Variable: `$CHIPYARD_ROOT`

Directory Path: `~/firesim/target-design/chipyard`

Files/Directories of Note:

- `generators`: This is a collection of all of the source code required hardware generators contained within Chipyard. This includes Rocket Chip (SoC generator), Rocket/BOOM/CVA6 (Processors), Constellation (NoC Generator), among many more. This is the place to put any custom hardware for your design.

  - `generators/chipyard/src/main/scala/config`: This is the list of available Chipyard Mixins that Chipyard uses to generate hardware designs. 

  - `generators/chipyard/src/main/scala/DigitalTop.scala`: This file controls the top level dependencies for the RocketChip SoC generators. If you add any custom hardware, make sure to add your hardware Scala Trait to the `DigitalTop` module in `DigitalTop.scala`.

- `build.sbt`: This file manages the relative locations of all of the Scala packages needed to run the Chipyard generator. If you add any custom hardware as it's own Scala package, make sure to add its path to this file, as well as making it a dependency for the `chipyard` package.

- `sims/verilator`: This is where you run `make` with the config flag of your custom hardware. All Verilog is generated here as well.

### FireMarshal

Environment Variable: N/A

Directory Path: `$CHIPYARD_ROOT/software/firemarshal`

Files/Directories of Note:

- `example-workloads`: This directory contains examples of using FireMarshal for different OS or bare-metal based systems.
