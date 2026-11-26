# Lab 6 — Integrated Lab Verification and Closeout

**Date:** 2026-11-26  
**Repository:** [Hyper-V-Ad-Lab](https://github.com/alamishahrad-netizen/Hyper-V-Ad-Lab)

## Scope

Lab 6 brings the existing Windows Server domain controller, Windows client, and Linux VM together for an integrated operational check. This document records only results confirmed during the Lab 6 work; it does not imply that every planned security-hardening or isolation task is complete.

## VM inventory

The operator confirmed that all three virtual machines were running in Hyper-V at the time of closeout.

| VM | Role | Address recorded during Lab 6 |
|---|---|---|
| `LAB-DC01` | Windows Server; Active Directory Domain Services and DNS for `lab.local` | `192.168.20.5` |
| `LAB-CLIENT01` | Windows 11 domain client | `192.168.88.249` |
| `LAB-LINUX01` | Linux lab workstation | `192.168.20.199` (reported during lab; confirm on guest before treating as a permanent/static assignment) |

The Lab 6 notes also record the MikroTik/lab gateway as `192.168.20.1`. The client and server addresses are on different IPv4 subnets in the recorded configuration. Do not describe them as one flat subnet; document the actual routing, interfaces, and virtual-switch mapping separately after checking the live configuration.

## Verified results

- All three VMs were confirmed running by the operator.
- Connectivity from the Windows client to the domain controller was tested successfully.
- From `LAB-LINUX01`, connectivity tests to the domain controller and the router were successful.
- The Linux network interface was reported as up.

These are point-in-time test results. Repeat the checks after network, firewall, VM, or router changes. The commands below are a recommended repeatable verification set; do not treat them as completed results until run and recorded.

## Suggested repeatable verification commands

### Windows client

Run in Command Prompt:

```bat
ipconfig /all
ping 192.168.20.5
nltest /dsgetdc:lab.local
gpupdate /force
```

### Linux VM

Run in a terminal (replace addresses if the live configuration has changed):

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
Get-Service NTDS,DNS
```

## Items intentionally not marked complete

- Confirm the live IP address, prefix, gateway, DNS settings, and virtual switch for each VM; in particular, resolve and document the cross-subnet path between the client (`192.168.88.249`) and domain controller (`192.168.20.5`).
- Verify the current Hyper-V checkpoint list and create/confirm a clean recovery checkpoint for each VM where appropriate.
- Verify that VM exports or other backups exist, are stored safely, and can be identified. A running VM is not proof of a backup.
- Capture current screenshots of Hyper-V Manager, each guest's network configuration, successful domain discovery, and Linux connectivity for the portfolio.
- Network isolation and further security hardening remain separate tasks unless explicitly tested and documented.

## Security and publication hygiene

Never commit passwords, recovery keys, license keys, private keys, VPN profiles, router exports containing credentials, or other secrets. Sanitize screenshots and command output before adding them to this public repository.

## Lab 6 closeout status

**Operational checks:** All three VMs running and selected connectivity checks confirmed.  
**Documentation:** This Lab 6 record added to the repository.  
**Backup/checkpoint preservation:** Pending explicit verification.  
**Final closeout:** Do not mark fully closed until the network mapping and backup/checkpoint checks above are completed.

## Next phase boundary

HUMAN SHIELD, NEURO SHIELD, and EEDI implementation are not part of this Lab 6 closeout. They remain reserved for a separately scoped Lab 7 after the planned break.
