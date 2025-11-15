
### **Fix Wi-Fi on Broadcom BCM4331 (MacBook Pro A1425) — Pop!_OS / Ubuntu (Offline Method)**

If you have installed Ubuntu, Pop!_OS, or any other Debian-based distribution on an older MacBook (such as the A1425), you may encounter issues with the built-in Broadcom Wi-Fi module.
This guide explains how to enable Wi-Fi on such machines — particularly those using the Broadcom BCM4331 chipset.
The method has been fully tested offline on Pop!_OS with kernel 6.x and should work on other Debian-based systems.

Before you begin, you can verify your system details:
Check your kernel version:
```
uname -r
```

Check your Wi-Fi chipset:
```
lspci -nn | grep -i network
```

If the output contains BCM4331, this guide applies to your device.


#### **1. Remove conflicting Broadcom drivers**

```
sudo apt remove --purge 'broadcom-sta-dkms' 'bcmwl-kernel-source'
sudo reboot
```

#### **2. Install b43-fwcutter (offline)**

Download on another device **b43-fwcutter** (Ubuntu 22.04):
https://mirrors.edge.kernel.org/ubuntu/pool/main/b/b43-fwcutter/b43-fwcutter_019-4_amd64.deb
Transfer to your system with a USB stick and install:
```
sudo dpkg -i b43-fwcutter_019-4_amd64.deb
```
#### **3. Download Broadcom firmware source (offline)**
Download **broadcom-wl-5.100.138.tar.bz2**:
https://sourceforge.net/projects/immortalwrt/files/sources/broadcom-wl-5.100.138.tar.bz2/download
Move the file to your Linux system.
#### **4. Extract and install b43 firmware**
```
tar xvjf broadcom-wl-5.100.138.tar.bz2
cd broadcom-wl-5.100.138/linux
sudo b43-fwcutter -w /lib/firmware wl_apsta_mimo.o
```
This places the firmware into /lib/firmware/b43.
#### **5. Reload the driver**

```
sudo modprobe -r b43
sudo modprobe b43
```

Check that the firmware is loaded:
```
dmesg | grep -i b43 | tail -n 20
```

You should see something like:
```
Loading firmware version 666.2
```
#### **6. Bring up Wi-Fi**

```
nmcli device
```

Look for a Wi-Fi interface (e.g., wlp3s0).

If missing:
```
sudo systemctl restart NetworkManager
```

Or reboot the system:
```
sudo reboot
```
