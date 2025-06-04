# MikroTik Firewall and NAT Rules to Receive and Transmit Internet

This configuration assumes:
- **WAN interface**: `ether1`
- **LAN interface**: `bridge1`
- **LAN IP range**: `192.168.88.0/24`

---

## ✅ 1. NAT Rule (Masquerade)
Allows LAN devices to access the internet by masquerading their IP addresses through the WAN interface.

```bash
/ip firewall nat
add chain=srcnat out-interface=ether1 action=masquerade
```

---

## ✅ 2. Basic Firewall Filter Rules
Protects the router and allows LAN-to-WAN traffic.

```bash
/ip firewall filter
add chain=input connection-state=established,related action=accept comment="Allow established/related
add chain=input in-interface=bridge1 action=accept comment="Allow LAN access to router"
add chain=forward connection-state=established,related action=accept comment="Allow established forwarding"
add chain=forward in-interface=bridge1 out-interface=ether1 action=accept comment="Allow LAN to WAN"
```

---

## ✅ 3. DHCP Server (Optional)
Configure the DHCP server to assign IP addresses to LAN clients.

```bash
/ip pool
add name=dhcp_pool ranges=192.168.88.10-192.168.88.254

/ip dhcp-server
add name=dhcp1 interface=bridge1 address-pool=dhcp_pool disabled=no

/ip dhcp-server network
add address=192.168.88.0/24 gateway=192.168.88.1 dns-server=8.8.8.8
```

---

## ✅ Tips
- Verify interfaces using `/interface print`
- Confirm IP setup with `/ip address print`
- Hotspot and PPPoE setups also require this masquerade rule
- Adjust interfaces and IPs to match your actual setup
