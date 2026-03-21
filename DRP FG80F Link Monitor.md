# DRP: Fortinet SD-WAN SLA Health Checks

## Setup

```
[FG]
├── wan1 --- [ISP1]
└── wan2 --- [ISP2]
```

- Configure wan1 and wan2 as SD-WAN members (enables SLA to switch between them)
- Add a static route to SD-WAN (ensures SD-WAN handles traffic)

## Goal

Switch to ISP2 when ISP1 connection fails entirely

## Configuration Steps

### Set SD-WAN Performance SLA

Navigate to: **SD-WAN > Performance SLAs**

Set SLA Target with the following parameters:

| Parameter | Value |
|-----------|-------|
| Latency Threshold | OFF |
| Jitter Threshold | OFF |
| Packet Loss Threshold | 100% |







## Diagnostics

### Check Health Status

```bash
diagnose sys sdwan health-check status
```

**Example Output:**

```
Health Check (Default_Google Search):

Seq(1 wan1): 
  - state: alive
  - packet-loss: 0.000%
  - latency: 27.686ms
  - jitter: 0.011ms
  - mos: 4.390
  - bandwidth-up: 999959 Kbps
  - bandwidth-dw: 999953 Kbps
  - bandwidth-bi: 1999912 Kbps
  - sla_map: 0x1

Seq(2 wan2):
  - state: dead
  - packet-loss: 100.000%
  - sla_map: 0x0
```

### Check SD-WAN Service Status

```bash
diagnose sys sdwan service
```

---


# DRP: Fortinet Link Monitor for Failover

## Setup

```
[FG]
├── wan1 --- [ISP1]
└── wan2 --- [ISP2]
```

## Goal

Switch to ISP2 when ISP1 fails using Fortigate Link Monitor

## Key Concepts

### Routing Database vs Routing Table

- **Routing Database (RIB)**: Contains all routes that are known to the system
- **Routing Table (FIB)**: Contains only the "best" routes (marked with `>`), which are actively used for packet forwarding
- The primary difference is that not all routes in the RIB are inserted into the FIB

### Route Prioritization

- Both default routes are configured with the **same Administrative Distance (AD)**, making both considered "best" simultaneously
- **Different Priorities** are used to prefer one over the other and prevent load balancing
- When the primary route is detected as dead or fails, it is removed from the Routing Table
- The next available route with the lowest priority automatically takes over

## Configuration Steps

### Prerequisites

⚠️ **Important**: Ensure the following before configuring:

1. Interfaces are **NOT** part of an SD-WAN
2. No policies are interfering with failover behavior

### Configure Static Routes

Navigate to: **Network > Static Routes > New**

#### Route 1 (Primary - ISP1)

| Parameter | Value |
|-----------|-------|
| Destination | 0.0.0.0/0.0.0.0 |
| Gateway Address | 41.224.54.80 |
| Interface | wan1 |
| Administrative Distance | 1 |













\[!pic2]



**CLI:**

```

\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*>get router info routing-table all    #Active routes\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*

Routing table for VRF=0

S\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*      0.0.0.0/0 \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\[1/0] via 41.224.54.80, wan1, \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\[1/0]

C       41.224.54.80/31 is directly connected, wan1

C       192.168.0.0/24 is directly connected, internal

```



```

\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*>get router info routing-table database #all routes, \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*=Active\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*



Routing table for VRF=0

S\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\*      0.0.0.0/0 \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\[1/0] via 41.224.54.80, wan1, \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\[1/0]

C       41.224.54.80/31 is directly connected, wan1

C       169.254.2.1/32 is directly connected, VPN Tunnel

C       192.168.0.0/24 is directly connected, internal

```





**Set up the Link Monitor:**



To know how to setup the link monitor, think of the exact route you want to target using: Destination \[route] \& first hop \[Gateway] \& exit interface, and apply this in the CLI:



**```**

config system link-monitor

    edit "WAN1\_Check"

        set srcintf "wan1"

        set server "8.8.8.8"

        set protocol ping

        set gateway-ip <Your\_WAN1\_Gateway\_IP>

        set interval 500

        set failtime 5

 	set service-detection disable

    next

end

```

```

diagnose sys link-monitor status

```





**A huge problem that caused me ache**, is DNS, Yes DNS. because after almost being fired (jk) for saying the failover is functional (which it is) but the office lost internet (not really )TWICE since I started working on the Failover mechanism, which never happened, but only when the backup modem looses internet, otherwise the failover executes flawlessly.



so what happened is: The FortiGate has "Override internal dns on wan2 (backup interface)", which means the it uses the wan2 modem's DNS server (modem's IP in this case) for all devices but wan1 doesn't have this option, so the modem with no internet's IP is now the DNS server of the Fortigate, thus of all LAN devices, which made it look like the internet is down for all the users.

