# 🌐 Enterprise Network Design & Implementation
### Cisco Packet Tracer | B.Tech Computer Networks & Cloud Security | UoVT


## 📋 Project Overview

This project demonstrates the **full design and implementation** of a secure, scalable enterprise network for a company with **600 staff** across a **3-floor building**, built entirely in **Cisco Packet Tracer 2025**.

The network follows a **3-tier hierarchical model** (Core → Distribution → Access) with redundancy at every layer and implements several industry-standard technologies.

---

## 🏗️ Network Topology

```
          [ISP1]──────────────[ISP2]
         Se0/3/0  195.136.17.x  Se0/3/1
             |                     |
        Se0/2/0               Se0/2/1
       [CORE-R1]─────────────[CORE-R2]        ← Core Layer (Cisco 2911)
       Gi0/0  Gi0/1         Gi0/0  Gi0/1
          \    /                \    /
    172.16.3.x/30          172.16.3.x/30
            \                    /
       [MLT-SW1]────────────[MLT-SW2]          ← Distribution Layer (Cisco 3650)
       Gi1/0/3-8             Gi1/0/3-8
           |                     |
    ┌──────┼──────────────────────┼──────┐
    │      │                      │      │
[SALES] [HR-SW]  [FINANCE] [ADMIN] [ICT] [SERVEROOM]  ← Access Layer (Cisco 2960)
VLAN10  VLAN20    VLAN30   VLAN40  VLAN50   VLAN60
```

---

## 🗂️ VLAN Configuration

| VLAN ID | Department | Switch | Network | Subnet Mask |
|---------|-----------|--------|---------|-------------|
| VLAN 10 | Sales & Marketing | SALES-SW | 172.16.1.0 | 255.255.255.128 /25 |
| VLAN 20 | HR & Logistics | HR-SW | 172.16.1.128 | 255.255.255.128 /25 |
| VLAN 30 | Finance & Accounts | FINANCE-SW | 172.16.2.0 | 255.255.255.128 /25 |
| VLAN 40 | Admin & Public Relations | ADMIN-SW | 172.16.2.128 | 255.255.255.128 /25 |
| VLAN 50 | ICT | ICT-SW | 172.16.3.0 | 255.255.255.128 /25 |
| VLAN 60 | Server Room | SERVERROOM-SW | 172.16.3.128 | 255.255.255.240 /28 |
| VLAN 99 | BlackHole (unused ports) | All Switches | N/A | N/A |

---

## ⚙️ Technologies Implemented

| Technology | Configuration Details |
|-----------|----------------------|
| **Hierarchical Design** | Core (2911 Routers) → Distribution (3650 MLT Switches) → Access (2960 Switches) |
| **VLANs** | 6 department VLANs + VLAN 99 BlackHole for unused ports |
| **Trunking** | IEEE 802.1Q — fa0/1-2 trunk on access switches, gig1/0/3-8 on MLT switches |
| **Inter-VLAN Routing** | SVIs on MLT-SW1 & MLT-SW2 with ip helper-address 172.16.3.130 |
| **OSPFv2** | Process ID 10, Area 0 — Router IDs: R1=3.3.3.3, R2=4.4.4.4, SW1=2.2.2.2, SW2=1.1.1.1 |
| **NAT/PAT** | PAT overload on Serial interfaces — ACL 1 permits all 172.16.x.x networks |
| **Dual ISP** | SEACOM (195.136.17.0/30) + Safaricom (195.136.17.4/30) with backup AD 70 |
| **DHCP** | Centralized server at 172.16.3.130 — 5 pools (one per department) |
| **SSH v2** | domain-name cisco.net + RSA 1024-bit — VTY transport input ssh only |
| **Port Security** | FINANCE-SW only — sticky MAC, maximum 1, violation shutdown |
| **Wireless** | WPA2-PSK Access Points per department — WPC300N card on laptops |
| **Static IPs** | Server Room: DHCP=.130, DNS=.131, Email=.132, SysAdmin=.133 |

---

## 📁 Repository Structure

```
📂 enterprise-network-design/
│
├── 📄 README.md
├── 📄 Enterprise_Network_FINAL.docx        ← Full documentation with screenshots
│
├── 📂 configs/
│   ├── ISP1_startup-config.txt
│   ├── ISP2_startup-config.txt
│   ├── CORE-R1_startup-config.txt
│   ├── CORE-R2_startup-config.txt
│   ├── MLT-SW1_startup-config.txt
│   ├── MLT-SW2_startup-config.txt
│   ├── SALES_startup-config.txt
│   ├── HR_startup-config.txt
│   ├── FINANCE_startup-config.txt
│   ├── ADMIN_startup-config.txt
│   ├── ICT_startup-config.txt
│   └── SERVERROOM_startup-config.txt
│
└── 📂 screenshots/
    └── PROJECT_SCREENSHOT.png
```

---

## 🌐 IP Addressing

### Point-to-Point Backbone Links (/30)

