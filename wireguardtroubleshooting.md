# WireGuard Server Troubleshooting Guide

This guide provides steps to diagnose and fix common issues when running a WireGuard VPN server on a Linux server (e.g. Ubuntu).

---

## 1. Check WireGuard Service Status

```bash
sudo systemctl status wg-quick@wg0
```

* Look for `Active: active (exited)`
* If `failed`, read the error logs carefully.

---

## 2. Common Errors and Fixes

### ❌ Parsing Error: `Line unrecognized: 'q[Interface]'`

* Cause: Typo in config file.
* Fix: Edit `/etc/wireguard/wg0.conf` and ensure:

  ```ini
  [Interface]
  Address = 10.200.200.1/24
  PrivateKey = <server_private_key>
  ListenPort = 51820
  ```

### ❌ Peers not applying

* Fix: Edit `/etc/wireguard/wg0.conf`, update `[Peer]` section, then:

  ```bash
  sudo wg-quick down wg0
  sudo wg-quick up wg0
  ```

### ❌ No ping to peer

* Check IP forwarding:

  ```bash
  sudo sysctl net.ipv4.ip_forward
  # If 0, enable it:
  echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
  sudo sysctl -p
  ```
* Allow traffic through UFW:

  ```bash
  sudo ufw allow 51820/udp
  sudo ufw allow in on wg0
  sudo ufw allow out on wg0
  ```

---

## 3. View WireGuard Interface

```bash
sudo wg show
```

Check:

* `latest handshake`: if missing, peer isn't connecting
* `transfer`: shows data flow

---

## 4. Restarting WireGuard

```bash
sudo systemctl restart wg-quick@wg0
```

Or:

```bash
sudo wg-quick down wg0
sudo wg-quick up wg0
```

---

## 5. Deleting a Peer

### Temporarily:

```bash
sudo wg set wg0 peer <public-key> remove
```

### Permanently:

* Remove from `/etc/wireguard/wg0.conf`
* Restart WireGuard

---

## 6. Check Config Syntax

```bash
sudo wg-quick strip wg0
```

This will print a minimal, validated version of the config.

---

## 7. MikroTik Notes

* Use `/interface wireguard peers print/remove` to manage peers
* Restart interface:

  ```bash
  /interface wireguard disable wg1
  /interface wireguard enable wg1
  ```
