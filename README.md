# DHCP Server setup with Inter-VLAN Routing
## Download Packet Tracer LAb
[Download Part 3: DHCP Server Setup with Inter-VLAN Routing](DHCP%20Sever%20Setup%20w%20Inter%20VLAN%20Routing.pkt)

## Objectives
- This setup will allow you to understand how devices in a network can automatically
 obtain IP addresses and other network configuration details from a DHCP server

## Software Requirements
- Cisco Packet Tracer

---

## Lab Setup



<p align="center">
  <img src="https://i.postimg.cc/437x9kxy/DHCPServersetupwith-Inter-VLANRouting.png" alt="DHCP Server Setup with Inter-VLAN Routing"/>
</p>
# VLAN and Inter-VLAN Routing Setup in Cisco Packet Tracer



Step 1: Add Devices to the Workspace
- Open Cisco Packet Tracer.
- From the End Devices section, drag and drop:
  - PCs for each VLAN/subnet (e.g., three PCs)
  - A Server to act as the DHCP server
- From the Network Devices section, drag and drop:
  - A Router (e.g., 2911 or 1941)
  - A Switch (e.g., 2960)

Step 2: Physically Connect Devices
- Click on the cable icon and select a copper straight-through cable.
- Connect devices as follows:
  - Router (GigabitEthernet 0/0) to Switch (FastEthernet 0/1)
  - Each PC to the Switch
  - Server to the Switch

Step 3: Configure VLANs on the Switch
- Access the Switch CLI:
  - Switch> enable
  - Switch# configure terminal
- Create VLANs:
  - Switch(config)# vlan 10
  - Switch(config-vlan)# name Sales
  - Switch(config)# vlan 20
  - Switch(config-vlan)# name IT
  - Switch(config)# vlan 30
  - Switch(config-vlan)# name HR
- Assign VLANs to Ports:
  - Switch(config)# interface range FastEthernet 0/2-5
    - switchport mode access
    - switchport access vlan 10
  - Switch(config)# interface range FastEthernet 0/6-10
    - switchport mode access
    - switchport access vlan 20
  - Switch(config)# interface range FastEthernet 0/11-15
    - switchport mode access
    - switchport access vlan 30
- Configure Trunk Port for Router:
  - Switch(config)# interface FastEthernet 0/1
    - switchport mode trunk
    - switchport trunk allowed vlan all

Step 4: Configure the Router for Inter-VLAN Routing
- Access the Router CLI:
  - Router> enable
  - Router# configure terminal
- Configure Sub-Interfaces for Each VLAN:
  - Router(config)# interface GigabitEthernet0/0.10
    - encapsulation dot1Q 10
    - ip address 192.168.10.1 255.255.255.0
  - Router(config)# interface GigabitEthernet0/0.20
    - encapsulation dot1Q 20
    - ip address 192.168.20.1 255.255.255.0
  - Router(config)# interface GigabitEthernet0/0.30
    - encapsulation dot1Q 30
    - ip address 192.168.30.1 255.255.255.0
- Enable the main interface:
  - Router(config)# interface GigabitEthernet0/0
    - no shutdown
- Save the configuration:
  - Router(config-if)# do wr

Step 5: Configure the DHCP Server
- Access the Server GUI:
  - Assign a static IP:
    - FastEthernet0 IP Address: 192.168.10.2
    - Subnet Mask: 255.255.255.0
    - Default Gateway: 192.168.10.1
- Enable DHCP service and configure pools for each VLAN:
  - VLAN 10 (Sales):
    - Pool Name: Sales
    - Default Gateway: 192.168.10.1
    - Start IP: 192.168.10.10
    - Subnet Mask: 255.255.255.0
    - Maximum Users: 20
  - VLAN 20 (IT):
    - Pool Name: IT
    - Default Gateway: 192.168.20.1
    - Start IP: 192.168.20.10
    - Subnet Mask: 255.255.255.0
    - Maximum Users: 20
  - VLAN 30 (HR):
    - Pool Name: HR
    - Default Gateway: 192.168.30.1
    - Start IP: 192.168.30.10
    - Subnet Mask: 255.255.255.0
    - Maximum Users: 20
- Click Add after each pool to save the settings.

Step 6: Configure DHCP Relay on the Router
- Access Router CLI and enter global configuration mode:
  - Router(config)# configure terminal
- Configure IP Helper on each VLAN sub-interface:
  - For VLAN 20:
    - Router(config)# interface GigabitEthernet0/0.20
      - ip helper-address 192.168.10.2
  - For VLAN 30:
    - Router(config)# interface GigabitEthernet0/0.30
      - ip helper-address 192.168.10.2
      - Router(config-subif)#exit

Step 7: Configure PCs to Obtain IP Addresses via DHCP

Configure Each PC:
- Click on each PC to open its configuration.
- Go to the Desktop tab > IP Configuration.
- Select DHCP. Each PC should automatically receive an IP address from the correct subnet range based on its VLAN.

Verify Configuration:
- From the Command Prompt on each PC, use the `ipconfig` command to verify the assigned IP address.
- Test connectivity between PCs in different VLANs using ping (e.g., `ping 192.168.20.10` from a PC in VLAN 10).

