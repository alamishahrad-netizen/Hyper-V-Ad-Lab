# Lab 4: Rocky Linux Server VM

**Goal:** Stand up a Linux VM in the lab, running Rocky Linux — a free, RHEL-compatible distro widely used in enterprise and government environments — and confirm it's a working node on the network.

## Why Rocky Linux

RHEL itself is the dominant Linux distro in enterprise and government IT, but requires a paid license. Rocky Linux is a free, 1:1 binary-compatible rebuild of RHEL, built specifically so people can learn and run RHEL-identical environments at no cost. Skills transfer directly to real RHEL in a production job.

## Environment

| Item | Value |
|---|---|
| VM name | `LAB-LINUX01` |
| Generation | 2 |
| Memory | 2048 MB |
| Virtual disk | `LAB-LINUX01.vhdx`, 40 GB |
| Guest OS | Rocky Linux 10.2 (Minimal install) |
| Virtual switch | `LAB-External` |
| Hostname | `lab-linux` |

## Steps

### 1. Download the ISO
Downloaded the **Minimal ISO** (x86_64) from the official Rocky Linux site — not the DVD/Everything image (bundles a full desktop environment not needed for a server) and not a Cloud Image (built for cloud platforms, not local Hyper-V installs).

### 2. Create the VM
In Hyper-V Manager, **New > Virtual Machine** with the settings in the table above. Chose **Install an operating system later**, same pattern as the earlier VMs.

### 3. Set Secure Boot for Linux
Linux requires a different Secure Boot template than Windows. In **Settings > Security**, left **Enable Secure Boot** checked but changed the template from "Microsoft Windows" to **"Microsoft UEFI Certificate Authority."**

### 4. Attach the ISO and fix the boot order
Same pattern as the earlier VMs: **Settings > SCSI Controller > DVD Drive > Add > Image file**, then **Settings > Firmware**, moved **DVD Drive** to the top of the boot order.

### 5. Boot and install
1. Connected to the VM before starting it, started it, landed on the GRUB boot menu (Rocky's installer menu, different from Windows' "press any key" prompt).
2. Selected **Install Rocky Linux 10.2** and let it load the graphical installer (Anaconda).
3. On the Installation Summary screen, configured:
   - **Time & Date:** Central Time
   - **Software Selection:** Minimal Install (default for the Minimal ISO)
   - **Installation Destination:** the 40 GB virtual disk, automatic partitioning
   - **Network & Host Name:** enabled networking, set hostname to `lab-linux`
   - **Root Password:** set, root account left unlocked
   - **User Creation:** created a standard admin user with sudo rights
4. Clicked **Begin Installation**.

### 6. Reboot and remove the ISO
After the install finished, removed the ISO from **Settings > DVD Drive** (set to None) before rebooting, so it wouldn't boot back into the installer.

### 7. First login
First boot dropped to a plain text `lab-linux login:` prompt — expected for a minimal install with no desktop environment. Logged in with the created user account.

### 8.
