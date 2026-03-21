# Fortinet FortiGate Configuration Guide

## Resources

- [FortiGate Administration Guide v7.2.9](https://docs.fortinet.com/document/fortigate/7.2.9/administration-guide/954635/getting-started)

## Connectivity & Access

### Serial Access
- Connect via console or USB to access FortiGate device

### External Monitoring
- API calls can be made for external monitoring

---

## Traffic Control Policies

### Implicit vs Explicit Denial

| Type | Behavior |
|------|----------|
| **Explicit Deny** | All traffic implicitly allowed unless explicitly denied |
| **Implicit Deny** | All traffic implicitly denied unless explicitly allowed |

> **FortiGate Default**: Uses **Implicit Deny** → Allows Nothing by default

---

## Firewall Features

### WAN Management

#### SD-WAN (Smart Data Plane WAN)
- Group of WAN connections with intelligent traffic steering
- Use cases:
  - **Backup ISP**: Add wan2 as backup without policy changes
  - **Link Monitoring**: Monitor multiple WANs with granular metrics
  - **Metrics**: Speed, jitter, latency, packet loss, bandwidth
  - **SNMP OID**: `1.3.6.1.4.1.12356.101.4.9.2.1`

#### Link Monitor
- Simple UP/DOWN monitoring of connections
- Monitor links to other servers (proprietary or public)
- **SNMP OID**: `1.3.6.1.4.1.12356.101.4.8.2.1.2`
```bash
config system link-monitor
end
```

### Web Filtering

#### With FortiGuard License
- Block cloud storage by category (e.g., "Cloud Storage", "File Sharing")
- Uses FortiGuard Category Based Filter

#### Without FortiGuard License
- Block specific domains/URLs (e.g., drive.google.com, dropbox.com)
- Block applications using Application Control
- Block via Static URL Filter or DNS Filter

### Network Automation & Security

- **Automations**: Cloud service providers, email, Teams, scripts, HTTP requests (in/out)
- **IP-MAC Binding**: Prevent MAC/IP spoofing
- **NAC (Network Access Control)**: Control per MAC address or per OS
- **DNS Filter**: Block specific websites
- **WAN Failover**: Backup ISP with health checks (ping-based)

### WAN Priority

- Uses Administrative Distance (AD) for route preference
- **Examples**:
  - `wan1 distance < wan2 distance` → wan2 is backup
  - Equal distance with different priorities → selective failover

---

## Interfaces

### VPN Interfaces
- **ssl.root**: SSL VPN Gateway
- **naf.root**: Network Analysis Framework for internal traffic processing
- **l2t.root**: L2TP VPN Gateway (Layer 2 Tunneling Protocol) VPN connections
- **VPN Tunnel**: IPsec VPN connections

---

## User & Administrator Management

### Access Types
- **Administrators**: Access Dashboard for device management
- **Users**: Access network/VPN resources

### Deletion Constraints
- ⚠️ Cannot delete a user if they are referenced in policies or groups

---

## Firewall Architecture

### NGFW (Next-Generation Firewall)
- Controls traffic flow at packet level
- Provides granular inspection and control

### FortiGate Hybrid Mesh Firewall (HMF)
- Centralized control platform
- Unifies deployments across infrastructure
- Unified policy management

---

## Authentication & Access Control

### Captive Portal

Users must authenticate to access internet. **Methods**:

#### 1. MAC-based Access
- Only allowed MAC addresses can connect

#### 2. OS-based Access
- Restrict access by operating system

#### 3. Credentials + MAC Binding
```
User:  john.doe
Allowed MACs:
  - 34:8A:AE:xx:xx:xx   (Laptop)
  - 7C:10:C9:xx:xx:xx   (Phone - company-owned)
```
- User cannot login with a MAC different from authorized ones

### Login URL Configuration
- **FQDN (Fully Qualified Domain Name)**: Redirect users to `https://login.yourcompany.com` (requires valid SSL certificate)
- **IP Address**: Alternative (less recommended)

### ZTNA (Zero Trust Network Access)
- Per-application access control
- Tagged IP-MAC user identification
- [ZTNA Documentation](https://docs.fortinet.com/document/fortigate/7.6.6/administration-guide/855420/zero-trust-network-access-introduction)

---

## VPN Configuration

### Split Tunneling

#### ON (Split Tunneling Enabled)
- **Local destination** traffic → Routes through VPN
- **Internet destination** traffic → Routes through user's ISP router
- **Use case**: Lower bandwidth consumption

#### OFF (All Traffic through VPN)
- All traffic → Routes through VPN
- **Use case**: High security, higher bandwidth required

### VPN User Management

- VPN users receive an IP from the **Client Address Range**
- FortiGate routes traffic from VPN users to **Local Address networks** via the local interface

### VPN Best Practices

#### Client Configuration Options

| Option | Purpose |
|--------|---------|
| **Save Password** | Store credentials locally - users don't need to re-enter on each connection |
| **Auto Connect** | Initiate VPN connection automatically on system startup or when internet is detected |
| **Always Up (Keep Alive)** | Prevent disconnection due to inactivity; auto-reconnect if link drops |

### Additional VPN Features
- **Policy Expiration**: Set automatic expiration on VPN policies
- **Policy Types**: IP-based, user-based, MAC-based, or service-based policies

---

## Firewall Modes & Inspection

### NGFW Modes

#### Profile-Based Mode
- Routes to profiles then to policies
- **Performance**: High (fast)
- **Inspection Level**: Layer 7
- **Trade-off**: Lower inspection depth

#### Policy-Based Mode
- Granular per-URL/per-app control
- SNAT enabled for this mode

### Inspection Modes

#### Flow-Based Inspection
- **Speed**: Ultra-fast, per-packet inspection
- **Best for**: Low-latency environments
- **Trade-off**: Less granular security

#### Proxy-Based Inspection
- **Speed**: Slower (multiple packets per file)
- **Granularity**: High control (e.g., block specific YouTube channels)
- **Best for**: High-security, high-control environments

---

## NAT Configuration

### IP Pool Configuration

#### Use Outgoing Interface Address
- Translates all private IPs to same public IP (simple NAT)

#### Randomized Pool
- Distributes traffic across multiple public IPs

### Source Port Preservation
- **Preserve Source Port**: Keeps same port for private and public IP
- **Use case**: Legacy applications requiring specific ports (e.g., VoIP)

---

## Health Checks & Monitoring

- **Health Check**: Enabled (ON) or Disabled (OFF)
- Monitors link status for automatic failover decisions

---

## Licensing & Features

### Free FortiGate Features
- Basic Firewall functionality
- SSL VPN
- IPsec VPN
- FortiClient VPN (VPN-only mode)

### License-Dependent Features
- Advanced threat protection
- Web filtering (FortiGuard)
- Advanced features

### DNS Configuration

#### Static IP
- Recommended for VPN deployments
- Direct IP configuration

#### Dynamic IP
- Use **Dynamic DNS** if IP changes frequently
- Configure domain name instead of IP address

---

## Routing

### View Routing Information

#### Routing Table (FIB)
```bash
get router info routing-table all
```
Shows currently active routes used for forwarding decisions.

#### Routing Database (RIB)
```bash
get router info routing-table database
```
Shows all known routes (active and inactive).

### Static Route Failover

- **Configuration**: Two static routes with same Administrative Distance but different priorities
- **Failover Trigger**: When physical link fails
- **Result**: Next available route with lowest priority automatically takes over
