# 🌐 Dinstar SME IP PBX UC120 Remote Access Setup

<div align="center">

![VoIP](https://img.shields.io/badge/VoIP-IP%20PBX-blue)
![OpenVPN](https://img.shields.io/badge/VPN-OpenVPN-orange)
![Ubuntu](https://img.shields.io/badge/Server-Ubuntu%2020.04+-purple)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Production%20Ready-brightgreen)

**Complete solution for accessing your Dinstar UC120 IP PBX from anywhere in the world**

[📖 User Manual](#-documentation) • [🚀 Quick Start](#-quick-start) • [🔒 Security](#-security) • [🛠️ Troubleshooting](#-troubleshooting)

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Architecture](#-architecture)
- [Prerequisites](#-prerequisites)
- [Documentation](#-documentation)
- [Quick Start](#-quick-start)
- [Security](#-security)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🎯 Overview

This repository provides a comprehensive guide for setting up **secure remote access** to a Dinstar SME IP PBX UC120 device without requiring additional hardware. The solution leverages an Ubuntu Server (hosted on Linode or similar cloud provider) as an OpenVPN gateway to create a secure encrypted tunnel to your IP PBX system.

### Why This Solution?

- ✅ **No Extra Hardware Required** - Uses existing infrastructure
- ✅ **Secure Encrypted Tunnel** - Industry-standard OpenVPN with TLS
- ✅ **Cloud-Based Gateway** - Reliable Linode infrastructure
- ✅ **Remote Management** - Access your PBX from anywhere
- ✅ **Cost-Effective** - Minimal cloud server costs
- ✅ **Production Ready** - Tested and documented

---

## ✨ Features

### 🔐 Security Features
- **TLS/SSL Encryption** - Military-grade AES-256-GCM cipher
- **Certificate-Based Authentication** - PKI infrastructure with Easy-RSA
- **Firewall Protection** - UFW with minimal port exposure
- **Fail2Ban Integration** - Automatic blocking of brute-force attempts
- **IP Whitelisting** - Optional restriction to known IP addresses

### 🌍 Network Features
- **Site-to-Site VPN** - Persistent tunnel between cloud server and PBX
- **Static IP Assignment** - Consistent addressing for UC120
- **Custom Routing** - Flexible traffic flow control
- **NAT Traversal** - Works behind firewalls and routers

### 📞 VoIP Features
- **Remote Extension Access** - SIP phones can connect from anywhere
- **Web Interface Access** - Manage PBX remotely via secure tunnel
- **Call Quality Optimization** - Compression and QoS support
- **Multi-Protocol Support** - SIP, RTP, and custom protocols

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                          INTERNET                                │
│                                                                  │
│  ┌────────────────┐              ┌────────────────┐             │
│  │  Remote Users  │──────────────│  SIP Clients   │             │
│  │  Web Browsers  │              │  Mobile Apps   │             │
│  └────────────────┘              └────────────────┘             │
└─────────────────────────────────────────────────────────────────┘
                                    │
                     Public IP: XX.XX.XX.XX
                                    │
                                    ▼
                    ┌───────────────────────────┐
                    │   Ubuntu Server (Linode)  │
                    │                           │
                    │  • OpenVPN Server         │
                    │  • Port 1194/UDP          │
                    │  • IP: 10.8.0.1           │
                    │  • UFW Firewall           │
                    │  • Fail2Ban               │
                    └───────────────────────────┘
                                    │
                      Encrypted VPN Tunnel
                      (AES-256-GCM + TLS)
                                    │
                                    ▼
                    ┌───────────────────────────┐
                    │  Dinstar UC120 IP PBX     │
                    │                           │
                    │  • OpenVPN Client         │
                    │  • IP: 10.8.0.2           │
                    │  • Local: 192.168.1.x     │
                    │  • Web Interface          │
                    │  • SIP Server             │
                    └───────────────────────────┘
                                    │
                                    ▼
                    ┌───────────────────────────┐
                    │   Local Network           │
                    │                           │
                    │  • SIP Extensions         │
                    │  • Analog Phones          │
                    │  • Internal Network       │
                    └───────────────────────────┘
```

### Traffic Flow

1. **User Access** → Connects to Ubuntu Server's public IP
2. **VPN Tunnel** → Encrypted connection established (10.8.0.0/24)
3. **PBX Access** → Traffic forwarded to UC120 (10.8.0.2)
4. **Service Delivery** → Web interface, SIP registration, call routing

---

## 📦 Prerequisites

### Hardware Requirements

| Component | Specification |
|-----------|--------------|
| **Dinstar UC120** | SME IP PBX with OpenVPN support |
| **Cloud Server** | Linode, DigitalOcean, AWS, or similar |
| **RAM** | Minimum 1GB (2GB+ recommended) |
| **Storage** | 10GB minimum |
| **Network** | 100Mbps+ connection |

### Software Requirements

| Software | Version | Purpose |
|----------|---------|---------|
| **Ubuntu Server** | 20.04 LTS or later | VPN gateway OS |
| **OpenVPN** | 2.5+ | VPN server |
| **Easy-RSA** | 3.0+ | Certificate management |
| **UFW** | Latest | Firewall |
| **Fail2Ban** | Latest | Intrusion prevention |

### Network Information Needed

Before starting, collect:

- [ ] Ubuntu Server public IP address
- [ ] Dinstar UC120 local IP address
- [ ] UC120 administrator credentials
- [ ] Desired VPN subnet (recommend: 10.8.0.0/24)
- [ ] SSH access to Ubuntu Server

---

## 📚 Documentation

### 📘 Main Documentation

- **[Complete User Manual](Dinstar-UC120-Remote-Access-Setup-Guide.pdf)** - Comprehensive 40+ page guide with:
  - Step-by-step installation instructions
  - Detailed configuration examples
  - Security hardening procedures
  - Troubleshooting guide
  - Command reference
  - Certificate management

### 📄 Quick Reference

- **[CLAUDE.md](CLAUDE.md)** - Repository guide for AI assistants
- **[Configuration Screenshots](Ranjan%20Dinstar%20SME%20IP%20PBX%20UC120.pdf)** - Original UC120 interface screenshots
- **[Cloudflare Tunnel Compatibility](cloudflare-dinstar-uc120-compatibility.md)** - Analysis of cloudflared installation on UC120

---

## 🚀 Quick Start

### Step 1: Ubuntu Server Setup (20 minutes)

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install OpenVPN and Easy-RSA
sudo apt install openvpn easy-rsa ufw fail2ban -y

# Setup certificate authority
mkdir -p ~/easy-rsa
ln -s /usr/share/easy-rsa/* ~/easy-rsa/
cd ~/easy-rsa
./easyrsa init-pki
./easyrsa build-ca nopass
./easyrsa gen-req server nopass
./easyrsa sign-req server server
./easyrsa gen-dh

# Generate client certificate for UC120
./easyrsa gen-req dinstar-uc120 nopass
./easyrsa sign-req client dinstar-uc120
```

### Step 2: Configure OpenVPN Server (10 minutes)

```bash
# Copy certificates
sudo mkdir -p /etc/openvpn/server
sudo cp ~/easy-rsa/pki/ca.crt /etc/openvpn/server/
sudo cp ~/easy-rsa/pki/issued/server.crt /etc/openvpn/server/
sudo cp ~/easy-rsa/pki/private/server.key /etc/openvpn/server/
sudo cp ~/easy-rsa/pki/dh.pem /etc/openvpn/server/

# Create server configuration
sudo nano /etc/openvpn/server/server.conf
# (See User Manual for complete configuration)

# Enable IP forwarding
sudo nano /etc/sysctl.conf
# Uncomment: net.ipv4.ip_forward=1
sudo sysctl -p

# Start OpenVPN
sudo systemctl enable openvpn-server@server
sudo systemctl start openvpn-server@server
```

### Step 3: Configure Firewall (5 minutes)

```bash
# Allow OpenVPN and SSH
sudo ufw allow 22/tcp
sudo ufw allow 1194/udp
sudo ufw enable

# Configure NAT
sudo nano /etc/ufw/before.rules
# Add NAT rules (see manual)
```

### Step 4: UC120 Configuration (10 minutes)

1. Access UC120 web interface: `http://192.168.x.x`
2. Navigate to **Network → VPN → OpenVPN → Client**
3. Upload `.ovpn` configuration file
4. Upload certificates (ca.crt, client.crt, client.key)
5. Enable connection
6. Verify status shows "Connected"

### Step 5: Test Connection (5 minutes)

```bash
# From Ubuntu Server
ping 10.8.0.2

# Check connected clients
cat /var/log/openvpn/openvpn-status.log
```

**Total Setup Time: ~50 minutes**

---

## 🔒 Security

### Security Measures Implemented

#### ✅ Encryption
- **AES-256-GCM** cipher for data encryption
- **SHA256** HMAC for authentication
- **TLS 1.2+** minimum version
- **Perfect Forward Secrecy** via DH parameters

#### ✅ Authentication
- **Certificate-based PKI** authentication
- **No default passwords** allowed
- **Strong password policy** enforced
- **Two-factor authentication** ready (optional)

#### ✅ Network Protection
- **Firewall (UFW)** with minimal port exposure
- **Fail2Ban** for brute-force protection
- **IP whitelisting** capability
- **DDoS mitigation** via cloud provider

#### ✅ Monitoring
- **Centralized logging** with syslog
- **Connection tracking** in OpenVPN logs
- **Certificate expiration monitoring**
- **Automatic security updates**

### Best Practices

1. **Change Default Credentials** - Immediately on UC120
2. **Use Strong Passwords** - Minimum 12 characters, mixed complexity
3. **Regular Updates** - Keep all systems patched
4. **Certificate Rotation** - Renew certificates annually
5. **Access Auditing** - Review logs regularly
6. **Backup Configuration** - Store securely offsite

### Security Checklist

- [ ] Changed UC120 default admin password
- [ ] Disabled unnecessary services on UC120
- [ ] Enabled UFW firewall on Ubuntu Server
- [ ] Configured Fail2Ban for OpenVPN
- [ ] Implemented strong SIP passwords
- [ ] Enabled automatic security updates
- [ ] Configured log monitoring
- [ ] Documented access procedures
- [ ] Created disaster recovery plan
- [ ] Tested backup/restore procedures

---

## 🛠️ Troubleshooting

### Common Issues

#### 🔴 VPN Connection Failed

**Symptom:** UC120 cannot connect to OpenVPN server

**Solutions:**
```bash
# Check server status
sudo systemctl status openvpn-server@server

# Verify firewall
sudo ufw status
sudo netstat -ulnp | grep 1194

# Check logs
sudo tail -f /var/log/openvpn/openvpn.log
```

#### 🔴 No Routing After Connection

**Symptom:** Connected but cannot ping UC120

**Solutions:**
```bash
# Verify IP forwarding
cat /proc/sys/net/ipv4/ip_forward  # Should return 1

# Check routing
ip route show

# Verify NAT
sudo iptables -t nat -L -v
```

#### 🔴 Poor Voice Quality

**Symptom:** Choppy audio or dropped calls

**Solutions:**
```bash
# Check latency
ping -c 100 10.8.0.2

# Test bandwidth
iperf3 -c 10.8.0.2

# Enable compression in server.conf
compress lz4-v2
```

### Getting Help

1. **Read the Manual** - Check the comprehensive User Manual PDF
2. **Review Logs** - Check `/var/log/openvpn/openvpn.log`
3. **Community Forums** - OpenVPN and Dinstar communities
4. **Issue Tracker** - Submit issues on GitHub

---

## 📊 Performance Metrics

### Expected Performance

| Metric | Value | Notes |
|--------|-------|-------|
| **Latency** | <50ms | For good VoIP quality |
| **Bandwidth** | 100+ Kbps per call | G.711 codec |
| **Concurrent Calls** | 10-30 | Depends on server specs |
| **Uptime** | 99.9%+ | With proper configuration |
| **Connection Time** | <5 seconds | Initial VPN establishment |

### Optimization Tips

- Choose geographically close Linode datacenter
- Upgrade server for high call volume
- Enable compression for bandwidth savings
- Use QoS for VoIP traffic prioritization
- Monitor and tune MTU settings

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

### Ways to Contribute

- 📖 **Documentation** - Improve guides and tutorials
- 🐛 **Bug Reports** - Report issues or problems
- 💡 **Feature Requests** - Suggest enhancements
- 🔧 **Code** - Submit automation scripts or tools
- 📸 **Screenshots** - Add visual guides
- 🌐 **Translations** - Help translate documentation

### Contribution Guidelines

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -am 'Add new feature'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## 📜 License

This project is licensed under the MIT License - see below for details:

```
MIT License

Copyright (c) 2025 Dinstar UC120 Remote Access Project

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
```

---

## 🙏 Acknowledgments

- **Dinstar** - For the UC120 IP PBX platform
- **OpenVPN** - For the robust VPN solution
- **Ubuntu/Canonical** - For the stable server platform
- **Linode** - For reliable cloud infrastructure
- **Community Contributors** - For feedback and improvements

---

## 📞 Support & Contact

### Documentation
- 📖 [Complete User Manual PDF](Dinstar-UC120-Remote-Access-Setup-Guide.pdf)
- 📘 [CLAUDE.md](CLAUDE.md) - AI assistant guide
- 📄 [Configuration Screens](Ranjan%20Dinstar%20SME%20IP%20PBX%20UC120.pdf)

### Resources
- 🌐 [Dinstar Official Website](https://www.dinstar.com/)
- 📚 [OpenVPN Documentation](https://openvpn.net/community-resources/)
- 💬 [Ubuntu Community](https://ubuntu.com/community)
- ☁️ [Linode Guides](https://www.linode.com/docs/)

### Professional Support
For professional deployment, security auditing, or custom integration services, please consult with certified network security professionals.

---

## 🎓 Additional Resources

### Tutorials & Guides
- [OpenVPN Hardening Guide](https://community.openvpn.net/openvpn/wiki/Hardening)
- [Ubuntu Server Security](https://ubuntu.com/server/docs/security-introduction)
- [VoIP Best Practices](https://www.voip-info.org/)

### Tools & Utilities
- [OpenVPN Connect](https://openvpn.net/client/) - Client software
- [Wireshark](https://www.wireshark.org/) - Network analysis
- [iperf3](https://iperf.fr/) - Bandwidth testing
- [mtr](https://www.bitwizard.nl/mtr/) - Network diagnostics

---

<div align="center">

**⭐ If this project helped you, please consider giving it a star! ⭐**

Made with ❤️ for the VoIP community

[↑ Back to Top](#-dinstar-sme-ip-pbx-uc120-remote-access-setup)

</div>
