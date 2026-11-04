# Troubleshooting Log

Every error I hit across the labs: what I saw, why it happened, how I fixed it. Format: **Symptom, Cause, Fix, Verify.**

## Windows / DHCP / DNS

| ID | Symptom | Cause | Fix | Verify |
|----|---------|-------|-----|--------|
| W1 | `ipconfig /release` > "The operation failed as no adapter is in the state permissible for this operation." | Adapter had a **static** IP (`192.168.10.50`); there was no DHCP lease to release | Set adapter to "Obtain an IP address automatically" | `ipconfig /release` and `/renew` work |
| W2 | `ip config /release` > "'ip' is not recognized as an internal or external command" | Typed two words. The Windows command is `ipconfig` | Run `ipconfig /release` | Command runs |
| W3 | After release, no IPv4 address shown | Expected: lease released | `ipconfig /renew` | Gets `192.168.10.100`, suffix `lab.local` |
| W4 | `nslookup fileserver.lab.local` > "DNS request timed out", "Server: UnKnown" but the name still resolves | nslookup does a reverse lookup of its DNS server first; no PTR / reverse zone exists for `192.168.10.10` | Not required for resolution. Optional: add a reverse lookup zone + PTR record | Forward lookup returns `192.168.10.10`; `ping 192.168.10.10` 0% loss |
| W5 | Client cannot resolve internal names | DNS server set to `8.8.8.8` (simulated fault); public DNS does not know `lab.local` | Set DNS to the domain controller `192.168.10.10` | `ipconfig /all` shows correct DNS; `nslookup` resolves |
| W6 | Need to confirm the client really got its lease from the server | n/a | DHCP console > Scope > Address Leases | `192.168.10.100` listed for `DOO2.lab.local` |
| W7 | User must set a new password | Admin reset | ADUC > Reset Password, tick "User must change password at next logon" | User prompted at next logon |
| W8 | Server Manager "Local Server" tile red with 1 event | Event logged on the server | Opened Events from the tile to review it | n/a |
| W9 | Share reachable but folder looks empty | Folder simply had no files yet | Confirm via `\\D001` that the share exists and opens | Folder opens, 0 items |

## <a name="linux"></a>Linux

| ID | Symptom | Cause | Fix | Verify |
|----|---------|-------|-----|--------|
| L1 | `apt install openssh-server` > "Could not resolve 'archive.ubuntu.com'", "Failed to fetch ...", "Unable to fetch some archives" | Laptop had no working internet / DNS at that moment | Restore network connectivity, then `sudo apt update` and re-run the install (the error itself suggests `apt-get update` or `--fix-missing`) | SSH server installed and listening on port 22 |
| L2 | SSH first connect: typed `y` > "Please type 'yes', 'no' or the fingerprint" | SSH requires the full word `yes` | Type `yes` | "Permanently added ... to the list of known hosts" |
| L3 | `hotname` > "Command 'hotname' not found, did you mean 'hostname'" | Typo | Use `hostname` (read the suggestion in the error) | Hostname printed |
| L4 | `su -outsider` > "invalid option -- 'o'" | No space after `-`; `su` read `-o` as an option | `su - outsider` | Password prompt, shell as `outsider` |
| L5 | `su-outsider` > "command not found" | Missing space; shell looked for a command named `su-outsider` | `su - outsider` | Works |
| L6 | `su -testuser` > "invalid option -- 't'" | Same as L4 | `su - testuser` (or `su testuser`) | Works |
| L7 | `su-testuser` > "command not found" | Same as L5 | `su - testuser` | Works |
| L8 | After `su testuser`, `ls` > "cannot open directory '.': Permission denied" | `su` without `-` keeps the old working directory (`/home/lavi`), which is `drwxr-x---` owned by `lavi` | Expected behaviour. Use `su - testuser` to land in testuser's own home | `pwd` shows the right home |
| L9 | `touch srv/helpdesk/oel.txt` > "No such file or directory" | Missing leading `/` (relative path) and a filename typo | `touch /srv/helpdesk/owl.txt` | File listed in `/srv/helpdesk` |
| L10 | `ls helpdesk` > "No such file or directory" | Ran from home directory using a relative path | `ls /srv/helpdesk` | Contents listed |
| L11 | `sudo chown root:hrlpdesk /srv/helpdesk` > "invalid group: 'hrlpdesk'" | Group name typo | `sudo chown root:helpdesk /srv/helpdesk` | `ls -ld` shows `root helpdesk` |
| L12 | `sudo chmod 770 /srv.helpdesk` > "No such file or directory" | Typed `.` instead of `/` | `sudo chmod 770 /srv/helpdesk` | `drwxrwx---` |
| L13 | `sudo chmod 770 /srv/helpdek` > "No such file or directory" | Folder name typo | `sudo chmod 770 /srv/helpdesk` | `drwxrwx---` |
| L14 | `cd users` > "No such file or directory" | `users` is a group name, not a folder | Use `groups <username>` to see group membership | Group list shown |
| L15 | `groups helpdesk` > "'helpdesk': no such user" | `groups` expects a **username**, `helpdesk` is a group | `groups testuser`, or `getent group helpdesk` to inspect the group | Membership shown |
| L16 | `testuser` lost access to the share after `usermod -rG helpdesk testuser` | Removed from the group on purpose to test | `sudo usermod -aG helpdesk testuser` (`-a` appends; without it other groups are replaced) | `groups testuser` shows `helpdesk` again |
| L17 | `ls /srv/helpdesk` as `outsider` > "Permission denied" | Correct behaviour: `770` gives no access to others | None needed. This is the passing result of the access test | Confirms permissions work |
| L18 | `journalctl -u ssh n -20` > "invalid option -- '2'" | Missing `-` before `n`; `-20` parsed as options | `journalctl -u ssh -n 20` | Last 20 log lines shown |

## Microsoft 365 / Cloud

| ID | Symptom | Cause | Fix |
|----|---------|-------|-----|
| M1 | Cannot assign a license when creating a user | A **location** must be selected first | Choose United States, then pick the license |
| M2 | "Create user without product license (not recommended)" | User would have limited or no Microsoft 365 access | Assign a license unless intentionally unlicensed |
| M3 | Email added as an authentication method but cannot be used to sign in | Portal states email is for self-service password reset only | Use an authenticator app / phone for MFA; keep email for SSPR |

## Help desk ticketing

| ID | Symptom | Cause | Fix |
|----|---------|-------|-----|
| T1 | Ticket #25 "Can't connect to Internet" | User's laptop could not connect to the access point because of a Wi-Fi setting on the laptop | Corrected on the device, categorised as Hardware, documented in a comment, closed |

## General rules I took away

1. Read the whole error. Many include the fix ("did you mean...").
2. Check spelling, spaces and slashes before assuming something is broken.
3. Static vs DHCP matters: some commands only apply to one.
4. Test in layers: IP (`ping`), name (`nslookup`), then the service.
5. Document the cause and the fix every time.
