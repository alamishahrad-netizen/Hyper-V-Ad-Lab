# Troubleshooting Log

Problems I ran into while building the lab, what caused them, and how I fixed them.

## 1. VM shows "Start PXE over IPv4" or "Boot image not found"

**Symptom:** After starting a VM, it tried to boot from the network, then showed "Boot image not found" and "The boot loader failed."

**Cause:** No bootable media — either the ISO wasn't attached to the DVD drive, the download hadn't finished, or I missed the short "Press any key to boot from CD or DVD" prompt.

**Fix:**
1. Turned the VM off and confirmed the ISO download had finished.
2. In **Settings > DVD Drive**, selected **Image file** and browsed to the ISO.
3. In **Settings > Firmware**, moved **DVD Drive** to the top of the boot order.
4. Opened the **Connect** window first, then started the VM and tapped the spacebar repeatedly.

**Lesson:** The boot prompt only waits a few seconds. Connecting before starting makes it easier to catch.

## 2. No "Promote this server to a domain controller" flag in Server Manager

**Symptom:** After the AD DS role installed, the yellow notification flag wasn't visible, even after refreshing.

**Cause:** Server Manager doesn't always display the flag right after a role install.

**Fix:** Opened **AD DS** in the left pane and used the **More...** link on the yellow "Configuration required" banner. Confirmed with `Get-ADDomain`.

**Lesson:** Verify server state with a command instead of relying only on the GUI.

## 3. `dcdiag` reports the advertising test failed (error 1355) and "not advertising as a time server"

**Symptom:** `dcdiag /q` reported the advertising test failed with error 1355 and the server wasn't advertising as a time server.

**Cause:** A new domain controller can fail this test when DNS records haven't registered, DNS doesn't point at itself, or the Windows Time service isn't set as a reliable source. Hyper-V time sync can also compete with Windows Time on a VM.

**Fix:**
1. Set the DC's preferred DNS to its own address (`192.168.88.5`).
2. Re-registered DNS records:

```powershell
ipconfig /flushdns
ipconfig /registerdns
net stop netlogon
net start netlogon
nltest /dsregdns
```

3. Configured and restarted the time service:

```powershell
w32tm /config /syncfromflags:manual /manualpeerlist:"time.windows.com pool.ntp.org" /reliable:yes /update
net stop w32time
net start w32time
w32tm /resync
```

4. Restarted the server, waited a few minutes.
5. Checked `w32tm /query /source`, `nslookup lab.local`, and `net share` (NETLOGON/SYSVOL present).
6. Re-ran `dcdiag /test:advertising` and `dcdiag /q` until clean.

**Lesson:** Active Directory depends on DNS and accurate time. Check those two first when `dcdiag` fails.

## 4. Windows 11 setup: "This PC doesn't support Windows 11 — PC must support TPM 2.0"

**Symptom:** Windows 11 Enterprise setup stopped with a TPM 2.0 compatibility error.

**Cause:** Generation 2 Hyper-V VMs support a virtual TPM, but it's off by default.

**Fix:** Turned the VM off, opened **Settings > Security**, checked **Enable Trusted Platform Module** (Secure Boot was already enabled).

**Lesson:** Check the Security tab before installing Windows 11 in any new Gen 2 VM.

## 5. `nslookup lab.local` times out / domain join fails with "lab.local is not valid"

**Symptom:** DNS lookups against `lab.local` timed out, pings to the DC got partial/no replies, and domain join rejected `lab.local` as "not valid."

**Cause:** The DC had been shut down or wasn't fully booted — nothing was there to answer.

**Fix:**
1. Checked the DC's state in Hyper-V Manager and started it.
2. Waited a couple minutes for AD DS/DNS to come up.
3. Re-ran `nslookup lab.local` until clean, then retried the domain join.

**Lesson:** Both VMs need to be running for domain tests. Set the DC's **Automatic Start Action** to always start with the host.

## 6. Domain/Workgroup "Change..." button greyed out after renaming the computer

**Symptom:** After renaming the client, the Domain option was greyed out/read-only.

**Cause:** Windows locks that option until the pending rename is applied with a restart.

**Fix:** Restarted once to apply the rename, then the **Change...** button was active for the domain join (which needed its own restart after).

**Lesson:** A rename and a domain join may need two separate restarts.

## 7. Domain join rejected `LAB\Administrator` as a credential format

**Symptom:** `LAB\Administrator` didn't work in the domain-join credentials prompt.

**Fix:** Used plain `Administrator` with the DC's password instead.

**Lesson:** Try the plain username before assuming the password or domain name is wrong.



## 8. New user created in `ForeignSecurityPrincipals` instead of the intended OU

**Symptom:** A new user account showed up under the built-in `ForeignSecurityPrincipals` container instead of the `LabUsers` OU I'd just created.

**Cause:** The wrong container was selected/highlighted in the left pane when I right-clicked to create the new user.

**Fix:** Deleted the misplaced account, clicked directly on `LabUsers` to select it first, then right-clicked it and chose **New > User** again.

**Lesson:** Always click the target OU first to confirm it's actually selected before right-clicking to create something inside it — especially easy to mis-tap on a touchscreen.
