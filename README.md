# Home Server on an Old Laptop: A Step-by-Step Guide

This guide documents how to transform an old laptop into a 24/7 home server. This setup is specifically designed for situations where you don't have Ethernet access and must rely entirely on Wi-Fi.

---

## Prerequisites

Before starting, make sure you have the following:
*   An old laptop (Intel 7th Gen or newer recommended for media).
*   A USB flash drive (at least 4GB).
*   An Android phone with a USB cable (for temporary internet during setup).
*   Another computer to prepare the installation media.

---

## Phase 1: Preparation and OS Installation

### 1. Create a Bootable USB
Download the Ubuntu Server ISO from the official website. Use a tool like **BalenaEtcher** or **Rufus** to flash the ISO onto your USB drive.

### 2. Booting from USB
Plug the USB into your old laptop and restart it. Immediately start tapping the BIOS/Boot Menu key (usually F12, F11, or F2 depending on your brand). Select your USB drive from the list.

### 3. The "No Ethernet" Workaround
When you reach the network configuration screen, the installer will likely say no network is found. 
*   Connect your Android phone to the laptop via USB.
*   On your phone, go to **Settings > Hotspot & Tethering > USB Tethering** and turn it on.
*   The installer should now detect a "wired" connection (your phone).

### 4. Complete the Install
Follow the prompts to finish the installation. Choose to "Erase disk and install Ubuntu" to ensure a clean setup. When asked, check the box to **Install OpenSSH Server**.

---

## Phase 2: Configuring Headless Wi-Fi

Once the installation is done and you've rebooted, log in. Your phone is still providing internet for now.

### 1. Install Wireless Tools
Run the following command to get the necessary tools for Wi-Fi:
```bash
sudo apt update && sudo apt install wireless-tools wpasupplicant
```

### 2. Find Your Wi-Fi Interface
Run `ls /sys/class/net/` to find your wireless interface name (it usually starts with `w`). Write it down (e.g., `wlp2s0`).

### 3. Edit Netplan Configuration
Open the Netplan config file:
```bash
sudo nano /etc/netplan/01-netplan.yaml
```
Enter your Wi-Fi details (using the template in the `templates/` folder of this repo). Save and exit (Ctrl+X, Y, Enter).

### 4. Apply and Test
Run `sudo netplan apply`. You can now unplug your phone. Test your connection with `ping google.com`.

---

## Phase 3: Server Optimization

### 1. Disable Sleep on Lid Close
To keep the server running when you close the laptop, edit the login configuration:
```bash
sudo nano /etc/systemd/logind.conf
```
Find these lines, uncomment them (remove the `#`), and change them to `ignore`:
```text
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore
```
Restart the service: `sudo systemctl restart systemd-logind`.

---

## Phase 4: Setting Up Services (CasaOS & More)

### 1. Install CasaOS
CasaOS provides a simple web dashboard to manage your server. Run the official install script:
```bash
curl -fsSL https://get.casaos.io | sudo bash
```
Once finished, go to your server's IP address in a web browser on another computer (e.g., `http://192.168.1.50`).

<p align="center">
  <img src="https://raw.githubusercontent.com/tasdidnoor/Assets/main/nodeBridge/CasaOsDashboard.png" width="97%" alt="CasaOs Dashboard" />
</p>

### 2. Jellyfin Media Server
Install Jellyfin from the CasaOS App Store. If your laptop has an Intel CPU, go to **Dashboard > Playback** and enable **Intel QuickSync** for smooth streaming.

### 3. Remote Access via Cloudflare
To access your server from anywhere without a router password:
1.  Install `cloudflared` on your server.
2.  Create a tunnel on the Cloudflare Zero Trust dashboard.
3.  Route your domain (e.g., `server.yourdomain.com`) to `http://localhost:80`.

---

## Final Thoughts
This setup turns a piece of "e-waste" into a powerful tool. By using phone tethering and Netplan, you can bypass the biggest hurdle of laptop-based servers: the lack of a wired connection.

---

## License
This project is licensed under the MIT License - see the LICENSE file for details.
