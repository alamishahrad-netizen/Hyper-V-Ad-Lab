# Lab 5 — Router and Home-Lab Security Audit

**Status:** Planning and preparation documented; audit execution and configuration changes are not yet evidenced in this repository.

## Purpose

Lab 5 is the security-audit phase for the existing Hyper-V environment and MikroTik router. The goal is to understand the current configuration before changing it, then make controlled, reversible improvements and verify their effects.

## Known environment

- Hyper-V home lab with an external virtual switch previously named `lab ext` (the earlier documentation also uses `LAB-External`; verify the live switch name before changing or scripting against it).
- MikroTik router is part of the lab network.
- Existing lab virtual machines include `LAB-DC01`, `LAB-CLIENT01`, and `LAB-LINUX01`.

## Preparation completed

The project scope and proposed audit workflow were discussed. Read-only RouterOS inventory commands were identified for collecting system, interface, address, route, firewall, NAT, and logging information.

No returned router configuration, audit findings, firewall modifications, VLAN/segmentation implementation, endpoint scan results, or signal-shielding experiment results are currently recorded as evidence in the retrieved project history. This document therefore does not claim those tasks are complete.

## Read-only MikroTik inventory

Run these commands from the RouterOS terminal and save sanitized output for the lab record:

```routeros
/system/resource/print
/system/routerboard/print
/system/package/print
/interface/print
/ip/address/print
/ip/route/print
/ip/firewall/filter/print
/ip/firewall/nat/print
/system/logging/print
```

Review output for sensitive information before publishing it. Never commit passwords, keys, private configuration exports, public IPs, or other secrets.

## Audit work to record when performed

1. Record router model, RouterOS version, interface names, bridge membership, addressing, routes, DHCP, NAT, and firewall policy.
2. Record the Hyper-V host's active virtual switches and the network adapter each switch uses.
3. Inventory Windows and Linux endpoint protection, listening services, patch status, and host firewall posture.
4. Map the intended network zones and traffic flows before creating VLANs, subnets, or isolation rules.
5. Back up the router configuration and preserve a recovery path before making changes.
6. Apply one change at a time, then test management access, DNS, domain authentication, internet access, and VM-to-VM connectivity as appropriate.
7. Record before/after configuration, test commands, results, timestamps, and rollback steps.

## Current status

| Work item | Status |
|---|---|
| Define router/home-lab security audit scope | Prepared |
| Identify read-only RouterOS inventory commands | Prepared; output not yet recorded |
| Capture router baseline and topology | Pending |
| Review host and endpoint security posture | Pending |
| Implement and verify firewall hardening | Pending |
| Implement and verify network segmentation/isolation | Pending |
| Test controlled signal-shielding experiment | Not documented; separate controlled test required |
| Preserve and verify router/VM backups | Pending |

## Safety and change control

Do not apply firewall, VLAN, routing, or isolation changes from an unverified diagram. Keep a local recovery path and a known-good configuration backup. Avoid locking yourself out of router management. Test changes in small increments and record rollback instructions.

## Scope boundary

This Lab 5 record covers router and home-lab security audit preparation. Integrated three-VM connectivity is documented separately in [Lab 6](06-lab6-integrated-verification.md). HUMAN SHIELD, NEURO SHIELD, and EEDI implementation are reserved for a separate Lab 7 and are not part of this audit closeout.
