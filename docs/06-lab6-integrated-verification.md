# Lab 6 — Integrated Lab Verification and Closeout

**Date:** 2026-11-26  
**Repository:** [Hyper-V-Ad-Lab](https://github.com/alamishahrad-netizen/Hyper-V-Ad-Lab)

## Scope

Lab 6 brings the existing Windows Server domain controller, Windows client, and Linux VM together for an integrated operational check. This document records only results confirmed during the Lab 6 work; it does not imply that every planned security-hardening or isolation task is complete.

## VM inventory

The operator confirmed that all three virtual machines were running in Hyper-V during this verification.

| VM | Role | Address recorded during Lab 6 |
|---|---|---|
| `LAB-DC01` | Windows Server; Active Directory Domain Services and DNS for `lab.local` | `192.168.20.5` |
| `LAB-CLIENT01` | Windows 11 domain client | `192.168.88.249` |
| `LAB-LINUX01` | Linux lab workstation | `192.168.20.199` |

The gateway used in Linux connectivity checks was `192.168.20.1`. These are point-in-time observations, not proof of static assignments. The client and domain controller are recorded on different IPv4 subnets. The exact active Hyper-V switch name, adapter mapping, subnet masks, routes, DNS settings, and router interfaces still need to be confirmed from the live systems.

## Verified results

### Domain controller — `LAB-DC01`

- IPv4 address reported as `192.168.20.5`.
- The operator confirmed the DNS, NTDS, and Netlogon services showed Running.

### Windows client — `LAB-CLIENT01`

- Windows client was installed and joined to the `lab.local` domain.
- The operator successfully signed in using the domain account `LAB\\shahrad`.
- A ping to `192.168.20.5` succeeded.
- `nltest /dsgetdc:lab.local` succeeded and located the domain controller.
- Hyper-V Enhanced Session initially caused a Remote Desktop Services sign-in-rights message. Disabling Enhanced Session allowed sign-in through the basic Hyper-V console. This was a console-session issue, not evidence that the domain account needed Remote Desktop permissions.

### Linux VM — `LAB-LINUX01`

- Linux interface `eth0` reported IPv4 address `192.168.20.199`.
- Default gateway reported as `192.168.20.1`.
- Ping tests to `192.168.20.5` (domain controller) and `192.168.20.1` (gateway) both succeeded.

These are point-in-time test results. Repeat the checks after network, firewall, VM, or router changes.

## Suggested repeatable verification commands

### Windows client

Run in Command Prompt or PowerShell:

```powershell
ipconfig /all
ping 192.168.20.5
nltest /dsgetdc:lab.local
gpupdate /force
```

### Linux VM

Run in a terminal:

```bash
ip -br address
ip route
ping -c 4 192.168.20.5
ping -c 4 192.168.20.1
```

### Domain controller

Run in elevated PowerShell:

```powershell
ipconfig /all
dcdiag
Get-Service NTDS,DNS,Netlogon
```

## Items intentionally not marked complete

- Confirm the live IP address, prefix, gateway, DNS settings, and virtual switch for each VM; in particular, resolve and document the cross-subnet path between the client (`192.168.88.249`) and domain controller (`192.168.20.5`).
- Verify the current Hyper-V checkpoint list and create/confirm a clean recovery checkpoint for each VM where appropriate.
- Verify that VM exports or other backups exist, are stored safely, and can be identified. A running VM is not proof of a backup.
- Capture current screenshots of Hyper-V Manager, each guest's network configuration, successful domain discovery, and Linux connectivity for portfolio evidence.
- Confirm Group Policy processing with `gpupdate /force` and record the result.
- Network isolation and further security hardening remain separate tasks unless explicitly tested and documented.

## Security and publication hygiene

Never commit passwords, recovery keys, license keys, private keys, VPN profiles, router exports containing credentials, or other secrets. Sanitize screenshots and command output before adding them to this public repository.

## Lab 6 closeout status

**Operational checks:** Core three-VM checks completed; selected connectivity and domain discovery confirmed.  
**Documentation:** Updated with the latest confirmed Lab 6 results.  
**Backup/checkpoint preservation:** Pending explicit verification.  
**Network topology and Group Policy evidence:** Partially verified; further live details pending.  
**Final closeout:** Keep open until topology and backup/checkpoint checks are completed.

## Next phase boundary

HUMAN SHIELD, NEURO SHIELD, and EEDI implementation are not part of this Lab 6 closeout. They remain reserved for a separately scoped Lab 7.
