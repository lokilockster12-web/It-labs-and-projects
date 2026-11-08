# Network Isolation & Remote Access Lab: USB Tethering, NAT Boundaries, and the AnyDesk Workaround

## Summary
I wanted to share a mouse and keyboard between a Linux Mint laptop and a Windows 10 PC that has no Wi-Fi. The Windows PC only had internet through an old smartphone using **USB tethering**. KDE Connect and Barrier both failed because the two machines ended up on **different subnets behind a NAT boundary**. I documented why it failed, then solved the real goal (controlling one PC from the other) with **AnyDesk**, which does not need both machines on the same network.

**Skills shown:** network troubleshooting, subnets/NAT/DHCP concepts, `ipconfig` / `ifconfig` diagnostics, Windows network profiles and firewall, Linux command line and config files, package installation, remote desktop tooling, documentation.

---

## 1. Environment

| Role | Device | Connection |
|---|---|---|
| Primary host | Lenovo Yoga 900 running Linux Mint | Home Wi-Fi |
| Secondary host | Windows 10 PC (no native Wi-Fi) | USB tethering through a smartphone (adapter shown as `Ethernet 3`) |
| Gateway for Windows PC | Old smartphone | Home Wi-Fi upstream, USB tether downstream |

**Goal:** share mouse/keyboard and move between the two PCs without building VPNs or static routes.

---

## 2. Topology and the "NAT Wall"

```
[ Home Wi-Fi Router ]
       │
       ├──────> [ Linux Mint Laptop ]            (primary subnet)
       │
       └──────> [ Smartphone ] ──(USB tether / NAT)──> [ Windows PC ]   (192.168.42.x)
```

- The phone runs its own DHCP server and NAT for the tethered PC, so the Windows PC received a private address in the `192.168.42.x` range.
- The Linux laptop sits on the home router's subnet. The two machines are not in the same broadcast domain.

**Why discovery failed**
- **Layer 2:** KDE Connect finds devices with UDP broadcast. Broadcasts do not cross the phone's NAT.
- **Layer 3:** Entering the Windows PC's IP manually (`customDevices`) does not help, because the home router has no route to the phone's internal `192.168.42.x` network, so traffic from the laptop cannot reach the PC.

---

## 3. What I tried

| # | Attempt | Result |
|---|---|---|
| 1 | Installed KDE Connect on both PCs and tried auto-discovery | Windows showed "No devices found" |
| 2 | Ran `ipconfig` on Windows to find the tether adapter's IPv4 address, subnet mask and gateway | Confirmed the PC was on a separate `192.168.42.x` network |
| 3 | Ran `ifconfig` on Linux to compare interfaces | Confirmed different subnets |
| 4 | Set the Windows `Ethernet 3` profile to **Private** so the PC is discoverable | Profile changed correctly, discovery still failed |
| 5 | Created a KDE Connect `customDevices` config on Linux pointing at the Windows IP | See the typo note in the troubleshooting log; no connection either way |
| 6 | Installed **Barrier** on Linux (Server) for input sharing with Windows (Client) | Package installed, but TCP still could not cross the NAT boundary reliably |

Full step-by-step notes are in [troubleshooting-log.md](troubleshooting-log.md).

---

## 4. Decision

Fixing this properly meant static routes, a VPN, or a tunnel. That is a lot of overhead for sharing a mouse, so I stopped and documented the boundary instead of forcing it.

## 5. Resolution: AnyDesk

I installed **AnyDesk** on both machines (Windows 10 through the browser download, Linux Mint with AnyDesk 8.1) and connected the two PCs. This works because AnyDesk connects through its own ID-based service over the internet instead of relying on local network discovery. Both PCs already had internet access (one via Wi-Fi, one via the tethered phone), so the subnet split no longer mattered.

**Trade-offs I noted**
- Works across subnets and NAT with no router or routing changes.
- Needs internet on both sides; it is not a purely local connection.
- It is remote desktop, not seamless edge-of-screen mouse sharing like Barrier would be on one LAN.
- Free licence is for non-professional use only.

---

## 6. Key takeaways
1. Peer-to-peer discovery tools depend on a shared broadcast domain; a NAT boundary breaks that.
2. Manually entering an IP only helps if there is a routed path to it.
3. Checking IP, mask and gateway on both machines (`ipconfig`, `ifconfig`) quickly shows when two devices are on different networks.
4. Typos in paths matter: one wrong character in a config path hid the real problem for a while (see log).
5. Sometimes the best fix is to change the tool (AnyDesk) rather than rebuild the network.

## 7. If I extended this lab
- Put both PCs on the same router (Wi-Fi USB adapter for the Windows PC) and retest KDE Connect/Barrier.
- Test Windows built-in Remote Desktop over a routed path.
- Compare AnyDesk with another remote tool for latency and security settings.
