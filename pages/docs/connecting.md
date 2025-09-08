---
layout: wiki
title: Connecting
description: Learn how to get started with this documentation system
author: Berkeley eXtensible Environment Team
last_modified_at: {{ page.last_modified_at }}
category: Guide
author: Berkeley eXtensible Environment Team
breadcrumbs:
  - title: Documentation
    url: /docs/
  - title: Environment
    url: /docs/environment/
  - title: Connecting
    url: /docs/environment/connecting
prev_page:
  title: BXE Environment
  url: /docs/environment/
next_page:
  title: Getting Started
  url: /docs/environment/getting-started
navbar: false
parent: Environment
order: 21
permalink: /docs/environment/connecting/
---

# Connecting to BXE

## Requesting a BXE Account

1. Please contact the LBNL T&E Team.
2. You will receive an email with login username, password, and the BXE FireSim node you have been assigned.
3. Log in via SSH to [`bxe.lbl.gov`](ssh://bxe.lbl.gov).

    a. If it's your first time logging in, you will be asked to reset your password.

```shell
$ ssh <USERNAME>@bxe.lbl.gov
Last login: Wed Apr 26 12:17:01 2023 from xxx.xxx.xxx.xxx
[USERNAME@bxe ~]$
```

<a name="ssh-keygen"></a>

4. Once logged in, generate an SSH Key Pair on `bxe.lbl.gov`:

```shell
[USERNAME@bxe ~]$ ssh-keygen -t ed25519 -C "<EMAIL ADDRESS>"
```

5. Send a copy of this generated Public Key (`~/.ssh/id_ed25519.pub`) to the email that supplied you the account details.

6. Once confirmed, log into your assigned node and have fun!

## Using SSH ProxyJump
You can log into your BXE VM directly from your host using SSH ProxyJump (`-J` option).

<div class="alert alert-primary mt-4">
    <h5><strong><i class="fas fa-info-circle me-2"></i>NOTE</strong></h5>
    <p>You'll need to download a copy of your generated SSH private (<code>~/.ssh/id_ed25519</code>) and public (<code>~/.ssh/id_ed25519.pub</code>) keys from the previous <a href="#requesting-a-bxe-account">Requesting a BXE Account</a> section to your local machine in order for ProxyJump to work.</p>
</div>

### Command Line

```shell
ssh -i <LOCAL-PATH-TO-BXE-SSH-PRIVATEKEY> -J <USERNAME>@bxe.lbl.gov bxeuser@firesim-xxx
```

<!-- ## SSH Config (used for [![vscode](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9a/Visual_Studio_Code_1.35_icon.svg/2048px-Visual_Studio_Code_1.35_icon.svg.png){height=24px} `vscode`](https://code.visualstudio.com/download)) -->
### SSH Config (used for <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/9a/Visual_Studio_Code_1.35_icon.svg/2048px-Visual_Studio_Code_1.35_icon.svg.png" class="bi" style="max-height:1em" alt="VS Code"> VSCode)

If you use <a class="icon-link" href="https://code.visualstudio.com/download" target="_blank"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/9a/Visual_Studio_Code_1.35_icon.svg/2048px-Visual_Studio_Code_1.35_icon.svg.png" class="bi" alt="VS Code"> VSCode</a>, add the following to your local `~/.ssh/config`:

```conf
Host firesim-xxx
  Hostname firesim-xxx
  User bxeuser
  IdentityFile <LOCAL-PATH-TO-BXE-SSH-PRIVATEKEY>
  ProxyJump <USERNAME>@bxe.lbl.gov
```

You can now simply login with:

```shell
ssh firesim-xxx
```

## Opening a Persistent Session on BXE FireSim Nodes
With any remote connection, disruptions happen. This can mean losing your work, like a running simulation, a custom architecture build, etc. Once you have established a connection to you BXE FireSim node, we recommend performing any work in a persistent session.

Below you'll find some instructions on two options we provide.

### Using `tmux`

Log into your assigned BXE FireSim node and launch `tmux`.

```shell
$ ssh -i <LOCAL-PATH-TO-BXE-SSH-PRIVATEKEY> -J <USERNAME>@bxe.lbl.gov bxeuser@firesim-xxx
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.15.0-71-generic x86_64)
# ...
bxeuser@firesim-xxx:~$ tmux
```

- To launch a new `tmux` window: <kbd>Ctrl</kbd>/<kbd>&#8984;cmd</kbd>+<kbd>b</kbd>, then type <kbd>:new</kbd>
- To switch between `tmux` windows: <kbd>Ctrl</kbd>/<kbd>&#8984;cmd</kbd>+<kbd>b</kbd>, type <kbd>s</kbd>, then select the number of the window you want to switch too
- To detach from a `tmux` session but keep the session running: <kbd>Ctrl</kbd>/<kbd>&#8984;cmd</kbd>+<kbd>d</kbd>

<div class="alert alert-primary mt-4">
    <h5><strong><i class="fas fa-info-circle me-2"></i>NOTE</strong></h5>
    <p>This <a href="https://tmuxcheatsheet.com/" target="_blank"><code>tmux</code> Cheat Sheet & Quick Reference</a> is a good resource.</p>
</div>

### Using VNC

Your BXE FireSim node comes installed with TigerVNC. However, you'll have to forward the VNC port with SSH tunneling. While you can use any VNC port and any port on the login node, we recommend the following settings. In this example, we will use:

- SSH Tunnel port: `:9501`
- VNC Port: `:1` (TCP Port: `:5901`)

---

Log into your designated BXE FireSim node, tunneling the you desired SSH port to your VNC port. Once logged in, launch `vncserver` with your desired port.

```shell
$ ssh -L 9501:localhost:5901 -i <LOCAL-PATH-TO-BXE-SSH-PRIVATEKEY> -J <USERNAME>@bxe.lbl.gov bxeuser@firesim-xxx
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.15.0-71-generic x86_64)
# ...
bxeuser@firesim-xxx:~$ vncserver :1
```

Open your VNC Viewer of choice and open the SSH Tunnel port: `localhost:9501`.
