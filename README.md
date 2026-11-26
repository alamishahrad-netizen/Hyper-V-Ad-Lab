# Hyper-V Active Directory Home Lab

A hands-on Hyper-V lab built to practice Windows Server administration, Active Directory, DNS, Windows domain clients, Linux administration, network troubleshooting, and defensive security fundamentals.

The lab documentation is organized by build phase. Historical build settings are preserved where useful; current network observations and outstanding verification are called out separately so the repository does not imply that old IP addresses or unverified controls are still active.

## Current status

| Component | Status |
|---|---|
| `LAB-DC01` — Windows Server 2022 / AD DS / DNS | Built; confirmed running during Lab 6 |
| `lab.local` domain | Created; earlier `dcdiag` pass documented; rerun for current health |
| Active Directory OUs, users, and groups | Created and documented in Lab 3 |
| `LAB-CLIENT01` — Windows 11 Enterprise Evaluation | Domain-joined; confirmed running during Lab 6 |
| `LAB-LINUX01` — Rocky Linux 10.2 Minimal | Installed; confirmed running during Lab 6 |
| Lab 5 router/security audit | Scope and read-only inventory prepared; audit findings and configuration changes not yet evidenced |
| Lab 6 integrated verification | Selected three-VM connectivity checks recorded; topology, backup, and checkpoint verification remain open |
| Group Policy | Planned |
| Network segmentation / isolation | Planned; do not treat as implemented until verified |

## Architecture and addressing notes

The lab uses Hyper-V and a MikroTik router. The original build guides describe the initial network configuration, including the original `192.168.88.0/24` addressing. Later Lab 6 notes record:

| System | Address recorded during Lab 6 |
|---|---|
| `LAB-DC01` | `192.168.20.5` |
| `LAB-CLIENT01` | `192.168.88.249` |
| `LAB-LINUX01` | `192.168.20.199` (confirm live assignment) |
| Gateway used in Linux/DC connectivity checks | `192.168.20.1` |

These are point-in-time observations, not a complete verified network diagram. The client and domain controller are recorded on different IPv4 subnets. The exact active Hyper-V switch name, adapter mapping, subnet masks, routes, DNS settings, and router interfaces still need to be confirmed from the live systems. Earlier notes use both `LAB-External` and `lab ext`; verify the actual switch name before applying changes or running scripts.

See [Lab 5](docs/06-lab5-security-audit.md) and [Lab 6](docs/06-lab6-integrated-verification.md) for current audit and verification notes.

## Documentation

- [Lab 1 — Windows Server / domain controller build](docs/01-build-steps.md)
- [Troubleshooting log](docs/02-troubleshooting.md)
- [Lab 2 — Windows 11 client and domain join](docs/03-lab2-client-vm.md)
- [Lab 3 — Active Directory users and groups](04-lab3-ad-users.md)
- [Lab 4 — Rocky Linux VM](docs/05-lab4-linux-vm.md)
- [Lab 5 — Router and home-lab security audit](docs/06-lab5-security-audit.md)
- [Lab 6 — Integrated verification and closeout](docs/06-lab6-integrated-verification.md)

## Environment

| Item | Value |
|---|---|
| Host OS | To be confirmed |
| Hypervisor | Microsoft Hyper-V |
| Host CPU / RAM | To be confirmed |
| Domain controller VM | `LAB-DC01` |
| Domain controller guest OS | Windows Server 2022 Standard Evaluation (Desktop Experience) |
| Domain | `lab.local` (NetBIOS name `LAB`) |
| Domain controller address | `192.168.20.5` recorded during Lab 6; verify live |
| Linux VM | `LAB-LINUX01`, Rocky Linux 10.2 Minimal |
| Windows client VM | `LAB-CLIENT01`, Windows 11 Enterprise Evaluation |
| Virtual switch | External switch; exact current name to be verified |

## Router administration lesson

During the build, access to the MikroTik admin account was lost after a password change. The router log showed failed attempts from the user's own PC. The router was hardware-reset, reconfigured, renamed `LAB-RTR01`, and a configuration backup was made. The lesson was to store credentials in a password manager immediately and preserve a known-good configuration backup.

Do not publish router exports, credentials, recovery keys, license keys, private keys, or unredacted screenshots.

## Skills practiced

- Hyper-V virtual machines, virtual switches, virtual disks, ISO attachment, firmware boot order, and checkpoints
- Windows Server 2022 installation and configuration
- Active Directory Domain Services and DNS
- Domain joining and standard domain-user authentication
- Active Directory organizational units, users, and groups
- Rocky Linux installation and basic network verification
- Troubleshooting with tools such as `dcdiag`, `nslookup`, `nltest`, `w32tm`, `ipconfig`, and Linux network utilities

## Operational safeguards

- Verify current DNS, time synchronization, addressing, routes, and domain-controller health after network changes.
- Preserve recovery checkpoints and tested backups; a running VM is not proof of a backup.
- Make router and firewall changes incrementally, with a recovery path.
- Keep secrets and sensitive configuration out of this public repository.

## Project boundary

Lab 6 closeout is limited to verifying the existing lab, correcting documentation, and preserving recovery points. HUMAN SHIELD, NEURO SHIELD, and EEDI are reserved for a separate Lab 7 after the planned break; they are not implemented by this repository update.
