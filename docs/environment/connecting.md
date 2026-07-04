---
layout: page
title: Connecting to BXE
parent: Environment
nav_order: 1
permalink: /docs/environment/connecting/
---

## Connecting to BXE

{:toc}

### Requesting a BXE Account

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

### Using SSH ProxyJump

You can log into your BXE VM directly from your host using SSH ProxyJump (`-J` option).

{: .important }
> You'll need to download a copy of your generated SSH private (`~/.ssh/id_ed25519`) and public (`~/.ssh/id_ed25519.pub`) keys from the previous [Requesting a BXE Account](#requesting-a-bxe-account) section to your local machine in order for ProxyJump to work.

#### Command Line

```shell
ssh -i <LOCAL-PATH-TO-BXE-SSH-PRIVATEKEY> -J <USERNAME>@bxe.lbl.gov <USERNAME>@bxe-xxx
```

<!-- #### SSH Config (used for [![vscode](https://code.visualstudio.com/assets/branding/code-stable.png){:height=24px} `vscode`](https://code.visualstudio.com/download)) -->
#### SSH Config (used for <img src="https://code.visualstudio.com/assets/branding/code-stable.png" class="bi" style="max-height:1em" alt="VS Code"> VSCode)

If you use <a class="icon-link" href="https://code.visualstudio.com/download" target="_blank"><img src="https://code.visualstudio.com/assets/branding/code-stable.png" class="bi" style="max-height:1em" alt="VS Code"> VSCode</a>, add the following to your local `~/.ssh/config`:

```conf
Host bxe-xxx
  Hostname bxe-xxx
  User <USERNAME>
  IdentityFile <LOCAL-PATH-TO-BXE-SSH-PRIVATEKEY>
  ProxyJump <USERNAME>@bxe.lbl.gov
```

You can now simply login with:

```shell
ssh bxe-xxx
```

### Opening a Persistent Session on BXE FireSim Nodes

With any remote connection, disruptions happen. This can mean losing your work, like a running simulation, a custom architecture build, etc. Once you have established a connection to you BXE FireSim node, we recommend performing any work in a persistent session.

Below you'll find some instructions on options we provide.

#### Using `tmux`

Log into your assigned BXE FireSim node and launch `tmux`.

```shell
$ ssh -i <LOCAL-PATH-TO-BXE-SSH-PRIVATEKEY> -J <USERNAME>@bxe.lbl.gov <USERNAME>@bxe-xxx
Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 6.14.0-35-generic x86_64)
# ...
<USERNAME>@bxe-xxx:~$ tmux
```

- To launch a new `tmux` window: <kbd>Ctrl</kbd>/<kbd>&#8984;cmd</kbd>+<kbd>b</kbd>, then type <kbd>:new</kbd>
- To switch between `tmux` windows: <kbd>Ctrl</kbd>/<kbd>&#8984;cmd</kbd>+<kbd>b</kbd>, type <kbd>s</kbd>, then select the number of the window you want to switch too
- To detach from a `tmux` session but keep the session running: <kbd>Ctrl</kbd>/<kbd>&#8984;cmd</kbd>+<kbd>d</kbd>

{: .note }
> This [`tmux` Cheat Sheet & Quick Reference](https://tmuxcheatsheet.com/){:target="_blank"} is a good resource.

#### Using `xrdp`

Your BXE node comes installed with `xrdp`. However, you'll have to forward the RDP port with SSH tunneling. In this example, we will use:

- SSH Tunnel port: `:9501`
- RDP port: `:3389`

Log into your designated BXE node, tunneling the you desired SSH port to the RDP port.

```shell
$ ssh -L 9501:localhost:3389 -i <LOCAL-PATH-TO-BXE-SSH-PRIVATEKEY> -J <USERNAME>@bxe.lbl.gov <USERNAME>@bxe-xxx
Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 6.14.0-35-generic x86_64)
# ...
<USERNAME>@bxe-xxx:~$ 
```

Open your RDP Viewer of choice and connect to the SSH Tunnel port: `localhost:9501`.

#### Using VNC

Your BXE node comes installed with TigerVNC. However, you'll have to forward the VNC port with SSH tunneling. While you can use any VNC port and any port on the login node, we recommend the following settings. In this example, we will use:

- SSH Tunnel port: `:9501`
- VNC Port: `:1` (TCP Port: `:5901`)

Log into your designated BXE  node, tunneling the you desired SSH port to your VNC port. Once logged in, launch `vncserver` with your desired port.

```shell
$ ssh -L 9501:localhost:5901 -i <LOCAL-PATH-TO-BXE-SSH-PRIVATEKEY> -J <USERNAME>@bxe.lbl.gov <USERNAME>@bxe-xxx
Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 6.14.0-35-generic x86_64)
# ...
<USERNAME>@bxe-xxx:~$ vncserver :1
```

Open your VNC Viewer of choice and connect to the SSH Tunnel port: `localhost:9501`.
