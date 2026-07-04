---
layout: page
title: Experimental Docs
navbar: true
parent: Documentation
nav_order: 10
permalink: /docs/experimental
---

## Overview

The `/opt/bxe/installBXE.sh` script automates the installation of Chipyard and FireSim for the BXE environment. It supports three installation modes:

1. **Chipyard** - Install Chipyard with FireSim integration
2. **FireSim** - Install standalone FireSim
3. **BXE Config** - Install BXE-specific configurations into an existing FireSim installation

## Prerequisites

- Linux-based system (Ubuntu/Debian recommended)
- Git installed and configured
- At least 50GB of free disk space (for Chipyard) or 30GB (for FireSim)
- Adequate RAM (16GB+ recommended)
- Internet connectivity for cloning repositories

## Quick Start

### Install Chipyard with FireSim

```bash
/opt/bxe/installBXE.sh chipyard
```

This will install Chipyard to `$HOME/chipyard` (or prompt for an alternative if it already exists).


### Install Standalone FireSim

```bash
/opt/bxe/installBXE.sh firesim
```

This will install FireSim to `$HOME/firesim` (or prompt for an alternative if it already exists).

### Install BXE Configuration

```bash
/opt/bxe/installBXE.sh bxe /path/to/firesim
```

This installs BXE-specific configurations into an existing FireSim installation at the specified path.

## Detailed Installation Process

### Chipyard Installation

```bash
/opt/bxe/installBXE.sh chipyard
```

**What happens:**

1. **Installation Path Configuration**
   - Checks if `$HOME/chipyard` exists
   - If it doesn't exist, uses that as the installation path
   - If it does exist, prompts you to enter an alternative path
   - Default alternative: `$HOME/chipyard-YYYYMMDDHHmmSS` (date-stamped)

2. **BXE Configuration Installation**
   - Creates `~/.bxe` directory
   - Backs up existing configuration if present
   - Installs BXE configuration files and managers

3. **Chipyard Repository Cloning**
   - Clones the Chipyard repository from GitHub
   - Displays the Chipyard location and Git commit hash

4. **Chipyard Build Setup**
   - Runs `build-setup.sh` to prepare the Chipyard environment
   - Applies native build arguments by default
   - If `BXE_CONTAINER` environment variable is set, uses container-specific build arguments

5. **FireSim Discovery**
   - Navigates to the FireSim subdirectory within Chipyard
   - Records FireSim location and Git commit hash

6. **Configuration Update**
   - Updates `~/.bxe/bxe-firesim.sh` with:
     - Chipyard root directory path
     - Chipyard Git commit hash
     - FireSim root directory path
     - FireSim Git commit hash
     - FireMarshal root directory path

7. **BXE FireSim Configuration**
   - Copies YAML configuration files to the FireSim deploy directory

8. **Profile Configuration**
   - Adds BXE environment sourcing to `~/.bashrc` (if not already present)
   - Line 10 is the insertion point

9. **SSH Key Management**
   - Checks for `~/.ssh/firesim.pem`
   - If missing, generates a new Ed25519 SSH key pair
   - Displays the public key
   - Instructs you to send the public key to the admin for FireSim runner configuration

### FireSim Installation

```bash
/opt/bxe/installBXE.sh firesim
```

**What happens:**

1. **Installation Path Configuration** (same as Chipyard)
   - Default path: `$HOME/firesim`
   - Prompts for alternative if it already exists
   - Default alternative: `$HOME/firesim-YYYYMMDDHHmmSS`

2. **BXE Configuration Installation** (same as Chipyard)

3. **FireSim Repository Cloning**
   - Clones standalone FireSim from GitHub
   - Displays location and Git commit hash

4. **FireSim Build Setup**
   - Runs `build-setup.sh` to prepare the FireSim environment

5. **Standalone Configuration Update**
   - Updates `~/.bxe/bxe-firesim.sh` with:
     - FireSim root directory path (no Chipyard paths)
     - FireSim Git commit hash
     - Conda root directory

6. **BXE FireSim Configuration** (same as Chipyard)

7. **Profile Configuration** (same as Chipyard)

8. **SSH Key Management** (same as Chipyard)

### BXE Configuration Installation

```bash
/opt/bxe/installBXE.sh bxe /path/to/firesim
```

**Requirements:**

- The FireSim path must exist
- The FireSim path must contain a `deploy/` subdirectory
- FireSim must have been installed via `installBXE.sh firesim` or the official FireSim installation process

**What happens:**

1. **Path Validation**
   - Verifies the provided FireSim path exists
   - Checks for the `deploy/` subdirectory

2. **Configuration Reset Prompt**
   - Asks if you want to reset the BXE configuration
   - Enter `y` or `Y` to reset, any other input to skip

3. **BXE Configuration Installation** (if you chose to reset)
   - Creates or updates `~/.bxe` directory
   - Backs up existing configuration

