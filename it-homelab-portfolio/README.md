# IT Help Desk Homelab Portfolio

Hands-on labs I built and documented while preparing for an entry-level IT / help desk role. Every lab includes the exact steps I followed, the errors I hit, how I diagnosed them, and screenshots as proof.

**Background:** Associate degree in Computer Science, CompTIA A+ certified.

## Labs

| # | Lab | What I did | Write-up |
|---|-----|-----------|----------|
| 1 | Windows Server Active Directory | Built a domain controller (`lab.local`), OUs, users, groups, file share, Group Policy, password reset | [docs/01-active-directory.md](docs/01-active-directory.md) |
| 2 | DHCP and DNS troubleshooting | DHCP scope, lease testing, DNS records, `ipconfig` / `nslookup` / `ping` drills, a simulated wrong-DNS fault | [docs/02-dhcp-dns-troubleshooting.md](docs/02-dhcp-dns-troubleshooting.md) |
| 3 | Linux users, permissions and SSH | Users and groups, shared folder permissions, access-denied testing, SSH from Windows, `systemctl` / `journalctl` | [docs/03-linux-users-permissions-ssh.md](docs/03-linux-users-permissions-ssh.md) |
| 4 | Microsoft 365 admin (free developer tenant) | Service health, create a user, assign a license, MFA / authentication methods | [docs/04-microsoft-365-admin.md](docs/04-microsoft-365-admin.md) |
| 5 | CertMaster Labs: help desk ticketing (Issue Trax) | Worked a "Can't connect to Internet" ticket from open to closed | [docs/05-help-desk-ticketing.md](docs/05-help-desk-ticketing.md) |

**Every error I hit, with cause and fix:** [docs/troubleshooting-log.md](docs/troubleshooting-log.md)

## Lab environment

| Component | Details |
|-----------|---------|
| Hypervisor | Oracle VirtualBox on a Windows PC |
| Server VM | Windows Server 2022 (Desktop Experience), hostname `D001`, static IP `192.168.10.10`, domain `lab.local` |
| Client VM | Windows 10, domain joined, hostname `DOO2` |
| Network | VirtualBox internal network, `192.168.10.0/24` |
| Linux | Linux Mint laptop (Ubuntu 24.04 base) |
| SSH client | Windows 10 PC using PowerShell |
| Cloud | Microsoft 365 E5 Developer tenant (free) |

## Skills demonstrated

- **Identity and access:** Active Directory Users and Computers, OUs, security groups, password resets, account options, Group Policy, least-privilege folder access
- **Networking:** DHCP scopes and leases, static vs DHCP addressing, DNS A records, `ipconfig`, `nslookup`, `ping`
- **Linux administration:** `adduser`, `usermod`, `chown`, `chmod`, `su`, groups, SSH, `systemctl`, `journalctl`, `apt`
- **Microsoft 365 / Entra ID:** user creation, license assignment, per-user MFA, authentication methods, service health
- **Help desk process:** ticket triage, categorising, documenting root cause, closing tickets
- **Troubleshooting method:** read the exact error, isolate the cause, fix, verify, document

## Troubleshooting approach

1. Read the exact error message (many contain the fix).
2. Check the basics first: spelling, spaces, paths, IP settings.
3. Change one thing at a time.
4. Verify the fix with a second tool (e.g. `ipconfig /all`, then `nslookup`, then `ping`).
5. Write it down.

## Notes

- Screenshots are in [`screenshots/`](screenshots/), grouped by lab.
- All usernames, IPs, domains and tickets are from lab environments.
