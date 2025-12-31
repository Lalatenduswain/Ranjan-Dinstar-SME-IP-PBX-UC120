# Cloudflare Tunnel (cloudflared) on Dinstar UC120 IP PBX

## Compatibility Assessment Report

**Date:** December 31, 2025
**Device:** Dinstar SME IP PBX UC120
**Software:** Cloudflare Tunnel Client (cloudflared)

---

## Executive Summary

Installing the official Cloudflare Tunnel client (cloudflared) on the Dinstar UC120 IP PBX is **likely not possible** due to architecture limitations. The UC120 is an embedded Linux device that probably uses a MIPS or ARM processor, while Cloudflare's official binaries have limited embedded device support.

---

## 1. Dinstar UC120 Specifications

| Specification | Details |
|---------------|---------|
| **Product Type** | SME IP PBX (VoIP Phone System) |
| **Target Users** | Small to Medium Enterprises |
| **Max Users** | Up to 60 users |
| **Operating System** | Linux-based (embedded) |
| **CPU Architecture** | Not publicly documented (likely ARM or MIPS) |
| **Interfaces** | FXO, FXS, GSM/VoLTE, VoIP/SIP |

### Key Characteristics
- Embedded Linux system with limited storage and RAM
- Proprietary firmware managed by Dinstar
- Designed for telephony, not general-purpose computing
- Larger models (UC1500) use x86, but UC120 likely uses embedded ARM/MIPS

---

## 2. Cloudflared Supported Platforms

### Officially Supported Operating Systems
- Linux
- Windows
- macOS
- Docker

### Officially Supported Linux Architectures

| Architecture | Binary Name | Status |
|--------------|-------------|--------|
| x86-64 (amd64) | cloudflared-linux-amd64 | Supported |
| x86 (32-bit) | cloudflared-linux-386 | Supported |
| ARM64 | cloudflared-linux-arm64 | Supported |
| ARM (32-bit) | cloudflared-linux-arm | Supported |
| **MIPS** | N/A | **NOT Supported** |

### Version Support Policy
Cloudflare supports versions of cloudflared released within the last 12 months. Older versions may encounter breaking changes.

---

## 3. Compatibility Analysis

### Scenario A: UC120 Uses ARM Processor
- **Possibility:** Partial compatibility
- **Challenges:**
  - Limited storage space on embedded device
  - May lack required system libraries (glibc version)
  - Firmware updates could overwrite installation
  - No package manager access

### Scenario B: UC120 Uses MIPS Processor
- **Possibility:** Not compatible with official binaries
- **Workaround:** Community-built MIPS binary exists but is unofficial
- **Risks:**
  - Unofficial build may have security vulnerabilities
  - No guaranteed updates or support
  - Based on older cloudflared version (2024.1.5)

### Additional Concerns
1. **Warranty:** Installing third-party software may void device warranty
2. **Stability:** Could interfere with PBX operations
3. **Updates:** Dinstar firmware updates may remove custom installations
4. **Resources:** Limited CPU/RAM may impact PBX performance

---

## 4. How to Check Your Device Architecture

If you have SSH access to your UC120, run the following commands:

```bash
# Check CPU architecture
uname -m

# View detailed CPU information
cat /proc/cpuinfo

# Check available storage
df -h

# Check available memory
free -m

# Check Linux distribution/version
cat /etc/os-release
```

### Interpreting Results

| uname -m Output | Architecture | Cloudflared Support |
|-----------------|--------------|---------------------|
| x86_64 | 64-bit Intel/AMD | Supported |
| i686, i386 | 32-bit Intel/AMD | Supported |
| aarch64 | 64-bit ARM | Supported |
| armv7l, armv6l | 32-bit ARM | Supported |
| mips, mipsel | MIPS | NOT Supported |

---

## 5. Alternative Approaches

### Option 1: External Tunnel Host (Recommended)

Deploy cloudflared on a separate device that tunnels traffic to your UC120.

**Suitable Devices:**
- Raspberry Pi (ARM64/ARM)
- Mini PC (x86)
- Docker container on NAS
- Virtual Machine
- Cloud instance

**Architecture:**
```
Internet <-> Cloudflare <-> cloudflared (Pi/VM) <-> UC120 PBX
```

**Advantages:**
- No modification to PBX
- Maintains warranty
- Easy to update and manage
- Survives PBX firmware updates

### Option 2: Router-Based Tunnel

If using OpenWrt-compatible router:
- Install cloudflared on router (if architecture supported)
- Route UC120 traffic through tunnel

### Option 3: Reverse Proxy

Deploy a reverse proxy (nginx/Caddy) with cloudflared on a separate host:
- Proxy forwards SIP/HTTP traffic to UC120
- More control over traffic handling

---

## 6. Recommended Solution

**Use a Raspberry Pi as a Cloudflare Tunnel endpoint:**

1. Purchase Raspberry Pi 4 or Pi Zero 2 W
2. Install Raspberry Pi OS (64-bit)
3. Install cloudflared:
   ```bash
   curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64 -o /usr/local/bin/cloudflared
   chmod +x /usr/local/bin/cloudflared
   cloudflared service install
   ```
4. Configure tunnel to route traffic to UC120's IP address
5. Connect Pi to same network as UC120

---

## 7. References

- [Dinstar UC120 Official Page](https://www.dinstar.com/soho-sme-voip-pbx/uc120/)
- [Cloudflare Tunnel Documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [Cloudflared Downloads](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/downloads/)
- [Cloudflared GitHub Releases](https://github.com/cloudflare/cloudflared/releases)
- [Community MIPS Build (Unofficial)](https://github.com/aladdinAlmarashly/Cloudflare-Tunnel-Client-for-Arch-MIPS)

---

## 8. Conclusion

Direct installation of cloudflared on the Dinstar UC120 is **not recommended** due to:
- Unknown/unsupported CPU architecture
- Limited system resources
- Risk to PBX stability
- Warranty concerns

The recommended approach is to deploy cloudflared on a separate device (Raspberry Pi, VM, or container) that acts as a tunnel endpoint for your UC120 PBX.

---

*Document generated for technical assessment purposes.*
