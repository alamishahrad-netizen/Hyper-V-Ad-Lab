# Lab 2: Windows 11 Enterprise Client — Join the Domain

**Goal:** Build a Windows 11 Enterprise client VM and join it to `lab.local`, proving the domain controller from Lab 1 can actually authenticate a second machine.

## Environment

| Item | Value |
|---|---|
| VM name | `LAB-CLIENT01` |
| Generation | 2 |
| Memory | 4096 MB |
| Virtual disk | `LAB-CLIENT01.vhdx`, 64 GB |
| Guest OS | Windows 11 Enterprise (Evaluation) |
| Virtual switch | `LAB-External` |
| Domain joined | `lab.local` |
| Computer name | `LAB-CLIENT01` |

## Steps

### 1. Download the ISO
Downloaded the Windows 11 Enterprise (not LTSC) evaluation ISO from the Microsoft Evaluation Center, 64-bit, English.

> LTSC is a stripped-down build meant for locked-down devices like ATMs — it skips the Store and most default apps and gets far fewer feature updates. Regular Enterprise matches a normal production desktop, which is what this lab is meant to reflect.

### 2. Create the VM
In Hyper-V Manager, **New > Virtual Machine**, using the settings in the table above. Chose **Install an operating system later** so the ISO could be attached the same way as `LAB-DC01`.

### 3. Attach the ISO and fix the boot order
**Settings > SCSI Controller > DVD Drive > Add > Image file**, browsed to the Windows 11 ISO. Then **Settings > Firmware**, moved **DVD Drive** to the top of the boot order, above the network adapter.

### 4. Enable TPM and Secure Boot
Windows 11 setup refused to continue with **"This PC doesn't support Windows 11 — PC must support TPM 2.0."** Fixed by turning the VM off, opening **Settings > Security**, and checking:
- **Enable Trusted Platform Module**
- **Enable Secure Boot** (template: Microsoft Windows)

Generation 2 VMs support a virtual TPM; it's just off by default.

### 5. Install Windows 11
Connected to the VM before starting it (so the brief "press any key" boot prompt couldn't be missed), tapped the spacebar quickly on start, and ran through setup:
- **Custom: Install Windows only (advanced)**, selected the single unallocated 64 GB disk.
- At account setup, bypassed the forced Microsoft account using the local-account path (**"who is going to use this device"**), creating a local username and password instead.

### 6. Set DNS
Inside the client VM: `ncpa.cpl` → Ethernet → Properties → IPv4 Properties → left the IP on DHCP, set **Preferred DNS server** to `192.168.88.5` (the DC). Verified with:


### 7. Rename the computer
`System > Rename this PC (advanced) > Rename...`, set the computer name to `LAB-CLIENT01` to match the VM name, and chose **Restart Later** since a domain join was coming next anyway.

> The **Domain/Workgroup "Change..."** option was greyed out until the rename was actually applied — had to restart once for the rename, then come back for the domain join as a second step rather than combining them.

### 8. Join the domain
After the restart, `System > Rename this PC (advanced) > Change...`, selected **Domain**, typed `lab.local`. Entered the credential as just `Administrator` with the DC's Administrator password (rather than the `LAB\Administrator` format) to get past an initial **"lab.local is not valid"** error — see [troubleshooting log](02-troubleshooting.md) for the full cause.

Got the **"Welcome to the lab.local domain"** message, then restarted to apply the join.

### 9. Verify
- Signed in at the client as `LAB\Administrator` via **Other user**.
- On `LAB-DC01`, opened **Active Directory Users and Computers**, expanded `lab.local > Computers`, and confirmed `LAB-CLIENT01` was listed.

That computer object appearing in AD is the proof the domain join worked end-to-end.

## Result

`LAB-CLIENT01` is domain-joined to `lab.local` and authenticates against `LAB-DC01`. Lab 2 complete.

## Next

- Create an `LabUsers` OU and real test user accounts on `LAB-DC01`, and sign into `LAB-CLIENT01` as a standard user (not the domain Administrator) to prove normal account authentication.
- Lab 3: stand up a Linux VM.
  
