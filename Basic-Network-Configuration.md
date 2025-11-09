# Networking Basic Configuration
This project demonstrates the configuration of a Cisco 2911 router (RouterDoInferno) with three subnets (10.255.255.0/24, 172.31.255.0/24, 192.168.29.0/24) and DHCP for IP management. 
The network connects multiple PCs and a laptop via switches, as per the provided diagram.
  
![alt text](https://github.com/jmoreira01/Network-Cybersecurity-Course-Portfolio/blob/main/topology.webp)

## Network Overview

**Router**: RouterDoInferno (Cisco 2911)

**Interfaces**:
- GigabitEthernet0/0: 10.255.255.1/24 (to Fa0/5 switch)
- GigabitEthernet0/1: 172.31.255.1/24 (to Fa0/4 switch)
- GigabitEthernet0/2: 192.168.29.1/24 (to Fa0/3 switch)

**Devices**: PCs (PC1 to PC12) and Laptop-PT across switches.

**Diagram Reference**: Star topology with router at center.

## Initial Configuration
```
> enable - Access Privileged EXEC mode.
> configure terminal - Enter Global Configuration mode.
> hostname RouterDoInferno - Rename the device.
> enable secret MYEnablePassword - Set Privileged EXEC password.
> line console 0 - Configure console login.
    > password MyConsolePassword
    > login
    > exit
> service password-encryption - Encrypt passwords.
> banner motd #
  > " THIS IS A CONSOLE BANNER #" - Add banner
```
## Interface GigabitEthernet0/0 (Subnet 10.255.255.0/24) configuration
```
> interface GigabitEthernet0/0 - Configure first subnet.
    > ip address 10.255.255.1 255.255.255.0
    > no shutdown
    > exit
```
## Set up DHCP for 10.255.255.0/24 - "ESQUERDA"
```
> ip dhcp pool Esquerda - Set up DHCP for 10.255.255.0/24.
    > network 10.255.255.0 255.255.255.0
    > default-router 10.255.255.1
    > exit
```
## Interface GigabitEthernet0/1 (Subnet 172.31.255.0/24) configuration
```
> interface GigabitEthernet0/1 - Configure second subnet.
    > ip address 172.31.255.1 255.255.255.0
    > no shutdown
    > exit
```
## Set up DHCP for 10.255.255.0/24 - "MEIO"
```
> ip dhcp pool Meio - Set up DHCP for 172.31.255.0/24.
    > network 172.31.255.0 255.255.255.0
    > default-router 172.31.255.1
    > exit
```
## Interface GigabitEthernet0/2 (Subnet 192.168.29.0/24) configuration
```
> interface GigabitEthernet0/2 - Configure third subnet.
    > ip address 192.168.29.1 255.255.255.0
    > no shutdown
    > exit
```
## Set up DHCP for 10.255.255.0/24 - "DIREITA"
```
> ip dhcp pool Direita - Set up DHCP for 192.168.29.0/24.
    > network 192.168.29.0 255.255.255.0
    > default-router 192.168.29.1
    > exit
```
## Checking configuration 
```
> show running-config - Verify configuration.
```

**At the end**:

* DHCP Requesting - IP configuration > Desktop > IP Configuration > Request DHCP

## 📚 CESAE DIGITAL – Centro para o Desenvolvimento de Competências Digitais
 - Course: Network & Cyber-Security Administrator
 - Unit: IP Addressing e Network Infrastructure
 - Instructor: Pedro Pereira