4. **BXE FireSim Configuration**
   - Copies YAML configuration files to the FireSim deploy directory

5. **SSH Key Management**
   - Performs the same SSH key checks and setup as other installation types

## Installation Path Behavior

### First Installation

When you first run the script, it uses the default path:

```bash
/opt/bxe/installBXE.sh chipyard
# Installs to: $HOME/chipyard
```

### Multiple Installations

If you already have an installation and want to add another:

```bash
/opt/bxe/installBXE.sh chipyard
# ⚠️  Directory $HOME/chipyard already exists

# The script will prompt:
# Default alternative: $HOME/chipyard-20250115143022
# Enter installation path (or press Enter to use default):
```

**Options:**

1. Press Enter to use the date-stamped path
2. Type a custom path and press Enter

Example custom path:
```
Enter installation path (or press Enter to use default): $HOME/chipyard-dev
# Installs to: $HOME/chipyard-dev
```

## SSH Key Management

At the end of any installation, the script performs SSH key setup:

1. **Key Check**
   - Looks for `~/.ssh/firesim.pem`
   - If found: displays "SSH key already exists"
   - If missing: generates a new Ed25519 key pair

2. **Key Generation** (if needed)
   - Creates `~/.ssh/firesim.pem` (private key)
   - Creates `~/.ssh/firesim.pem.pub` (public key)

3. **Public Key Display**
   - Shows your public key in yellow
   - You must send this key to your FireSim cluster admin
   - The admin needs to add it to the authorized keys on FireSim runner machines

**Important:** Without this key configured on the runner machines, you won't be able to communicate with FireSim.

## Configuration Files

### BXE Configuration Directory

All BXE configurations are stored in `~/.bxe/`:

```
~/.bxe/
├── bxe-firesim.sh          # Main BXE environment setup script
├── config_build.yaml       # Chipyard build configuration
├── managers/
│   └── *.py                # BXE manager scripts
└── *.yaml                  # FireSim deployment configurations
```

### BXE Shell Environment

The script adds a line to your `~/.bashrc` to source the BXE environment:

```bash
source ~/.bxe/bxe-firesim.sh
```

This line is added at line 10 if it doesn't already exist.

To activate the BXE environment in your current shell:

```bash
source ~/.bxe/bxe-firesim.sh
```

### Conda Root Detection

The script automatically detects Conda:

1. Prefers system Conda at `/opt/conda`
2. Falls back to user Conda at `~/.conda`
3. Can be overridden via `CONDA_ROOT` environment variable

## Troubleshooting

### Installation Takes Too Long

- The initial build can take 1-2 hours depending on your system
- Check disk space: `df -h`
- Monitor with: `watch -n 5 'ps aux | grep build'`

### "Directory already exists" Error

If you see an error about a directory existing:

1. Choose a different installation path when prompted, or
2. Delete/rename the existing directory, or
3. Use the `bxe` installation mode to use an existing installation

### SSH Key Error

If the script fails during SSH key generation:

```bash
# Manually regenerate the key
./regenSSHKey.sh
```

Then contact the LBNL BXE admin to add the new public key.

### Conda Not Found

If the script can't find Conda:

```bash
# Install Conda first
wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh
bash Miniforge3-Linux-x86_64.sh
```

Then run the installation script again.

### Git Clone Fails

If cloning the repositories fails:

1. Check your internet connection
2. Verify you have Git installed: `git --version`
3. Try cloning manually first: `git clone https://github.com/ucb-bar/chipyard`

### Insufficient Disk Space

Installation requirements:

- **Chipyard**: 50GB+
- **FireSim**: 30GB+
- **Build directory**: 10GB+ for artifacts

Check available space:

```bash
df -h $HOME
```

## After Installation

Once installation completes successfully:

1. **Source the BXE environment:**
   ```bash
   source ~/.bxe/bxe-firesim.sh
   ```

2. **Verify installation:**
   ```bash
   which firesim
   echo $FIRESIM_ROOT
   ```

3. **Share your public key:**
   - View your key: `cat ~/.ssh/firesim.pem.pub`
   - Send it to your FireSim cluster admin
   - Wait for confirmation before attempting to use FireSim

## Advanced Configuration

### Custom Installation Paths

If you need installation in non-default locations:

```bash
/opt/bxe/installBXE.sh chipyard /custom/path/chipyard
/opt/bxe/installBXE.sh firesim /custom/path/firesim
```

### Reinstalling Over Existing Setup

To reinstall and reconfigure:

```bash
# Backup existing installation
mv $HOME/chipyard $HOME/chipyard-backup

# Run installer
/opt/bxe/installBXE.sh chipyard
```

<!--
### Using with Docker

If you plan to use the Docker container:

```bash
export BXE_CONTAINER=1
/opt/bxe/installBXE.sh chipyard
```

This configures the installation with container-specific build flags.
 -->
