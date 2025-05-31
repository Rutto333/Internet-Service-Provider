# Server to MikroTik WireGuard VPN Setup Guide

## Prerequisites

* VPS with Ubuntu/Debian
* MikroTik router with RouterOS 7.x+ (WireGuard support)
* SSH access to server
* Admin access to MikroTik router

## Part 1: Server Setup (WireGuard Server)

### Step 1: Install WireGuard on Server

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install WireGuard
sudo apt install wireguard -y

# Install additional tools
sudo apt install qrencode iptables-persistent -y
```

### Step 2: Generate Server Keys

```bash
# Navigate to WireGuard directory
cd /etc/wireguard

# Generate private key
wg genkey | sudo tee server_private.key

# Generate public key from private key
sudo cat server_private.key | wg pubkey | sudo tee server_public.key

# Set proper permissions
sudo chmod 600 server_private.key
```

### Step 3: Create WireGuard Server Configuration

```bash
# Create server config file
sudo nano /etc/wireguard/wg0.conf
```

Add the following configuration:

```ini
[Interface]
PrivateKey = YOUR_SERVER_PRIVATE_KEY
Address = 10.0.0.1/24
ListenPort = 51820
SaveConfig = true

# Enable IP forwarding and NAT
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

# MikroTik client configuration will be added here
[Peer]
PublicKey = MIKROTIK_PUBLIC_KEY
AllowedIPs = 10.0.0.2/32
```

### Step 4: Enable IP Forwarding

```bash
# Enable IP forwarding
echo 'net.ipv4.ip_forward=1' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

### Step 5: Configure Firewall

```bash
# Allow WireGuard port
sudo ufw allow 51820/udp

# Allow SSH (if using UFW)
sudo ufw allow ssh

# Enable firewall
sudo ufw enable
```

## Part 2: MikroTik Router Setup (WireGuard Client)

### Step 1: Generate MikroTik Keys

Connect to MikroTik via Winbox or SSH and run:

```
/interface/wireguard
add name=wg-server listen-port=51820

/interface/wireguard/peers
add interface=wg-server public-key="SERVER_PUBLIC_KEY" endpoint-address=YOUR_SERVER_IP endpoint-port=51820 allowed-address=0.0.0.0/0
```

### Step 2: Get MikroTik Public Key

```
/interface/wireguard
print
```

Copy the public key from the output.

### Step 3: Configure MikroTik WireGuard Interface

```
# Set IP address on WireGuard interface
/ip/address
add address=10.0.0.2/24 interface=wg-server

# Add route through VPN
/ip/route
add dst-address=0.0.0.0/0 gateway=10.0.0.1 distance=1
```

### Step 4: Configure Firewall Rules

```
# Allow established and related connections
/ip/firewall/filter
add chain=input action=accept connection-state=established,related

# Allow WireGuard
add chain=input action=accept protocol=udp dst-port=51820

# Allow local network
add chain=input action=accept src-address=192.168.1.0/24

# NAT rule for internet access through VPN
/ip/firewall/nat
add chain=srcnat action=masquerade out-interface=wg-server
```

## Part 3: Complete Server Configuration

### Step 1: Add MikroTik Peer to Server Config

Edit the server configuration:

```bash
sudo nano /etc/wireguard/wg0.conf
```

Update the \[Peer] section with MikroTik's public key:

```ini
[Peer]
PublicKey = MIKROTIK_PUBLIC_KEY_HERE
AllowedIPs = 10.0.0.2/32
```

### Step 2: Start WireGuard Service

```bash
# Enable and start WireGuard
sudo systemctl enable wg-quick@wg0
sudo systemctl start wg-quick@wg0

# Check status
sudo systemctl status wg-quick@wg0
sudo wg show
```

## Part 4: PHPNuxBill Configuration

### Step 1: Configure PHPNuxBill

1. Open browser and navigate to `http://YOUR_SERVER_IP`
2. Set up MikroTik Router connection in PHPNuxBill:

   * Router IP: `10.0.0.2` (MikroTik VPN IP)
   * API Port: `8728`
   * Username/Password: MikroTik admin credentials

### Step 2: Enable MikroTik API

On MikroTik router:

```
/ip/service
set api disabled=no port=8728

# Create API user (optional, for security)
/user
add name=api-user password=secure_password group=full
```

## Part 5: Testing and Verification

### Step 1: Test VPN Connection

On server:

```bash
# Check WireGuard status
sudo wg show

# Ping MikroTik through VPN
ping 10.0.0.2
```

On MikroTik:

```
# Check interface status
/interface/wireguard/print

# Ping server through VPN
/ping 10.0.0.1
```

### Step 2: Test PHPNuxBill Connection

1. Login to PHPNuxBill admin panel
2. Go to Settings → Router
3. Test connection to MikroTik
4. Verify hotspot users can be managed

## Troubleshooting

### Common Issues:

1. **Connection fails**: Check firewall rules and ensure ports are open
2. **API connection fails**: Verify MikroTik API is enabled and credentials are correct
3. **No internet through VPN**: Check IP forwarding and NAT rules
4. **WireGuard won't start**: Check configuration syntax and key formats

### Debug Commands:

```bash
# Server side
sudo wg show
sudo journalctl -u wg-quick@wg0
sudo iptables -L -n

# MikroTik side
/log print where topics~"wireguard"
/interface/wireguard/print detail
```

## Security Recommendations

1. Change default WireGuard port
2. Use strong passwords for MikroTik API
3. Implement fail2ban on server
4. Regular security updates
5. Monitor logs for suspicious activity
6. Use certificate-based authentication where possible

## Network Topology

```
Internet
    ↓
[Server] ←→ WireGuard VPN ←→ [MikroTik Router]
10.0.0.1                             10.0.0.2
    ↓                                  ↓
PHPNuxBill                        Local Network
                                  192.168.1.0/24
```

This setup allows PHPNuxBill running on your server to manage the MikroTik router through a secure WireGuard VPN tunnel.

