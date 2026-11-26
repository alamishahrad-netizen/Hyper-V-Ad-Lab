# Repository Audit — Documentation and Evidence Review

**Review date:** 2026-11-26  
**Branch reviewed:** `main`

## Review scope

This review inspected the repository tree and the Markdown files present on `main`. It checked the lab index, document organization, internal Markdown links, consistency of the recorded network information, completion claims, and whether referenced scripts or supporting evidence were present.

This is a repository-content review. It is not a live audit of the Hyper-V host, guest operating systems, MikroTik router, backup storage, or checkpoint state.

## Repository structure observed

| Path | Purpose |
|---|---|
| `README.md` | Project overview, current status, environment notes, and document index |
| `docs/01-build-steps.md` | Lab 1 — Windows Server and domain-controller build |
| `docs/02-troubleshooting.md` | Troubleshooting history |
| `docs/03-lab2-client-vm.md` | Lab 2 — Windows client and domain join |
| `04-lab3-ad-users.md` | Lab 3 — Active Directory users and groups |
| `docs/05-lab4-linux-vm.md` | Lab 4 — Rocky Linux VM |
| `docs/06-lab5-security-audit.md` | Lab 5 — Router and home-lab security audit |
| `docs/06-lab6-integrated-verification.md` | Lab 6 — Integrated three-VM verification |

The repository tree reviewed contained these Markdown files and no `scripts/` directory. A prior README reference to `scripts/verify-dc.ps1` was therefore removed rather than left as a dead link.

## Consistency and quality work completed

- Rebuilt the README as the central index for the six documented lab phases.
- Marked original Lab 1 and Lab 2 addressing as historical where later Lab 6 observations differ.
- Recorded the three VM addresses as point-in-time Lab 6 observations, not guaranteed current/static assignments.
- Documented that the Windows client and domain controller are recorded on different IPv4 subnets; routing and virtual-switch mapping still require live verification.
- Added Lab 5 audit preparation and a clear table of work that remains pending.
- Added Lab 6 integrated verification, with tested items separated from suggested commands and outstanding tasks.
- Added the Linux network checks to the Lab 4 record.
- Added a security caveat to Lab 3: permanent-password settings were used for convenience in the test environment and should not be presented as a production recommendation.
- Corrected document links and removed a misplaced duplicate Lab 5 document.
- Reviewed the local Markdown links against the repository file tree.

## Important technical observations

### Network addressing

The repository preserves these values from the Lab 6 notes:

| System | Recorded address |
|---|---|
| `LAB-DC01` | `192.168.20.5` |
| `LAB-CLIENT01` | `192.168.88.249` |
| `LAB-LINUX01` | `192.168.20.199` |
| Gateway used in Linux/DC checks | `192.168.20.1` |

The actual subnet masks, current IP assignments, DNS configuration, router interfaces, routes, and Hyper-V switch-to-adapter mapping have not been independently read from the running machines during this repository review. Earlier documentation uses both `LAB-External` and `lab ext`; verify the live switch name.

### Security posture

The repository currently documents the build and selected connectivity checks. It does not yet contain sufficient evidence to claim:
- A completed router firewall audit or hardened RouterOS configuration.
- Implemented VLANs or network isolation.
- A completed endpoint vulnerability scan or baseline.
- A tested backup/restore process for each VM.
- Verified current Hyper-V checkpoints for all three VMs.
- A current, clean `dcdiag` result from the live domain controller.

## Remaining hands-on verification

1. On the Hyper-V host, record the exact switch names, switch types, bound physical adapters, and each VM's virtual NIC attachment.
2. On each guest, record `ipconfig /all` (Windows) or `ip -br address` and `ip route` (Linux), plus DNS settings.
3. On `LAB-DC01`, run `dcdiag /v`, check AD DS and DNS services, and verify DNS records and time synchronization.
4. On `LAB-CLIENT01`, verify domain discovery, DNS resolution, domain authentication, and Group Policy processing.
5. On `LAB-LINUX01`, repeat the documented connectivity tests and capture the output.
6. In Hyper-V Manager, inspect checkpoints for each VM and document names/dates. Create a recovery point only after confirming the VM is in a safe state.
7. Confirm VM exports or backups exist on storage separate from the live VM files, and verify that the backup can be located and restored.
8. Capture sanitized screenshots and test output for portfolio evidence.
9. Perform the Lab 5 RouterOS baseline audit only after preserving a known-good router configuration and recovery path.

## Phase boundary

Lab 6 remains in closeout until live topology, checkpoints, and backups are verified. HUMAN SHIELD, NEURO SHIELD, and EEDI are intentionally excluded from this work and remain reserved for the separately scoped Lab 7 after the planned break.
