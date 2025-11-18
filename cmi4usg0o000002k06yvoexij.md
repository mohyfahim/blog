---
title: "How to Rename USB Wi-Fi and LTE Dongles Using udev"
seoTitle: "Rename USB Dongles Using udev Guide"
seoDescription: "Learn how to rename USB Wi-Fi and LTE dongles using udev rules for stable, readable interface names in Linux systems"
datePublished: Tue Nov 18 2025 17:34:32 GMT+0000 (Coordinated Universal Time)
cuid: cmi4usg0o000002k06yvoexij
slug: how-to-rename-usb-wi-fi-and-lte-dongles-using-udev
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/FO4CR0MnY_k/upload/a3bc5f6527964563856f2d121938c587.jpeg
tags: linux, security, hacking, usb, embedded-systems

---

**Short summary / TL;DR**:

My system has an on-board Wi-Fi and several USB network devices (a Realtek Wi-Fi dongle and an RNDIS LTE modem). USB interfaces get unpredictable names like wlxa0a3f0904f79. I solved this by writing udev rules that detect devices by USB vendor:product (VID:PID) and (optionally) DRIVER or MAC, then rename them to stable names (wlan-usb and lte-usb). This makes NetworkManager and scripts much simpler and deterministic.

Below is a publishable blog post / personal documentation you can keep, copy, or paste later.

# Problem statement

USB Wi-Fi dongles and USB LTE modems appear with interface names derived from MACs, e.g. wlxa0a3f0904f79.

Names change between different dongles or devices; this breaks static configuration (bridge/NetworkManager profiles, scripts).

I need stable, readable names: wlan-usb for the USB Wi-Fi dongle and lte-usb for the RNDIS/LTE device.

# Solution overview

Use udev rules in `/etc/udev/rules.d/` to detect the USB device by vendor/product ID (VID:PID), optionally match the driver (e.g., rndis\_host) or the MAC, and rename the kernel net interface at creation time using `NAME="..."`.

This is:

* robust (matches device hardware, not ephemeral kernel names),
    
* deterministic (same device always gets the same name), and
    
* integrates cleanly with NetworkManager once the rename is applied.
    

## Exact udev rules I used

`> Replace 0bda:b812 and 1782:000c with your device VID:PID if different. If you prefer MAC-based match, see the MAC example below.`

Create `/etc/udev/rules.d/10-network-usb.rules` with the following contents:

```bash
# /etc/udev/rules.d/10-network-usb.rules
# Rename Realtek USB Wi-Fi 0bda:b812 -> wlan-usb
SUBSYSTEM=="net", ACTION=="add", SUBSYSTEMS=="usb", \
ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="b812", \
NAME="wlan-usb"

# Rename Spreadtrum/Unisoc RNDIS device 1782:000c -> lte-usb
# Prefer this rule only for RNDIS driver instances to avoid renaming non-rndis interfaces
SUBSYSTEM=="net", ACTION=="add", SUBSYSTEMS=="usb", \
ATTRS{idVendor}=="1782", ATTRS{idProduct}=="000c", DRIVER=="rndis_host", \
NAME="lte-usb"
```

## How to install & test the rules (copy/paste)

```bash
# create the rule file (example)
sudo tee /etc/udev/rules.d/10-network-usb.rules > /dev/null <<'EOF’

# Rename Realtek USB Wi-Fi 0bda:b812 -> wlan-usb

SUBSYSTEM=="net", ACTION=="add", SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="b812", NAME="wlan-usb"

# Rename Spreadtrum/Unisoc RNDIS device 1782:000c -> lte-usb
SUBSYSTEM=="net", ACTION=="add", SUBSYSTEMS=="usb", ATTRS{idVendor}=="1782", ATTRS{idProduct}=="000c", DRIVER=="rndis_host", NAME="lte-usb"

EOF
```

## Reload rules

```bash
sudo udevadm control --reload
# trigger (or unplug & replug the dongles)
sudo udevadm trigger --action=add /sys/class/net/* || true
# show renamed interfaces
ifconfig -a
```

If you unplug and replug the device, you should see `wlan-usb` and `lte-usb` appear.