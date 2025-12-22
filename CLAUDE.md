# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a documentation repository for setting up remote public access to a **Dinstar SME IP PBX UC120** device without adding extra hardware. The solution involves using an Ubuntu Server on Linode as an OpenVPN gateway.

## Project Context

**Hardware/Infrastructure:**
- Dinstar SME IP PBX UC120 (VoIP/telephony system with built-in OpenVPN Client/Server)
- Ubuntu Server on Linode (cloud-based VPN gateway)
- Goal: Access the IP PBX from public internet without additional devices

**Current Contents:**
- `Ranjan Dinstar SME IP PBX UC120.pdf` - Configuration screenshots showing:
  - OpenVPN Client configuration interface
  - OpenVPN Server management interface
  - Network/VPN settings for the RP-UC120 device
  - Requirement specification

## Dinstar UC120 Key Features (from documentation)

The device web interface (RP-UC120) includes:
- **VPN/OpenVPN** - Both Client and Server modes
- **Network Configuration** - Status, System, Network, Profile, Extension, Trunk, Call Control
- **VPN Protocols** - OpenVPN, L2TP, PPTP, SSTP support
- **Certificate Management** - CA, certificate revocation, client/server certificates
- **Authentication** - Username/password and certificate-based auth

## Typical Use Cases for This Repository

1. **Initial Setup Documentation** - Document the complete OpenVPN configuration process
2. **Configuration Scripts** - Add automation scripts for:
   - Ubuntu server OpenVPN server setup
   - Firewall rules (iptables/ufw)
   - Port forwarding configuration
   - Certificate generation and distribution
3. **Troubleshooting Guides** - Common issues with PBX remote access
4. **Security Hardening** - Best practices for exposing PBX to public internet

## Recommended Repository Structure (if expanding)

```
/
├── docs/
│   ├── Ranjan Dinstar SME IP PBX UC120.pdf
│   ├── setup-guide.md (step-by-step instructions)
│   └── troubleshooting.md
├── scripts/
│   ├── ubuntu-openvpn-server-setup.sh
│   ├── generate-certificates.sh
│   └── firewall-rules.sh
├── configs/
│   ├── server.conf.example (OpenVPN server config for Ubuntu)
│   ├── client.ovpn.example (OpenVPN client config for UC120)
│   └── iptables-rules.example
└── README.md (overview and quick start)
```

## Solution Architecture

**Recommended Setup:**
1. Ubuntu Server on Linode runs OpenVPN server with public IP
2. Dinstar UC120 connects as OpenVPN client to Ubuntu server
3. Ubuntu server forwards traffic to UC120 via VPN tunnel
4. Users access UC120 web interface via Ubuntu server's public IP
5. SIP/VoIP traffic routed through VPN tunnel

## Working with This Repository

**To add configuration scripts:**
```bash
# Create scripts directory
mkdir -p scripts configs docs

# Move existing PDF to docs
git mv "Ranjan Dinstar SME IP PBX UC120.pdf" docs/

# Add your scripts/configs
# Commit changes
git add .
git commit -m "Add OpenVPN configuration scripts"
```

**To test OpenVPN configurations:**
```bash
# On Ubuntu server
sudo openvpn --config configs/server.conf

# Check VPN status
sudo systemctl status openvpn@server

# Monitor connections
sudo tail -f /var/log/openvpn.log
```

## Security Considerations

- Use certificate-based authentication (shown as disabled in PDF screenshot)
- Configure firewall to only allow necessary ports (OpenVPN 1194/UDP, web interface port)
- Implement fail2ban for brute force protection
- Use strong passwords for UC120 admin interface
- Consider IP whitelisting for management access
- Regularly update Ubuntu server and UC120 firmware

## Network Ports

**Typical ports to configure:**
- **1194/UDP** - OpenVPN default port
- **5060/UDP** - SIP signaling (if exposing SIP)
- **10000-20000/UDP** - RTP media streams (configurable)
- **80/TCP or 443/TCP** - UC120 web interface (if proxying)
