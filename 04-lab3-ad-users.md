# Lab 3: Active Directory Users and Groups

**Goal:** Create an Organizational Unit and test user accounts on `LAB-DC01`, then prove a standard domain account (not the Administrator) can log into `LAB-CLIENT01`.

## Steps

### 1. Create the Organizational Unit
On `LAB-DC01`, opened **Active Directory Users and Computers**, right-clicked `lab.local` → **New > Organizational Unit**, named it `LabUsers`.

### 2. Create test user accounts
Inside `LabUsers`, created two accounts:

| Full name | Logon name |
|---|---|
| John Smith | `jsmith` |
| Maria Jones | `mjones` |

For each: set a password, unchecked **User must change password at next logon**, and checked **Password never expires** to simplify the original lab test. This is a lab-only convenience, not a recommended production setting. For a realistic security baseline, use a suitable password policy, require rotation only where policy calls for it, and avoid permanent passwords; disable or delete unused test accounts.

### 3. Verify domain login from the client
On `LAB-CLIENT01`, signed out of the Administrator session, clicked **Other user**, and signed in as `LAB\jsmith` with the password set above.

The account authenticated successfully and Windows built a fresh local profile for it on first login — confirming the client is validating credentials against `LAB-DC01` over the domain, not using any local account.

## Result

Both accounts exist in `LabUsers` under `lab.local`, and `jsmith` successfully authenticated on `LAB-CLIENT01` — proof that Active Directory is handling identity across the domain, not just on the server itself.

## Notes

- The test accounts are demonstration identities only. Do not publish their passwords or reuse credentials from personal or production accounts.
- The original test-user configuration allowed passwords never to expire for convenience. Review and harden this before using the lab as a production-like security demonstration.
- Account names are intentionally generic (`jsmith`, `mjones`) rather than themed, to keep the documentation professional for a portfolio audience.
- Accidentally created the first account while `ForeignSecurityPrincipals` was selected instead of `LabUsers` — deleted it and recreated it with `LabUsers` properly selected first. See [troubleshooting log](docs/02-troubleshooting.md).
