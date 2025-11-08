# 🌐 Network & Cybersecurity Administrator - Practical Lab

## 📋 Project Overview
This project demonstrates a multi-VLAN network setup using Cisco Packet Tracer, VLSM for efficient IP addressing, VTP for VLAN propagation, inter-VLAN routing via subinterfaces, DHCP services, and security configurations like SSH access and trunk port hardening. 
The network includes four VLANs for different departments (Admin, Accounts, Commercial, Management).

- **Tools Used**: Cisco Packet Tracer v8.2
- **Key Features**:
  - VLSM subnetting from base address 172.18.0.0/16
  - VTP domain: cesae.local
  - DHCP on router for client VLANs (excluding management)
  - Isolated VLAN 40 (Management) – no inter-VLAN communication
  - DNS/HTTP server on external link (209.165.100.0/24)
  - SSH access

![alt text]([https://github.com/jmoreira01/Network-Cybersecurity-Course-Portfolio/blob/main/topology.webp](https://github.com/jmoreira01/Networking/blob/main/pratical_lab_topology.png))

## 🏗️ Network Topology
- **Router (RCENTRAL)**: Connected to SWCENTRAL via G0/0 (trunk for subinterfaces); G0/1 to external server.
- **SWCENTRAL**: Central switch, VTP server, trunk links to four access switches and router.
- **Access Switches**: SW-LEFT1, SW-LEFT2 (left side), SW-RIGHT1, SW-RIGHT2 (right side) – All VTP clients, with PCs/laptops in respective VLANs.
- **End Devices**: PCs in VLANs 10/20/30; Management laptop in VLAN 40; DNS/HTTP server externally.
- **Connections**: All inter-switch and switch-router links are trunks (native VLAN 40, no DTP).

### VLAN Architecture
| VLAN ID | Name       | Purpose        | Host Requirements |
|---------|------------|----------------|-------------------|
| 10      | ADMIN      | Administration | 2048 hosts        |
| 20      | ACCOUNT    | Accounting     | 515 hosts         |
| 30      | COMERCIAL  | Commercial     | 30 hosts          |
| 40      | MANAGEMENT | Network Mgmt   | 6 hosts           |

---

## 🔢 VLSM Addressing Scheme - 172.18.0.0/16

### IP Address Allocation Table

| VLAN | Network Name | Hosts | Subnet/Mask | Network Address | Usable IP Range             | Broadcast     | Default Gateway |
|------|--------------|-------|-------------|-----------------|-----------------------------|---------------|-----------------|
| 10   | ADMIN        | 2048  | /20         | 172.18.0.0      | 172.18.0.1 - 172.18.15.254  | 172.18.15.255 | 172.18.0.1      |
| 20   | ACCOUNT      | 515   | /22         | 172.18.16.0     | 172.18.16.1 - 172.18.19.254 | 172.18.19.255 | 172.18.16.1     |
| 30   | COMERCIAL    | 30    | /27         | 172.18.20.0     | 172.18.20.1 - 172.18.20.30  | 172.18.20.31  | 172.18.20.1     |
| 40   | MANAGEMENT   | 6     | /29         | 172.18.20.32    | 172.18.20.33 - 172.18.20.38 | 172.18.20.39  | 172.18.20.33    |

### Subnet Mask Reference
- **VLAN 10:** 255.255.240.0 (/20) - 4094 hosts
- **VLAN 20:** 255.255.252.0 (/22) - 1022 hosts
- **VLAN 30:** 255.255.255.224 (/27) - 30 hosts
- **VLAN 40:** 255.255.255.248 (/29) - 6 hosts

### Management IPs (VLAN 40)**:
- **SWCENTRAL:**  172.18.20.34  | 255.255.255.248  | 172.18.20.33
- **SW-LEFT1:**   172.18.20.35  | 255.255.255.248  | 172.18.20.33
- **SW-LEFT2:**   172.18.20.36  | 255.255.255.248  | 172.18.20.33
- **SW-RIGHT1:**  172.18.20.37  | 255.255.255.248	| 172.18.20.33
- **SW-RIGHT2:**  172.18.20.38  | 255.255.255.248	| 172.18.20.33
- **Management:** 172.18.20.33 (static, for SSH access)
---

## ⚙️ Configuration Guide
### Basic Configurations
#### Hostnames
```bash
! Router
hostname RCENTRAL

! Switches
hostname SWCENTRAL
hostname SW-LEFT1
hostname SW-LEFT2
hostname SW-RIGHT1
hostname SW-RIGHT2
```
#### Security Settings
```bash
! Router and Switch security
enable secret cesae123
banner motd #
SWCENTRAL ACESSO RESTRITO! #
line console 0 
password cisco
login
service password-encryption
```
### VTP Configuration
#### VTP Server (Central Switch)
```bash
vtp mode server
vtp domain cesae.local
vtp password cesae
```
#### VTP Client (All other Switchs)
```bash
vtp mode client
vtp domain cesae.local
vtp password cesae
```
#### VLAN Creation (VTP Server only)
```bash
vlan 10
 name ADMIN
vlan 20
 name ACCOUNT
vlan 30
 name COMERCIAL
vlan 40
 name MANAGEMENT
```
### Mode Access Port | Trunk Configuration
#### Trunk Configuration (SWCENTRAL)
```bash
! Connections to SW-LEFT1, SW-LEFT2, SW-RIGHT1, SW-RIGHT2
interface range f0/1-5
 switchport mode trunk
 switchport trunk native vlan 40
 switchport nonegotiate
 no shutdown
```
#### Trunk Configuration (Distribution Switches)
```bash
! On each distribution switch
interface f0/1
 switchport mode trunk
 switchport trunk native vlan 40
 switchport nonegotiate
 no shutdown
```
#### Mode Access Port Configurations
```bash
! Assign access ports to respective VLANs
interface range f0/2-4
 switchport mode access
 switchport access vlan !10 or 20 or 30
 no shutdown
```
### Router-on-a-Stick Configuration 
#### Sub-Interfaces on Router
```bash
interface giga0/0.10
 encapsulation dot1q 10
 ip address 172.18.0.1 255.255.240.0

interface giga0/0.20
 encapsulation dot1q 20
 ip address 172.18.16.1 255.255.252.0

interface giga0/0.30
 encapsulation dot1q 30
 ip address 172.18.20.1 255.255.255.224

#VLAN 40 Without Configuration
```
### DHCP Configuration
#### IP Exclusions
```bash
ip dhcp excluded-address 172.18.0.1
ip dhcp excluded-address 172.18.16.1
ip dhcp excluded-address 172.18.20.1
```
#### DHCP Pools
```bash
ip dhcp pool VLAN10
 network 172.18.0.0 255.255.240.0
 default-router 172.18.0.1
 dns-server 209.165.100.254
 domain-name cesae.local

ip dhcp pool VLAN20
 network 172.18.16.0 255.255.252.0
 default-router 172.18.16.1
 dns-server 209.165.100.254
 domain-name cesae.local

ip dhcp pool VLAN30
 network 172.18.20.0 255.255.255.224
 default-router 172.18.20.1
 dns-server 209.165.100.254
 domain-name cesae.local
```
#### DNS/HTTP Server Configuration
- Server IP: 209.165.100.254/24 (static)
- DNS Service: Add A record – cesae.local → 209.165.100.254
- HTTP Service: Enable with a simple index.html page for cesae.local domain

### SSH CONFIGURATION (All Switches)
```bash
username admin secret class
ip domain-name cesae.local
crypto key generate rsa 2048
ip ssh time-out 15
ip ssh authentication-retries 4
line vty 0 15
 transport input ssh
 login local
 exec-timeout 5 0
exit
```

### Laptop for Switch Management
**Static IP:** 172.18.20.33
**Purpose:** Exclusive SSH access to switches via VLAN 40

### Verification Commands
```bash
! VTP and VLAN Verification
show vtp status
show vlan brief

! Interface and Trunk Verification
show ip interface brief
show interfaces trunk
show interfaces status

! DHCP and Routing Verification
show ip dhcp binding

! SSH and Management Verification
show ssh
show users
show running-config
```
---

## ✅ Testing Procedures

#### VLAN Connectivity Test

 - Ping between VLANs 10, 20, 30 (should work)

 - Ping from VLAN 40 to other VLANs (should fail)

#### DHCP Functionality

 - Connect devices to VLANs 10, 20, 30 and verify automatic IP assignment

#### DNS Resolution

 - nslookup cesae.local should resolve to 209.165.100.254

#### SSH Access

 - SSH from management laptop (VLAN 40) to switches

 - Verify SSH is blocked from other VLANs

#### Web Service

 - Access http://cesae.local from any VLAN (except 40)

---

 ## 🎯 Implementation Checklist

  - VLSM addressing scheme implemented

  - VTP domain configured with server and clients

  - VLANs created and propagated

  - Router sub-interfaces configured

  - VLAN 40 isolation

  - DHCP services configured

  - DNS and HTTP services operational

  - Trunk ports with DTP disabled

  - Native VLAN 40 configured

  - Switch management IPs assigned

  - SSH access restricted to VLAN 40

  - Security configurations applied

  - All connectivity tests passed


---

## 📚 CESAE DIGITAL – Centro para o Desenvolvimento de Competências Digitais
 - Course: Network & Cyber-Security Administrator
 - Unit: Networking Models & Protocols
 - Instructor: Hugo Viana
