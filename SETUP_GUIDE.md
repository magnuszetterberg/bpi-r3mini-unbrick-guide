# Banana Pi R3 Mini OpenWRT Setup Guide

This guide walks you through the initial setup of OpenWRT 24.10 on the BPI-R3 Mini.

---

## 1. Update Package Lists

```bash
opkg update
```

---

## 2. Install LuCI (Web Interface)

```bash
opkg install luci
```

---

## 3. Install Wi-Fi Drivers & Firmware

```bash
opkg install kmod-mt76x2 kmod-mt7615e mt76-firmware-filogic wpad-openssl
```

---

## 4. Install Common Utilities

```bash
opkg install luci-app-firewall luci-app-opkg luci-app-sqm \
  bash nano htop curl wget kmod-usb-storage block-mount fdisk
```

---

## 5. Enable LuCI Web Server

```bash
/etc/init.d/uhttpd enable
/etc/init.d/uhttpd start
```

---

## 6. Enable & Configure Wi-Fi

```bash
uci set wireless.radio0.disabled=0
uci set wireless.radio1.disabled=0

uci set wireless.default_radio0.ssid="BPI-R3-2G"
uci set wireless.default_radio0.encryption="psk2"
uci set wireless.default_radio0.key="ChangeMe123!"

uci set wireless.default_radio1.ssid="BPI-R3-5G"
uci set wireless.default_radio1.encryption="psk2"
uci set wireless.default_radio1.key="ChangeMe123!"

uci commit wireless
wifi reload
```

---

## 7. Enable Firewall & DHCP

```bash
/etc/init.d/firewall enable
/etc/init.d/firewall start
/etc/init.d/dnsmasq enable
/etc/init.d/dnsmasq start
```

---

## 8. Access LuCI Web Interface

Open your browser and go to: [http://192.168.1.1](http://192.168.1.1)

---

## Notes
- Change Wi-Fi SSIDs and passwords as needed.
- Run all commands as root.
- For advanced configuration, use LuCI or SSH.