| Link | Network | Router IP | Switch IP |
|------|---------|-----------|-----------|
| CORE-R1 ↔ MLT-SW1 | 172.16.3.144/30 | 172.16.3.146 | 172.16.3.145 |
| CORE-R1 ↔ MLT-SW2 | 172.16.3.152/30 | 172.16.3.154 | 172.16.3.153 |
| CORE-R2 ↔ MLT-SW1 | 172.16.3.148/30 | 172.16.3.150 | 172.16.3.149 |
| CORE-R2 ↔ MLT-SW2 | 172.16.3.156/30 | 172.16.3.158 | 172.16.3.157 |

### WAN Public IP Links (ISP)

| Link | Network | CORE-R IP | ISP IP |
|------|---------|-----------|--------|
| CORE-R1 ↔ ISP1 | 195.136.17.0/30 | 195.136.17.1 | 195.136.17.2 |
| CORE-R1 ↔ ISP2 | 195.136.17.4/30 | 195.136.17.5 | 195.136.17.6 |
| CORE-R2 ↔ ISP1 | 195.136.17.8/30 | 195.136.17.9 | 195.136.17.10 |
| CORE-R2 ↔ ISP2 | 195.136.17.12/30 | 195.136.17.13 | 195.136.17.14 |

---

## 🔐 Security Features

```
✅ SSH v2 only on all VTY lines (Telnet blocked)
✅ RSA 1024-bit encryption keys
✅ Port Security on Finance-SW (sticky MAC + shutdown violation)
✅ service password-encryption on all devices
✅ VLAN segmentation — departments isolated from each other
✅ VLAN 99 BlackHole — all unused switch ports dead-ended
✅ PAT — internal IPs hidden behind public IP
✅ WPA2-PSK on all wireless access points
✅ Banner MOTD warning on all devices
```

---

## 📌 Key Configuration Snippets

### OSPF on CORE-R1
```
router ospf 10
 router-id 3.3.3.3
 network 172.16.3.144 0.0.0.3 area 0
 network 172.16.3.152 0.0.0.3 area 0
 network 195.136.17.0 0.0.0.127 area 0
ip route 0.0.0.0 0.0.0.0 Serial0/2/0
ip route 0.0.0.0 0.0.0.0 Serial0/2/1 70
```

### Inter-VLAN Routing on MLT-SW1
```
ip routing
interface vlan 10
 ip address 172.16.1.1 255.255.255.128
 ip helper-address 172.16.3.130
interface vlan 20
 ip address 172.16.1.129 255.255.255.128
 ip helper-address 172.16.3.130
```

### PAT on CORE-R1
```
access-list 1 permit 172.16.1.0 0.0.0.127
access-list 1 permit 172.16.1.128 0.0.0.127
access-list 1 permit 172.16.2.0 0.0.0.127
access-list 1 permit 172.16.2.128 0.0.0.127
access-list 1 permit 172.16.3.0 0.0.0.127
access-list 1 permit 172.16.3.128 0.0.0.15
ip nat inside source list 1 interface Serial0/2/0 overload
ip nat inside source list 1 interface Serial0/2/1 overload
```

### Port Security on FINANCE-SW
```
interface range fa0/3-24
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
```

### SSH v2 on CORE-R1
```
ip domain-name cisco.net
username admin password cisco
crypto key generate rsa  ! modulus 1024
ip ssh version 2
line vty 0 15
 login local
 transport input ssh
```

---

## 🧪 Testing Results

| Test | Method | Result |
|------|--------|--------|
| Inter-VLAN Connectivity | Ping between departments | ✅ Pass |
| DHCP Auto-Assignment | IP auto-assigned to all PCs | ✅ Pass |
| SSH Remote Access | SSH from SysAdmin PC to routers | ✅ Pass |
| OSPF Adjacency | show ip ospf neighbor | ✅ Pass |
| NAT/PAT Translation | show ip nat translations | ✅ Pass |
| Port Security (Finance) | show port-security | ✅ Pass |
| Wireless Connectivity | Laptop/Tablet connect to APs | ✅ Pass |
| Dual ISP Redundancy | Primary/backup route testing | ✅ Pass |

---

## ⚠️ Troubleshooting Encountered

### 1. Port Security "Disabled" Status
- **Issue:** `show port-security` returned empty — status showed `Disabled`
- **Root Cause:** Dual-execution required on large interface ranges in Packet Tracer
- **Fix:** Re-applied `switchport mode access` + `switchport port-security` explicitly

### 2. Empty NAT Translation Table
- **Issue:** `show ip nat translations` returned empty — pings timed out
- **Root Causes:** Missing `ip nat inside` tags + incorrect wildcard masks + no reverse routes on ISP
- **Fix:** Re-declared NAT boundaries, widened ACL, added static reverse routes on ISP routers

### 3. OSPF Router-ID Not Taking Effect
- **Issue:** OSPF neighbors failed to form FULL adjacency
- **Root Cause:** Router-ID was auto-selected before manual assignment
- **Fix:** `clear ip ospf process` → forced reload of OSPF with correct router-IDs

---

## 🎓 About

| | |
|---|---|
| **Student** | B.Tech in Computer Networks & Cloud Security |
| **University** | University of Vocational Technology (UoVT), Sri Lanka |
| **Simulation Tool** | Cisco Packet Tracer 2025 |
| **Base Network** | 172.16.1.0 |
| **Year** | 2026 |

---

