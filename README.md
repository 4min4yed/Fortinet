# Fortinet Security Administration Repository

![Fortinet](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRGayrhUDuz0G2XoHTDrDArDpSj03QQHRTTFw&s)

## Overview

This repository contains comprehensive documentation and configuration guides for Fortinet security infrastructure, including FortiGate firewall setup, disaster recovery procedures, and monitoring solutions.

## Contents

###  Fortinet FortiGate Documentation

- **[FW-setup.md](FW-setup.md)** - Complete Fortinet FortiGate configuration guide covering:
  - Firewall features and WAN management
  - SD-WAN and Link Monitor setup
  - VPN configuration and best practices
  - Authentication and access control (Captive Portal, ZTNA)
  - NAT configuration
  - Routing and failover strategies
  - Licensing and advanced features

###  Disaster Recovery Procedures

- **[DRP FG80F Link Monitor.md](DRP%20FG80F%20Link%20Monitor.md)** - Link Monitor failover configuration for FG80F:
  - Setup for WAN failover between ISPs
  - Static route configuration with prioritization
  - Routing Table (FIB) vs Routing Database (RIB) concepts
  - Diagnostics and health check commands

###  Network Monitoring

- **[NAGIOS UBUNTU SETUP.md](NAGIOS%20UBUNTU%20SETUP.md)** - Step-by-step guide for setting up NAGIOS monitoring on Ubuntu:
  - SSH server installation
  - Apache2 configuration
  - Nagios installation and compilation
  - Web dashboard setup with authentication

## Key Features Documented

### WAN Management
- **SD-WAN**: Intelligent traffic steering with performance SLA monitoring
- **Link Monitor**: Simple UP/DOWN monitoring for automatic failover
- **WAN Failover**: Backup ISP configuration using administrative distance and priorities

### Security
- Implicit Deny firewall posture
- IP-MAC Binding and spoofing prevention
- Web filtering (FortiGuard and alternatives)
- NAC (Network Access Control) per MAC/OS
- DNS filtering

### VPN & Remote Access
- Split tunneling configuration
- SSL/IPsec VPN setup
- Captive Portal authentication
- Zero Trust Network Access (ZTNA)
- FortiClient VPN configuration

### Network Monitoring
- NAGIOS service monitoring
- Health check status verification
- SD-WAN SLA monitoring
- Route failover diagnostics

## Quick Start

### FortiGate Firewall
1. Review [FW-setup.md](FW-setup.md) for general concepts and features
2. Follow [DRP FG80F Link Monitor.md](DRP%20FG80F%20Link%20Monitor.md) for WAN failover setup

### Monitoring Setup
1. Follow [NAGIOS UBUNTU SETUP.md](NAGIOS%20UBUNTU%20SETUP.md) for complete installation steps
2. Access dashboard at `http://your-server-ip/nagios`

## Resources

- [Fortinet FortiGate Administration Guide v7.2.9](https://docs.fortinet.com/document/fortigate/7.2.9/administration-guide/954635/getting-started)
- [FortiGate ZTNA Documentation v7.6.6](https://docs.fortinet.com/document/fortigate/7.6.6/administration-guide/855420/zero-trust-network-access-introduction)

---

**Last Updated**: March 2026

