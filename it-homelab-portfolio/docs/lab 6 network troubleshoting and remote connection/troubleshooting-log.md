# Troubleshooting Log: USB Tethering Lab

Format: **Symptom → What I checked → Finding → Next step**

## 1. KDE Connect shows no devices
- **Symptom:** Windows KDE Connect: "No devices found", refresh did nothing.
- **Check:** Both apps open, both PCs powered and online.
- **Finding:** Discovery relies on broadcast; likely different networks.

## 2. Compare network settings on both PCs
- **Windows:** `ipconfig` showed adapter `Ethernet 3` (the USB tether) with its own IPv4 address, subnet mask and default gateway in `192.168.42.x`. The Wi-Fi adapter showed *Media disconnected* because the PC has no Wi-Fi.
- **Linux:** `ifconfig` run on Mint to read the laptop's Wi-Fi address.
- **Finding:** Different subnets. This is the root cause.

## 3. Windows network profile
- **Check:** Settings → Network & Internet → Status shows `Ethernet 3`, connected to the internet.
- **Action:** Network profile set to **Private** ("PC is discoverable") instead of Public.
- **Finding:** Needed for discovery, but not enough on its own.

## 4. Manual IP in KDE Connect config (Linux)
- **Action:** Tried to write `[General]` / `customDevices=<windows ip>` into `~/.config/kdeconnect/config`.
- **Problem 1:** The first attempt printed `mkdir: command not found`, so the command was mistyped.
- **Problem 2:** Opening the file in nano showed `Directory '/home/lavi/,config/kdeconnect' does not exist`. The path had a **comma instead of a dot** (`,config` instead of `.config`).
- **Fix:** Use the correct path `~/.config/kdeconnect/config`, create the folder with `mkdir -p ~/.config/kdeconnect`, then restart the daemon with `killall kdeconnectd`.
- **Finding:** Even with the file correct, packets cannot reach `192.168.42.x` from the home subnet, so the connection still fails.

## 5. Barrier
- **Action:** Installed Barrier on Linux with apt (install output ended with `Setting up barrier (2.4.0...)`), then ran `ifconfig`.
- **Finding:** Barrier uses TCP between server and client, so it hits the same routing wall.

## 6. Decision and workaround
- Chose not to build static routes or a VPN for a mouse-sharing goal.
- Installed AnyDesk on both PCs and connected them successfully.

## Commands used
```bash
# Linux
ifconfig
mkdir -p ~/.config/kdeconnect
nano ~/.config/kdeconnect/config
killall kdeconnectd
sudo apt install barrier
```
```cmd
:: Windows
ipconfig
```
