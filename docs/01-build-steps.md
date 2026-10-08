# Build Steps

How I built `LAB-DC01`, a Windows Server 2022 domain controller for `lab.local`, in Hyper-V.

## What you need

- A Windows PC with Hyper-V enabled
- The Windows Server 2022 Evaluation ISO (free 180-day trial, from the Microsoft Evaluation Center)

## 1. Create the virtual switch

In Hyper-V Manager, open **Virtual Switch Manager** and create an **External** switch named `LAB-External`, bound to the host's network adapter. This puts VMs on the same network as the host PC.

## 2. Create the virtual machine

Run **New > Virtual Machine** in Hyper-V Manager:

| Setting | Value |
|---|---|
| Name | `LAB-DC01` |
| Network connection | `LAB-External` |
| Virtual hard disk | `LAB-DC01.vhdx`, 80 GB (lowered from the 127 GB default, since it is dynamically expanding) |
| Installation options | Install an operating system later |

## 3. Attach the ISO and set the boot order

1. Right-click `LAB-DC01`, choose **Settings**, select **SCSI Controller**.
2. Select **DVD Drive**, click **Add**, choose **Image file**, browse to the Windows Server ISO.
3. Select **Firmware** and move **DVD Drive** to the top of the boot order.
4. Click **OK**.

## 4. Boot the VM and install Windows Server

1. Right-click `LAB-DC01` and choose **Connect** first, while the VM is still off.
2. Click into the window, start the VM, and tap the spacebar repeatedly to get past **"Press any key to boot from CD or DVD"**. See the [troubleshooting log](02-troubleshooting.md) if "Boot image not found" appears.
3. Pick **Windows Server 2022 Standard Evaluation (Desktop Experience)**.
4. Choose **Custom: Install Microsoft Server Operating System only** and select the unallocated 80 GB disk.
5. Let it install and restart. Don't press a key at the "Press any key" prompt after the restart.
6. Set the built-in Administrator password and store it in a password manager.

## 5. Rename the server

In **Server Manager > Local Server**, change the computer name to `LAB-DC01` and restart.

## 6. Set a static IP

1. Run `ipconfig` and note the current address, subnet mask, and gateway.
2. Check the target address is free: `ping 192.168.88.5` should return "Destination host unreachable" or time out.
3. Run `ncpa.cpl`, open **Ethernet > Properties > Internet Protocol Version 4 (TCP/IPv4)** and set:

| Setting | Value |
|---|---|
| IP address | `192.168.88.5` |
| Subnet mask | `255.255.255.0` |
| Default gateway | `192.168.88.1` |
| Preferred DNS | `192.168.88.1` for now (changed to `192.168.88.5` after promotion) |

4. Confirm with `ipconfig` and `ping google.com`.

## 7. Install Active Directory Domain Services

1. In Server Manager, choose **Manage > Add Roles and Features**.
2. Select **Role-based or feature-based installation** and `LAB-DC01`.
3. Check **Active Directory Domain Services** and accept the additional features.
4. Click **Install**.

## 8. Promote the server to a domain controller

1. Click **Promote this server to a domain controller** (or open **AD DS** in Server Manager and click **More...** on the yellow banner).
2. Choose **Add a new forest** with the root domain name `lab.local`.
3. Leave the functional levels at their defaults and keep **DNS server** and **Global Catalog** checked.
4. Set a Directory Services Restore Mode (DSRM) password and store it in a password manager.
5. Accept the defaults on the remaining pages (ignore the DNS delegation warning) and click **Install**.
6. After the restart, sign in as `LAB\Administrator`.

## 9. Point DNS at the DC itself

Change **Preferred DNS server** to `192.168.88.5` in the adapter's IPv4 properties.

## 10. Verify the domain controller

```powershell
Get-ADDomain
nslookup lab.local
dcdiag /q
```

`Get-ADDomain` should show `lab.local` as the DNS root and `LAB` as the NetBIOS name. `nslookup` should return `192.168.88.5`. `dcdiag /q` prints nothing when there are no errors.

## 11. Take a checkpoint

In Hyper-V Manager, right-click `LAB-DC01`, choose **Checkpoint**, and rename it `DC-clean-build`. If a later change breaks something, apply this checkpoint to roll back.
